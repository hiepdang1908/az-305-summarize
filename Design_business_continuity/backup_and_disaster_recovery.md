# Backup and disaster recovery

## Start with business objectives

| Measure | Architecture question |
|---|---|
| Recovery time objective (RTO) | How long may the business service be unavailable? |
| Recovery point objective (RPO) | How much committed data may be lost? |
| Retention | Which historical recovery points must remain, and for how long? |
| Recovery scope | Item, database, virtual machine (VM), application, zone, region, or site? |
| Consistency | Crash-consistent, file-system-consistent, or application-consistent? |
| Recovery sequence | Which identity, network, data, and app dependencies start first? |
| Compliance | Where may recovery data reside, and must it be immutable? |

Define RTO/RPO per workload tier and for the whole service. A database recovering in minutes does not meet a 15-minute workload RTO if DNS, secrets, compute, or validation takes an hour.

## Failure-to-control matrix

| Failure | Primary controls | Why another control is insufficient |
|---|---|---|
| VM host failure | Multiple instances, zones/sets, load balancing | Backup restore is too slow for high availability (HA) |
| Availability-zone failure | Zone-redundant service or active instances across zones | Availability set does not span zones |
| Regional failure | Secondary-region deployment/data copy + failover orchestration | Zones remain inside one region |
| Accidental deletion | Soft delete, backup, versioning, point-in-time restore (PITR) | Replication may copy deletion |
| Data corruption | Historical backup/version/PITR, isolated validation | Active replica may receive corruption |
| Ransomware/credential compromise | Immutable/isolated backups, separate authorization, monitoring, recovery account | Online writable replicas can be encrypted/deleted too |
| Database failure | Service-native HA/replica and database-aware backup | VM replication may not provide required transaction RPO |
| On-premises site loss | Azure Site Recovery or workload-native replication plus Azure recovery environment | Local backup alone can share the site failure |

## Azure Backup versus Azure Site Recovery

| Dimension | Azure Backup | Azure Site Recovery (ASR) |
|---|---|---|
| Purpose | Historical data/workload recovery | Workload continuity through replication and failover |
| Typical object | VM, disk, file share, database/workload, blob where supported | Supported Azure VMs, VMware/physical servers, and replication scenarios |
| Recovery shape | Restore point to original/new target | Start replicated workload at recovery site/region |
| Best for | Deletion, corruption, retention, ransomware recovery | Site/region outage and migration-like failover |
| RTO direction | Restore-dependent; usually longer | Lower through pre-replicated state |
| RPO direction | Backup frequency/snapshot policy | Replication lag and workload behavior |
| Testing | Restore test | Test failover in isolated network |
| Does not replace | HA or live disaster recovery (DR) | Historical backup |

Use both when the workload needs rapid failover and historical recovery.

### Recovery mechanism comparisons

| Compare | First mechanism | Second mechanism | Decision rule |
|---|---|---|---|
| Snapshot vs backup | A snapshot is a point-in-time copy normally tied closely to the source service/account and useful for fast rollback/restore | A managed backup adds policy, retention, vault/isolation, monitoring, and recovery workflows according to workload support | Use snapshots for a supported fast recovery layer; use backup when independent lifecycle, history, and governed recovery are required |
| Geo-redundancy vs backup | Geo-redundancy maintains infrastructure copies, often asynchronously | Backup preserves recoverable historical points according to policy | Geo copies address failure scope/durability; they can reproduce deletion or corruption and do not replace history |
| Replication vs backup | Replication keeps a current/warm copy for continuity and lower RTO | Backup keeps older recovery points for rollback and retention | Use replication for failover and backup for deletion, corruption, attack, and historical recovery |

The exact independence of a snapshot or backup is service-specific. Validate account/vault isolation, immutability, authorization, and whether deletion of the source can affect recovery points.

## Vault and protection design

Azure backup capabilities use vault resources according to workload: Recovery Services vault and Backup vault. Support differs by data source. Select the vault type from the protected workload rather than assuming they are interchangeable.

Design considerations:

- Vault and protected resource region/subscription support
- Azure role-based access control (Azure RBAC) separation between workload administrators and backup operators
- Soft delete, immutability, multi-user authorization, and resource guard features where supported
- Vault redundancy and cross-region restore requirements
- Private endpoint support and network prerequisites
- Policy frequency/retention aligned to RPO and compliance
- Application consistency and pre/post scripts for supported workloads
- Restore location, network, keys, identities, and capacity
- Cost of protected instances, retained recovery points, snapshots, and cross-region copies

Do not place backup deletion authority in the same unrestricted operator path as production. Recovery credentials and procedures must survive a tenant/subscription/security incident within the documented threat model.

## Compute backup and recovery

| Requirement | Direction |
|---|---|
| Restore Azure VM after deletion/corruption | Azure VM Backup from vault recovery point |
| Fast disk snapshot restore | Instant restore/snapshot capabilities where supported |
| Region/site failover | ASR, workload-native replication, or rebuild from code/data |
| Stateless tier | Prefer redeployment from immutable image/infrastructure as code (IaC); back up state, not disposable instances |
| Application-consistent VM recovery | Validate agent/extension and application writer support |

