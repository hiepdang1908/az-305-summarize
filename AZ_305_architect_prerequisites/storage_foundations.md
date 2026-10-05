# Storage foundations

## Data and access models

| Model | Access shape | Azure direction | Architectural use |
|---|---|---|---|
| Object | Object name/key over Hypertext Transfer Protocol (HTTP) APIs | Blob Storage | Media, documents, logs, backup objects, application data |
| Hierarchical object/data lake | Object storage plus directory semantics | Data Lake Storage Gen2 | Analytics engines, directory operations, access control list (ACL)-based lake organization |
| File | Shared filesystem protocol | Azure Files | Server Message Block (SMB)/Network File System (NFS) shares, shared application data, file-server modernization |
| Block/disk | Blocks presented to a host | Managed disks | Virtual machine (VM) operating-system and data volumes |
| Key/value or key/attribute | Lookup by partition/key | Table Storage or Cosmos DB according to capability | Simple entities through globally distributed operational NoSQL |
| Queue | Durable asynchronous message backlog | Azure Queue Storage | Simple decoupling between application components |

## Data shape

- **Structured:** fixed relational schema, rows/columns, keys, joins, and transactions.
- **Semi-structured:** self-describing fields such as JavaScript Object Notation (JSON) or Extensible Markup Language (XML); the access pattern can require a document database, relational JSON, table store, or files.
- **Unstructured:** objects such as images, audio, video, documents, and raw logs.

Data shape alone does not select a service. Also evaluate query pattern, transaction/consistency requirement, protocol, latency, throughput, partitioning, durability, recovery, and cost.

## Azure Storage family

An Azure Storage account is a management, security, endpoint, and redundancy boundary for supported storage services. Account kind, region, performance tier, namespace features, and redundancy selection constrain which capabilities can be combined; verify the required feature set before choosing the account configuration.

| Account option | Services / characteristic | Starting use |
|---|---|---|
| Standard general-purpose v2 | Blob (including Data Lake Storage), Files, Queue, and Table; broad standard redundancy choices | Default candidate for most standard Azure Storage workloads |
| Premium block blobs | Blob workloads with high transaction rates or consistently low storage latency; LRS or ZRS options | Performance-sensitive object workloads |
| Premium file shares | Azure Files with high-scale/performance characteristics; SMB/NFS and LRS/ZRS availability depend on supported configuration | Performance-sensitive managed file shares |
| Premium page blobs | Page blobs only; LRS | Specialized page-blob scenarios |

Account type and redundancy are coupled: do not assume every service/account option supports every performance tier or replication choice.

| Service | Key architectural distinction |
|---|---|
| Blob Storage | Massive object storage with access tiers, lifecycle, versioning, immutability, and redundancy choices |
| Azure Files | Managed SMB/NFS shares; preserves filesystem access model for compatible applications |
| Queue Storage | Simple asynchronous work messages; fewer broker features than Service Bus |
| Table Storage | Cost-efficient key/attribute entities with a limited query model |
| Managed disks | VM-attached block storage with workload-specific performance tiers |
| Azure Data Lake Storage Gen2 (ADLS Gen2) | Blob Storage with hierarchical namespace (HNS) and analytics-oriented directory/access control list (ACL) semantics |

Data Lake Storage Gen2 is not a separate physical storage engine from Blob Storage. It enables hierarchical namespace capabilities on a storage account; that choice affects feature compatibility and data organization.

## Moving and migrating data

These tools solve different movement problems; select by volume, source/target, ongoing synchronization, and available bandwidth.

| Tool/service | Appropriate use | Boundary |
|---|---|---|
| AzCopy | Scripted upload, download, or copy of files/blobs, including between accounts | `sync` is source-to-destination, not bidirectional synchronization; validate object/file semantics and permissions |
| Azure Storage Explorer | Cross-platform graphical browsing and movement of Azure Storage data | A client tool for individual or small-group operations, not a migration assessment service |
| Azure File Sync | Keep Windows Server file shares synchronized with Azure Files; optional cloud tiering/cache | Synchronization is not backup; deletion/change can propagate |
| Azure Migrate | Assess and coordinate supported workload/infrastructure migration paths | A migration hub and tool set, not a general-purpose file-copy utility |
| Azure Data Box | Offline transfer when datasets are very large and online bandwidth/time is insufficient | Physical shipping/import adds lead time; not continuous synchronization |

