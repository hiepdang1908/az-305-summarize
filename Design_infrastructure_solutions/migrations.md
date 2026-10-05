# Migrations

## Migration is an architecture decision

```text
Strategy and measurable outcomes
        ↓
Discover inventory and dependencies
        ↓
Assess readiness, compatibility, performance, cost, recovery time objective (RTO), recovery point objective (RPO)
        ↓
Build landing zone and migration waves
        ↓
Migrate / modernize / validate / cut over
        ↓
Govern, secure, manage, optimize, decommission source
```

Do not select a tool before selecting the target architecture and migration strategy.

## Cloud Adoption Framework (CAF) focus

| CAF area | AZ-305 architecture use |
|---|---|
| Strategy | Motivations, business outcomes, financial/technical constraints |
| Plan | Digital estate, skills, dependencies, waves, backlog, ownership |
| Ready | Landing zone: identity, subscriptions, network, security, governance, operations |
| Adopt: migrate | Assess, deploy target, replicate/copy, test, cut over, decommission |
| Adopt: modernize/cloud-native | Replatform/refactor data and applications to managed services |
| Govern | Policies, cost, compliance, platform guardrails |
| Secure | Zero Trust, posture, threat protection, workload controls |
| Manage | Monitoring, reliability, operations baseline, optimization |

A landing zone should precede production migration. Moving servers into an ungoverned subscription creates future rework and risk.

### Migration framework and program terminology

The current AZ-305 Learn module still uses **Azure Migration and Modernization Program (Azure Migration Framework)** for the coordinated migration journey: assess and build a business case, prepare the landing zone, migrate in controlled waves, then govern and optimize. Treat that as a framework/program context, not as a migration tool.

Microsoft's current customer-engagement offering is **Azure Accelerate**, which brings together Azure Migrate and Modernize, Azure Innovate, partner expertise, and Cloud Accelerate Factory assistance. Program branding does not change tool selection: Azure Migrate discovers/assesses and migrates supported servers, Database Migration Service handles supported database paths, and Data Box addresses offline bulk transfer.

## Migration strategies

| Strategy | Change | Speed | Cloud benefit | Choose when |
|---|---|---|---|---|
| Rehost | Move largely unchanged to infrastructure as a service (IaaS) | Fast | Low initially | Deadline/compatibility dominates; modernization later |
| Replatform | Limited changes to managed platform | Medium | Medium/high | Platform as a service (PaaS) compatibility with acceptable remediation |
| Refactor/rearchitect | Redesign code/data | Slowest | Highest potential | Scale, resilience, agility, or cost require architectural change |
| Rebuild/cloud-native | Create a new implementation around required capabilities | Slow; new delivery lifecycle | High when legacy constraints are intentionally removed | Existing implementation cannot economically meet target requirements |
| Repurchase/replace | Adopt software as a service (SaaS)/product | Varies | Transfers operations | Commodity capability and process change are acceptable |
| Retain | Keep in place | None now | None | Blocked by dependency, compliance, cost, or timing |
| Retire | Decommission | Fast after validation | Removes cost/risk | Workload no longer provides value |

Some frameworks also use "relocate" for platform moves with minimal workload change. Use the organization's defined taxonomy consistently.

## Discovery and assessment

Inventory:

- Servers/VMs, OS, CPU, memory, disks, utilization, support status
- Databases, versions, features, compatibility, size, change rate
- Applications, owners, business criticality, users, authentication, certificates
- Network flows, DNS, IP dependencies, latency, bandwidth, firewalls
- File shares/object stores, protocols, ACLs, change rate
- Backup, RTO/RPO, maintenance windows, licensing, compliance

Dependency analysis prevents moving an application while leaving a latency-sensitive database, authentication service, file share, or hard-coded IP behind. Group tightly coupled components into migration waves.

Assessment outputs should include target recommendation, sizing, readiness issues, cost estimate, remediation, downtime model, test plan, and rollback criteria. Peak/seasonal measurements and license benefits matter; short samples can under-size a target.

## Tool selection matrix

| Need | Recommended starting point | Notes |
|---|---|---|
| Discover/assess servers and dependencies | Azure Migrate discovery and assessment | Appliance/agentless or supported discovery depends on source |
| Replicate supported VMware/Hyper-V/physical servers to Azure VMs | Azure Migrate: Server Migration | Validate target, test migration, cutover, then stop source replication |
| Existing servers already protected by Site Recovery | Continue Azure Site Recovery (ASR) replication only when changing tools adds unjustified risk; still use Azure Migrate assessment where useful | For a new server migration, prefer purpose-built Azure Migrate; ASR's primary role is disaster recovery |
| SQL discovery/assessment/target recommendation | Azure Migrate and current Azure SQL assessment experiences | Use current Database Migration Service (DMS)/Azure Arc tooling as documented for source/target |
| Online/offline supported database migration | Azure Database Migration Service or integrated database-specific migration service | Support matrix changes; validate engine, version, online/generally available (GA) status |
| Small one-time object/file copy | AzCopy or Storage Explorer | Client-driven; scripting/operations owned by customer |
| Managed online file/folder migration at scale | Azure Storage Mover | Supports documented Server Message Block (SMB)/Network File System (NFS)/Amazon Web Services (AWS) S3 source-target combinations; agent/connectivity may be required |
| Windows file server identity/share migration | Storage Migration Service | Windows Server tool; can target Azure VMs/Azure Files patterns |
| Continuous cache/namespace for file-server transition | Azure File Sync | Synchronization/tiering, not a universal bulk-migration replacement |
| Offline bulk data transfer with constrained bandwidth | Azure Data Box family | Device/order/region/capacity/security constraints; allow shipping/import time |
| Online bulk transfer over dedicated link | Storage Mover/AzCopy over ExpressRoute or internet as architecture requires | ExpressRoute does not make copy free or remove throughput limits |

