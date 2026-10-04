# Semi-structured and unstructured data

Key storage terms: Azure Data Lake Storage Gen2 (ADLS Gen2), hierarchical namespace (HNS), access control list (ACL), input/output operations per second (IOPS), recovery time objective (RTO), recovery point objective (RPO), high availability (HA), and disaster recovery (DR).

## Storage decision matrix

| Service | Access/model | Hierarchy | Best fit | Scale/performance direction | Protection considerations |
|---|---|---|---|---|---|
| Blob Storage | Object Representational State Transfer (REST)/software development kit (SDK); block, append, page blobs | Flat namespace with virtual folders | Images, media, backup, logs, objects | Massive object scale; hot/cool/cold/archive and premium options | Redundancy, versioning, soft delete, immutability, backup |
| Data Lake Storage Gen2 | Blob APIs plus hierarchical namespace and distributed file system (DFS) endpoint | Real directories/atomic directory operations | Analytics lake, big-data frameworks, directory ACLs | Analytics-optimized namespace; lifecycle tiers | Same storage redundancy plus role-based access control (RBAC)/ACL design |
| Azure Files | Managed Server Message Block (SMB)/Network File System (NFS) shares and REST | File/share directories | Lift-and-shift file shares, shared app data, user profiles | Standard/premium; protocol and share-type constraints | Snapshots, soft delete, Azure Backup, redundancy |
| Managed disks | Block storage attached to Azure VMs | Disk/block | OS and data disks for VMs | Disk tier/size/IOPS/throughput choices | Snapshots, backup, zone/region design |
| Table Storage | Key/attribute entities over REST | Partition/row keys | Simple, high-scale key-value/attribute data | Efficient key access; limited query model | Account redundancy and application data model |
| Cosmos DB | API-specific NoSQL database | Logical partitions/containers | Globally distributed semi-structured operational data | Tunable consistency, request units (RU)/serverless, global distribution | Continuous/periodic backup options and multi-region design |

```text
Object storage → Blob Storage
Analytics lake + filesystem semantics → Data Lake Storage Gen2
SMB/NFS shared filesystem → Azure Files
VM block device → managed disk
Simple key/attribute store → Table Storage
Global operational NoSQL → Cosmos DB
```

## Semi-structured data choices

JSON/XML does not identify one service by itself. Choose from access and transaction requirements.

| Requirement | Direction |
|---|---|
| Globally distributed operational JSON with indexed queries and tunable consistency | Cosmos DB |
| Simple key/attribute entities addressed mainly by partition and row key | Table Storage |
| Files/objects retained and processed by applications or analytics engines | Blob Storage or ADLS Gen2 |
| Relational transactions and joins over JSON fields | Relational database with supported JSON features |
| Append/replay telemetry stream | Event Hubs for ingestion plus durable analytical/operational store |

