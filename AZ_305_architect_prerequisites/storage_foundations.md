# Storage foundations

## Data and access models

| Model | Access shape | Azure direction | Architectural use |
|---|---|---|---|
| Object | Object name/key over HTTP APIs | Blob Storage | Media, documents, logs, backup objects, application data |
| Hierarchical object/data lake | Object storage plus directory semantics | Data Lake Storage Gen2 | Analytics engines, directory operations, ACL-based lake organization |
| File | Shared filesystem protocol | Azure Files | SMB/NFS shares, shared application data, file-server modernization |
| Block/disk | Blocks presented to a host | Managed disks | VM operating-system and data volumes |
| Key/value or key/attribute | Lookup by partition/key | Table Storage or Cosmos DB according to capability | Simple entities through globally distributed operational NoSQL |
| Queue | Durable asynchronous message backlog | Azure Queue Storage | Simple decoupling between application components |

## Data shape

- **Structured:** fixed relational schema, rows/columns, keys, joins, and transactions.
- **Semi-structured:** self-describing fields such as JSON/XML; the access pattern can require a document database, relational JSON, table store, or files.
- **Unstructured:** objects such as images, audio, video, documents, and raw logs.

Data shape alone does not select a service. Also evaluate query pattern, transaction/consistency requirement, protocol, latency, throughput, partitioning, durability, recovery, and cost.

## Azure Storage family

| Service | Key architectural distinction |
|---|---|
| Blob Storage | Massive object storage with access tiers, lifecycle, versioning, immutability, and redundancy choices |
| Azure Files | Managed SMB/NFS shares; preserves filesystem access model for compatible applications |
| Queue Storage | Simple asynchronous work messages; fewer broker features than Service Bus |
| Table Storage | Cost-efficient key/attribute entities with a limited query model |
| Managed disks | VM-attached block storage with workload-specific performance tiers |
| Data Lake Storage Gen2 | Blob Storage with hierarchical namespace and analytics-oriented directory/ACL semantics |

Data Lake Storage Gen2 is not a separate physical storage engine from Blob Storage. It enables hierarchical namespace capabilities on a storage account; that choice affects feature compatibility and data organization.

## Redundancy and failure scope

| Redundancy | Conceptual protection | Architectural consequence |
|---|---|---|
| LRS | Multiple copies in one physical location | Lowest cost; does not protect against datacenter/zone loss |
| ZRS | Synchronous copies across availability zones in one region | Zone resilience; no secondary region |
| GRS | Primary-region copies plus asynchronous copy to a secondary region | Regional durability with possible nonzero RPO; secondary not normally readable before failover |
| RA-GRS | GRS plus read access to secondary | Enables secondary reads; secondary remains read-only |
| GZRS | ZRS primary plus asynchronous secondary region | Zone availability plus regional durability |
| RA-GZRS | GZRS plus read access to secondary | Zone protection and secondary-region reads |

```text
Zone failure requirement
→ evaluate ZRS/GZRS or service-native zone redundancy

Regional disaster requirement
→ evaluate geo redundancy or explicit cross-region replication

Deletion/corruption/history requirement
→ backup, versioning, PITR, or immutability; redundancy alone is insufficient
```

Support varies by account type, service, region, tier, and feature. Geo replication is generally asynchronous, so it can lose unreplicated writes during a primary-region disaster.

## Security and access foundations

- Prefer Microsoft Entra identity and managed identity over shared keys where supported.
- Management permission on a storage account and permission to read its data are separate control/data-plane decisions.
- A Private Endpoint supplies private network reachability; it does not grant data authorization.
- Encryption at rest is default for Azure Storage; customer-managed keys add control and key-lifecycle responsibility.
- SAS grants delegated data access and must be restricted by resource, permission, protocol, network, and expiry as applicable.

## Cost and performance foundations

- Hotter tiers generally cost more to store and less to access; colder tiers reverse that balance and can add minimum-duration/retrieval constraints.
- Premium tiers improve latency/throughput but have service-specific feature and redundancy combinations.
- Many small files/objects can create transaction and analytics overhead even when total capacity is modest.
- Replication, cross-region reads, private endpoints, transactions, and data movement contribute to total cost.

Detailed storage selection: [Semi-structured and unstructured data](../Design_data_storage_solutions/semi_structured_and_unstructured_data.md). Detailed relational/NoSQL decisions: [Relational data](../Design_data_storage_solutions/relational_data.md). Recovery: [Business continuity](../Design_business_continuity/README.md).

Official references: [Azure storage services](https://learn.microsoft.com/en-us/training/modules/describe-azure-storage-services/4-describe-azure-storage-services), [Azure Storage redundancy](https://learn.microsoft.com/en-us/azure/storage/common/storage-redundancy), [Storage architecture design](https://learn.microsoft.com/en-us/azure/architecture/storage/storage-get-started).