Use the [Azure Storage migration tools selection guide](https://learn.microsoft.com/en-us/azure/storage/common/storage-migration-tools) for current source-target support.

## Server migration

```text
Discover → assess/right-size → remediate → landing-zone readiness
         → replicate → isolated test migration → delta sync
         → change freeze → cut over → validate → decommission
```

Architecture decisions:

- Rehost target VM size, disk tiers, zones, availability, licensing, backup, monitoring
- Network address plan, DNS, hybrid routing, firewall, egress, private endpoints
- Identity/domain dependency and time synchronization
- Downtime and data consistency during final cutover
- Rollback point and criteria
- Modernization follow-up; do not leave rehosted technical debt unowned

Test migration must use an isolated or controlled network to avoid duplicate machine identities, writes, scheduled jobs, or production integrations.

## Application migration

| Source characteristic | Target direction |
|---|---|
| Supported web runtime, minimal platform dependency | App Service |
| Containerized HTTP/event microservice without Kubernetes need | Container Apps |
| Kubernetes requirement | AKS |
| Event-triggered code | Functions |
| Vendor/OS dependency | VM |
| Workflow/connectors | Logic Apps |

Assess session state, filesystem writes, background jobs, authentication, certificates, outbound IP assumptions, local dependencies, and configuration. A web application that writes local disk or stores in-memory session may not scale safely on PaaS until state is externalized.

## Database migration

```text
Compatibility-first target selection:

Full OS/instance control required       → SQL Server on Azure VM
High instance compatibility, managed   → SQL Managed Instance
Database-scoped cloud-native target     → Azure SQL Database
PostgreSQL/MySQL engine requirement     → corresponding Flexible Server
Global NoSQL access pattern             → evaluate Cosmos DB (rearchitecture)
```

Assessment must cover engine/version, unsupported features, instance dependencies, SQL Agent jobs, linked servers, logins, cross-database access, collation, extensions, performance, and downtime.

| Migration mode | Downtime | Choose when |
|---|---|---|
| Offline | Downtime includes copy/restore | Database is small or outage window is sufficient |
| Online | Continuous sync followed by brief cutover | Downtime must be minimized and source-target pair supports it |

Online migration reduces cutover downtime but increases setup, network, synchronization, and operational complexity. Always validate data, security principals, jobs, performance, high availability (HA)/disaster recovery (DR), and backup on the target before final acceptance.

## Unstructured data migration

Selection factors:

- Total bytes and file/object count; many small files behave differently from large objects
- Change rate and allowed freeze window
- Network bandwidth, latency, proxy/firewall, egress cost
- SMB/NFS/object semantics, timestamps, ACLs, links, sparse files
- Target Blob/Azure Data Lake Storage (ADLS)/Azure Files compatibility
- Encryption and chain of custody
- Incremental catch-up, checksum/validation, namespace cutover

For very large datasets with limited bandwidth, seed using Data Box and use Azure Storage Mover for online catch-up when the source-target combination supports it. The offline device shortens network transfer but adds ordering/shipping/import lead time.

## Migration wave design

Prioritize low-risk, high-learning workloads before tightly coupled critical systems. A wave contains applications and dependencies that can move and validate together.

For each wave define:

- Entry criteria and owners
- Target architecture and capacity
- Test migration and acceptance evidence
- Change freeze and final synchronization
- Cutover order and DNS/connection changes
- Business validation
- Rollback decision point
- Hypercare, cost/performance tuning
- Source shutdown and license/backup cleanup

## Cost and risk

- Dual-running source/target, replication, test environments, transfer, licenses, and retained backups are migration costs.
- Right-size from measured utilization and account for peak/headroom.
- Azure Hybrid Benefit/reservations can reduce steady cost after sizing stabilizes.
- PaaS can reduce operations but migration/remediation effort can be substantial.
- A faster rehost may be lower project cost but higher long-term operating cost.
- Do not decommission until backup, monitoring, security, performance, and business acceptance are verified.

## Common Trap

- Azure Migrate assessment is not the same as migration execution.
- Azure Site Recovery is primarily a DR service; do not select it over Azure Migrate for a new server migration merely because both replicate machines.
- Online migration minimizes downtime; it does not guarantee zero downtime.
- Data Box is an offline bulk-transfer tool, not continuous synchronization.
- AzCopy moves data but does not assess application compatibility or recreate full file-server behavior.
- Rehost preserves compatibility and technical debt.
- Target PaaS selection must precede tool selection.
- Ignore hard-coded IP/DNS, identity, or latency dependencies and the migration wave will fail despite successful copying.

Official references: [Azure Migrate overview](https://learn.microsoft.com/en-us/azure/migrate/migrate-services-overview), [Azure Migrate versus Site Recovery](https://learn.microsoft.com/en-us/azure/site-recovery/migrate-overview), [Cloud Adoption Framework migration](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/migrate/), [Azure Database Migration Service](https://learn.microsoft.com/en-us/azure/dms/dms-overview), [Storage Mover overview](https://learn.microsoft.com/en-us/azure/storage-mover/service-overview), [Data Box overview](https://learn.microsoft.com/en-us/azure/databox/data-box-overview), [Azure SQL migration guides](https://learn.microsoft.com/en-us/data-migration/).

---

## Navigation

| Previous | Home | Next |
|---|---|---|
| [← Application architecture](application_architecture.md) | [Domain home](README.md) | [Networking →](networking.md) |