VM backup protects VM state; it does not automatically create a multi-tier application-consistent recovery plan. For databases, use database-aware protection when transaction-level RPO/restore is required.

## Database backup and recovery

| Workload | Direction |
|---|---|
| Azure SQL Database/Managed Instance | Automated backups, point-in-time restore, deleted-database restore, long-term retention, geo-restore as supported |
| SQL Server on Azure VM | Automated Backup/SQL infrastructure as a service (IaaS) extension, Azure Backup workload protection, or SQL-native strategy according to control needs |
| PostgreSQL/MySQL flexible server | Service-native automated backups and PITR; geo-redundant backup/replica features as supported |
| Cosmos DB | Continuous or periodic backup mode according to recovery granularity, cost, and feature compatibility |

Cross-region active replicas support DR/read scale; backup supports historical recovery. Test the actual restore workflow, including server configuration, users/logins, keys, connection strings, and application validation.

## Unstructured data protection

| Data | Protection choices |
|---|---|
| Block blobs | Versioning, soft delete, container soft delete, PITR where supported, immutability, operational/vaulted backup, object replication |
| Azure Files | Share snapshots, soft delete, Azure Files backup, redundancy |
| Azure Data Lake Storage Gen2 (ADLS Gen2) | Blob protection features subject to hierarchical namespace (HNS) compatibility; validate every feature combination |
| Managed disks | Snapshots, incremental snapshots, Azure Disk Backup, VM Backup |

Blob operational backup uses continuous capabilities for fast operational recovery, while vaulted backup provides an isolated copy/longer-term protection model where supported. Product support evolves; verify account type, redundancy, region, and HNS compatibility.

## Azure Site Recovery design

```text
Source workload
    ↓ continuous replication
Recovery region/site storage and configuration
    ↓ recovery plan / ordered groups / automation
Test failover → validate without production impact
    ↓ declared event
Planned or unplanned failover → commit → reprotect → failback
```

Architectural requirements:

- Supported source/target scenario and region pairing are not assumptions; validate them.
- Pre-create or map target network, subnets, NSGs, DNS, load balancers, IP behavior, and capacity.
- Ensure identity, Key Vault, certificates, private DNS, and dependent platform as a service (PaaS) data are available.
- Use recovery plans for ordering and automation, but test scripts and permissions.
- ASR is VM-aware, not necessarily application/transaction-aware. Database-native replication may be required.
- Test failover regularly in an isolated network. A successful replication status is not proof of recoverability.

## Active-active versus active-passive DR

| Model | RTO/RPO direction | Cost | Complexity | Choose when |
|---|---|---|---|---|
| Backup and rebuild | Longest | Lowest | Lower steady-state, high recovery risk | Noncritical or easily reconstructed workload |
| Pilot light | Longer | Low | Core data/services kept ready | Moderate RTO and cost sensitivity |
| Warm standby | Moderate/low | Medium | Scaled-down secondary and tested failover | Faster recovery without full duplicate capacity |
| Active-passive | Low | High | Secondary ready, routing/failover required | Predictable fast regional recovery |
| Active-active | Lowest potential RTO | Highest | Data consistency, conflict, session, routing, deployment complexity | Business requires multi-region serving/failover |

Active-active compute is useless if the database remains single-region or cannot support the write/consistency pattern.

## Recovery runbook

1. Detect and classify the incident; avoid failing over for a local/transient problem.
2. Establish authority to declare disaster.
3. Freeze or fence the old primary where split brain is possible.
4. Recover identity, keys, connectivity, DNS, and data dependencies.
5. Promote/fail over data according to RPO decision.
6. Start stateless and stateful tiers in dependency order.
7. Change routing, validate health and business transactions.
8. Communicate achieved data point and limitations.
9. Reprotect the new primary and plan controlled failback.

## Common Trap

- Replication is not backup.
- A vault policy is not proof a restore will work.
- ASR does not automatically meet database transaction RPO.
- Geo-redundant storage does not mean the secondary is writable or instantly failed over.
- Cross-region failover may be manual and asynchronous even when local HA is automatic.
- Recovery plans must include DNS, identity, secrets, network, quotas, and operational authority.

Official references: [Azure Backup architecture](https://learn.microsoft.com/en-us/azure/backup/backup-architecture), [Azure Site Recovery overview](https://learn.microsoft.com/en-us/azure/site-recovery/site-recovery-overview), [Blob data protection](https://learn.microsoft.com/en-us/azure/storage/blobs/data-protection-overview), [Azure SQL business continuity](https://learn.microsoft.com/en-us/azure/azure-sql/database/business-continuity-high-availability-disaster-recover-hadr-overview), [Reliability design principles](https://learn.microsoft.com/en-us/azure/well-architected/reliability/principles).

---

## Navigation

| Previous | Home | Next |
|---|---|---|
| [← Business continuity solutions](README.md) | [Domain home](README.md) | [High availability →](high_availability.md) |