Cosmos DB needs a partition-key, consistency, throughput, and backup design; see [Cosmos DB and Table Storage](relational_data.md#cosmos-db-and-table-storage). Blob Storage holds JSON objects but does not become an operational document database merely because the object format is JSON.

## Blob types and tiers

| Choice | Use |
|---|---|
| Block blob | General object data and streaming/uploaded files |
| Append blob | Append-oriented logging scenarios |
| Page blob | Random read/write pages; underlying pattern used for virtual hard disk (VHD) scenarios |
| Hot | Frequent access; higher storage/lower access cost relative to colder tiers |
| Cool | Infrequent but online data |
| Cold | Less frequent but still online data |
| Archive | Lowest storage cost, offline retrieval with rehydration delay and feature constraints |

Choose tier from total cost: storage, transactions, retrieval, minimum-duration/early-deletion charges, rehydration time, and required redundancy. Use lifecycle management to tier or expire data, but test filters and protect regulated/critical data from unintended deletion.

## Azure Files decisions

| Requirement | Direction |
|---|---|
| Existing SMB application with minimal change | Azure Files SMB |
| Linux/Unix NFS semantics | Azure Files NFS where protocol/tier/network requirements are met |
| Local cache and cloud tiering for Windows file servers | Azure File Sync |
| Consistently high IOPS/low latency | Premium file shares where supported |
| Identity-based SMB authorization | Active Directory Domain Services (AD DS), Microsoft Entra Domain Services, or supported Microsoft Entra Kerberos option according to client/workload requirements |

Azure File Sync is synchronization/caching, not by itself a complete backup. A change or deletion can synchronize. Pair it with snapshots/backup and recovery controls.

## Data Lake Storage Gen2

HNS adds directory semantics, atomic directory operations, and POSIX-like ACL support to Blob Storage. Enable it when analytics engines need those semantics. HNS is an account-level architectural choice with feature-interaction and migration implications; validate compatibility before enabling or converting an existing account.

Authorization can combine Azure RBAC at broader scopes and ACLs for directories/files. Effective access design must account for execute permission on the directory path and avoid unmanageable per-file ACL sprawl.

## Managed disks

| Requirement | Direction |
|---|---|
| General production VM disk | Premium solid-state drive (SSD) variants or Standard SSD according to latency/IOPS needs |
| Highest IOPS/throughput and adjustable performance | Ultra Disk or Premium SSD v2 where supported |
| Low-cost, latency-tolerant workload | Standard hard disk drive (HDD)/SSD according to workload |
| Zone-resilient VM design | Place zonal disks with VMs or use supported zone-redundant disk options according to architecture |
| Shared block storage for clustered application | Shared managed disk only for supported clustering scenarios; it does not itself create application HA |

Disk snapshots are point-in-time copies but application consistency, orchestration, retention, and restore testing still matter. Azure Backup provides policy/vault operations beyond ad hoc snapshots.

## Storage redundancy

| Option | Primary-region copies | Secondary region | Secondary readable before failover | Protects against |
|---|---|---|---|---|
| Locally redundant storage (LRS) | Within one physical location | No | No | Drive/rack-level failures; not datacenter loss |
| Zone-redundant storage (ZRS) | Synchronous across zones | No | N/A | Zone/datacenter failure in the region |
| Geo-redundant storage (GRS) | LRS primary, asynchronous geo-copy | Yes | No unless converted/configured as read access (RA) | Regional disaster after failover, subject to replication RPO |
| Read-access geo-redundant storage (RA-GRS) | Same as GRS | Yes | Read-only | Reads from secondary plus geo durability |
| Geo-zone-redundant storage (GZRS) | ZRS primary, asynchronous geo-copy | Yes | No unless RA | Zone resilience plus regional disaster protection |
| Read-access geo-zone-redundant storage (RA-GZRS) | Same as GZRS | Yes | Read-only | Zone resilience plus secondary-region reads |

Important constraints:

- Geo replication is asynchronous; regional loss can lose writes not yet replicated.
- GRS/GZRS secondary is not writable and not normally readable until failover unless RA is selected.
- A customer-initiated account failover changes the primary and can lose unreplicated data.
- Support differs by account type, region, service, and feature. Archive and some conversion combinations are restricted.
- Redundancy protects infrastructure copies; it does not preserve history after logical deletion/corruption.

## Data protection layers

| Failure/requirement | Control |
|---|---|
| Accidental deletion | Soft delete for supported blobs/containers/file shares |
| Accidental overwrite | Blob versioning, snapshots, application versioning |
| Point-in-time rollback | Point-in-time restore for supported block blob accounts/configurations |
| Regulatory write once, read many (WORM)/ransomware resistance | Time-based immutability or legal hold; locked policies where required |
| Operational backup policy | Azure Backup for supported workloads including blobs/files/disks/VMs |
| Regional copy | Geo redundancy, object replication, or workload-specific copy design |
| Credential compromise | Entra authorization, least privilege, disable shared key where feasible, scoped user-delegation shared access signature (SAS) |

Object replication copies block blobs asynchronously between accounts and can support deliberate source/destination choices. It is replication, not a historical backup. Versioning and change feed prerequisites/behavior must be validated.

## Storage security

Preference order where supported:

1. Microsoft Entra identity with least-privilege data-plane RBAC.
2. Managed identity for workloads.
3. User delegation SAS for bounded delegation.
4. Service/account SAS only when required and tightly scoped.
5. Shared account keys only as a legacy compatibility fallback.

Use HTTPS, encryption at rest, customer-managed keys only when key-control requirements justify lifecycle complexity, and private endpoints when private IP access/isolation is mandatory. A service endpoint keeps the service's public endpoint but identifies/restricts the VNet subnet; it does not give the service a private IP.

## Performance and cost decisions

- Separate accounts when workloads need different region, redundancy, performance, security, ownership, or failure boundaries.
- Do not create an account per object or combine all organizational data into one account without assessing quotas and blast radius.
- Premium performance reduces latency but costs more and has service-specific feature combinations.
- Small-file analytics can be inefficient; compact/partition data according to query patterns.
- Replication adds capacity and data-transfer cost; private endpoints add DNS and network operations.
- Lifecycle tiering lowers capacity cost but can increase retrieval cost and recovery time.

## When not to choose

- Do not use Azure Files when object semantics and massive web-scale object access are desired.
- Do not use Blob Storage as a drop-in SMB share.
- Do not enable HNS only for visual folders; use it for analytics/file-system semantics.
- Do not use managed disks as shared object/file storage.
- Do not choose Archive for data with short RTO.
- Do not treat RA-GRS readable secondary as a writable active-active store.

## Common Trap

- ZRS is zone HA, not regional DR.
- GRS is regional durability, but the secondary is not automatically an active writable endpoint.
- Replication is not backup; deletion/corruption can replicate.
- Soft delete and versioning address different recovery cases.
- Private Endpoint and service endpoint provide different network models.
- Storage account firewall permissions do not grant data authorization.

Official references: [Storage account overview](https://learn.microsoft.com/en-us/azure/storage/common/storage-account-overview), [Azure Storage redundancy](https://learn.microsoft.com/en-us/azure/storage/common/storage-redundancy), [Blob access tiers](https://learn.microsoft.com/en-us/azure/storage/blobs/access-tiers-overview), [Data Lake Storage Gen2](https://learn.microsoft.com/en-us/azure/storage/blobs/data-lake-storage-introduction), [Azure Files planning](https://learn.microsoft.com/en-us/azure/storage/files/storage-files-planning), [Blob data protection](https://learn.microsoft.com/en-us/azure/storage/blobs/data-protection-overview).

---

## Navigation

| Previous | Home | Next |
|---|---|---|
| [← Relational data](relational_data.md) | [Domain home](README.md) | [Data integration and analytics →](data_integration_and_analytics.md) |