Choose online transfer when network capacity and cutover window are acceptable. Consider an offline Data Box seed for bulk data with constrained bandwidth, then plan any required online delta/cutover separately. Validate current source-target support and data validation requirements before selecting a tool.

## Redundancy and failure scope

| Redundancy | Conceptual protection | Architectural consequence |
|---|---|---|
| Locally redundant storage (LRS) | Three copies within one datacenter in the primary region | Lowest-cost redundancy option; does not protect against datacenter/zone loss |
| Zone-redundant storage (ZRS) | Synchronous copies across availability zones in one region | Zone resilience; no secondary region |
| Geo-redundant storage (GRS) | Primary-region copies plus asynchronous copy to a secondary region | Regional durability with possible nonzero recovery point objective (RPO); secondary not normally readable before failover |
| Read-access geo-redundant storage (RA-GRS) | GRS plus read access to secondary | Enables secondary reads; secondary remains read-only |
| Geo-zone-redundant storage (GZRS) | ZRS primary plus asynchronous secondary region | Zone availability plus regional durability |
| Read-access geo-zone-redundant storage (RA-GZRS) | GZRS plus read access to secondary | Zone protection and secondary-region reads |

```text
Zone failure requirement
→ evaluate ZRS/GZRS or service-native zone redundancy

Regional disaster requirement
→ evaluate geo redundancy or explicit cross-region replication

Deletion/corruption/history requirement
→ backup, versioning, point-in-time restore (PITR), or immutability; redundancy alone is insufficient
```

Support varies by account type, service, region, tier, and feature. Geo replication is generally asynchronous, so it can lose unreplicated writes during a primary-region disaster.

## Security and access foundations

- Prefer Microsoft Entra identity and managed identity over shared keys where supported.
- Management permission on a storage account and permission to read its data are separate control/data-plane decisions.
- A Private Endpoint supplies private network reachability; it does not grant data authorization.
- Encryption at rest is default for Azure Storage; customer-managed keys add control and key-lifecycle responsibility.
- A shared access signature (SAS) grants delegated data access and must be restricted by resource, permission, protocol, network, and expiry as applicable.

## Cost and performance foundations

- Hotter tiers generally cost more to store and less to access; colder tiers reverse that balance and can add minimum-duration/retrieval constraints.
- Premium tiers improve latency/throughput but have service-specific feature and redundancy combinations.
- Many small files/objects can create transaction and analytics overhead even when total capacity is modest.
- Replication, cross-region reads, private endpoints, transactions, and data movement contribute to total cost.

Detailed storage selection: [Semi-structured and unstructured data](../Design_data_storage_solutions/semi_structured_and_unstructured_data.md). Detailed relational/NoSQL decisions: [Relational data](../Design_data_storage_solutions/relational_data.md). Recovery: [Business continuity](../Design_business_continuity/README.md).

Official references: [Azure storage accounts](https://learn.microsoft.com/en-us/training/modules/describe-azure-storage-services/2-accounts), [Azure Storage redundancy](https://learn.microsoft.com/en-us/training/modules/describe-azure-storage-services/3-redundancy), [Azure storage services](https://learn.microsoft.com/en-us/training/modules/describe-azure-storage-services/4-describe-azure-storage-services), [Data migration options](https://learn.microsoft.com/en-us/training/modules/describe-azure-storage-services/6-identify-azure-data-migration-options), [File movement options](https://learn.microsoft.com/en-us/training/modules/describe-azure-storage-services/7-identify-azure-file-movement-options), [Storage architecture design](https://learn.microsoft.com/en-us/azure/architecture/storage/storage-get-started).

---

## Navigation

| Previous | Home | Next |
|---|---|---|
| [← Compute foundations](compute_foundations.md) | [Prerequisites home](README.md) | [Identity, access, and security foundations →](identity_access_security_foundations.md) |
