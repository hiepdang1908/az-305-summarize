# Relational data

## Relational service decision matrix

| Dimension | Azure SQL Database | Azure SQL Managed Instance | SQL Server on Azure VM | Azure Database for PostgreSQL flexible server | Azure Database for MySQL flexible server |
|---|---|---|---|---|---|
| Model | Managed database PaaS | Managed instance PaaS | IaaS SQL Server | Managed PostgreSQL PaaS | Managed MySQL PaaS |
| Customer responsibility | Schema, queries, indexes, logical security | Same, plus instance-level design | OS, SQL installation/configuration, patching strategy, HA/DR, backup design | Schema, queries, logical security | Schema, queries, logical security |
| Compatibility | Database-scoped SQL Server compatibility; some instance features differ | High SQL Server instance compatibility | Maximum SQL Server and OS compatibility | PostgreSQL applications/extensions subject to support list | MySQL applications/features subject to support list |
| Instance-level features | Limited by database/logical-server model | Strong support for SQL Agent, cross-database and instance features | Full control | PostgreSQL-native managed model | MySQL-native managed model |
| OS/file-system access | No | No | Yes | No | No |
| Scaling | vCore/DTU, provisioned/serverless; elastic pools; Hyperscale for applicable workloads | Scale compute/storage within service constraints; instance pools where applicable | Resize/add VMs and storage; application/SQL design owns scale-out | Vertical scaling, storage growth, read replicas | Vertical scaling, storage growth, read replicas |
| Built-in HA | Platform-managed; zone redundancy in supported tiers/regions | Platform-managed; zone redundancy where supported | Customer designs Azure + SQL HA | Same-zone or zone-redundant HA options where supported | Zone-redundant HA options where supported |
| Cross-region DR | Active geo-replication and failover groups; geo-restore | Failover groups and geo-restore capabilities | AGs, distributed AGs, log shipping, backup/restore, or ASR according to RPO | Geo-redundant backup/restore and cross-region replicas/features as supported | Geo-redundant backup/restore/read-replica patterns as supported |
| Backup | Automated service backups, PITR, long-term retention options | Automated service backups, PITR, long-term retention options | Customer/SQL VM extension/Azure Backup design | Automated backups and PITR within configured retention | Automated backups and PITR within configured retention |
| Networking | Public endpoint controls, firewall, private endpoint | VNet-injected instance; subnet/DNS/routing prerequisites | VM NIC/VNet; full network control | Public or private access models; private DNS requirements | Public or private access models; private DNS requirements |
| Migration difficulty | Lowest when database is cloud-ready and instance dependencies are removed | Lower for instance-dependent SQL workloads | Lowest code change for rehost; highest ongoing operations | Best for PostgreSQL-compatible workloads | Best for MySQL-compatible workloads |
| Operational overhead | Lowest | Low to moderate | Highest | Low | Low |
| Ideal workload | Cloud-native managed SQL databases/SaaS | Lift-and-modernize SQL Server needing instance compatibility | OS/SQL control, unsupported PaaS feature, vendor constraint | Managed open-source PostgreSQL | Managed open-source MySQL |

```text
Maximum compatibility or OS control → SQL Server on Azure VM
High SQL Server compatibility with managed PaaS → SQL Managed Instance
Cloud-native managed SQL database → Azure SQL Database
PostgreSQL engine requirement → Azure Database for PostgreSQL flexible server
MySQL engine requirement → Azure Database for MySQL flexible server
```

Validate engine versions, extensions, regional features, and migration source/target pairs before committing. Legacy "Single Server" references for PostgreSQL/MySQL are obsolete; use Flexible Server for current designs.

## Azure SQL Database service and compute choices

| Requirement | Direction |
|---|---|
| Predictable continuous workload | Provisioned compute |
| Intermittent single-database workload, automatic pause acceptable | Serverless compute where supported |
| Many databases with variable, complementary demand | Elastic pool |
| Very large database, rapid storage growth, read scale, fast restore | Evaluate Hyperscale |
| Zone-failure resilience | Zone-redundant configuration in a supported service tier/region |
| Strongest isolation and predictable latency | Business-critical/premium-style architecture; validate tier feature set |
| Lower-cost general workload | General Purpose when its storage latency and HA model meet requirements |

DTU bundles compute, memory, and I/O. The vCore model exposes compute generation and separates compute/storage choices more clearly; it is normally better for sizing transparency and Azure Hybrid Benefit. Choose from workload measurements, not tier labels.

### Compute is not data scalability

- Scale up/down changes resources available to an existing database/instance.
- Read scale offloads eligible read-only workloads.
- Elastic pools share compute across databases.
- Sharding/partitioning distributes data and requests across databases or partitions and requires application/data design.
- Caching reduces repeated reads but introduces freshness and invalidation decisions.

## High availability and disaster recovery

| Need | Azure SQL direction | Key trade-off |
|---|---|---|
| Local node/service failure | Built-in service HA | Application still needs retry logic for transient disconnects |
| Availability-zone failure | Zone redundancy where supported | Tier/region support and cost |
| Read-only scale | Read replicas/read scale where supported | Read-after-write delay may matter |
| Cross-region readable secondary for SQL Database | Active geo-replication | Database-level relationships; application failover design required |
| Group databases and stable listener-style endpoints | Failover group | Replication is asynchronous across regions; failover policy matters |
| Recover from accidental change/deletion | Point-in-time restore | Creates a restored database; recovery time depends on size/service |
| Long retention for compliance | Long-term retention | Restore workflow, not an active secondary |

Synchronous local/zone copies target HA. Cross-region replication is normally asynchronous and therefore may have nonzero RPO. A readable replica is not a backup: corruption or logical deletion can replicate.

For SQL Server on Azure VMs:

| Technology | Protection unit | Appropriate direction |
|---|---|---|
| Always On availability group | Database | Separate copies, read replicas, fast database failover; instance objects require separate handling |
| Failover cluster instance | Instance | Instance-level protection and shared storage requirement |
| Log shipping | Database | Simple warm-standby DR; typically manual failover and nonzero RPO |
| Azure Site Recovery | VM | VM-level replication; application-consistency and SQL RPO must be validated |
| Azure Backup / SQL-native backup | Historical recovery | Deletion/corruption/ransomware/history; not live failover |

## Database data protection

```text
At rest      → service encryption/TDE; customer-managed key if required
In transit   → TLS and certificate validation
In use       → Always Encrypted/confidential-computing features where the threat model requires
Access       → Microsoft Entra authentication, database roles, least privilege
Network      → firewall/private connectivity/DNS; disable public access when required
Recovery     → automated backups, PITR/LTR, restore tests, immutable or isolated copies where applicable
Detection    → auditing, Defender capabilities, Monitor logs/alerts
```

Transparent Data Encryption protects database files/backups at rest, not against an authorized query. Dynamic data masking reduces accidental exposure but is not encryption or a privilege boundary. Always Encrypted protects selected values from database operators under its supported query/driver model but adds application/key constraints.

## Cosmos DB and Table Storage

Azure Cosmos DB is a globally distributed database platform with multiple APIs and tunable consistency. Table Storage is a low-cost Azure Storage key/attribute store using partition and row keys.

| Dimension | Azure Cosmos DB | Azure Table Storage |
|---|---|---|
| Scope | Managed NoSQL database platform | Storage account table service |
| Distribution | Native multi-region distribution; multi-region writes where configured | Storage redundancy/failover model, not the same global database control plane |
| Consistency | Multiple configurable consistency levels | Strong consistency within its service model; fewer tuning choices |
| Query/index | Rich API-dependent queries and automatic/configurable indexing | Key-oriented queries; limited secondary query capability |
| Scale model | Request units or serverless; partition-key design is central | Storage transactions and partition scalability |
| Latency/availability objectives | Designed for globally distributed low-latency applications | Suited to simpler, cost-sensitive key/attribute data |
| Features | Change feed, TTL, global distribution, API-specific features | Simple table entities |
| Cost/operations | Higher capability and design complexity | Lower cost/simpler feature set |

### Cosmos DB partitioning and consistency

- Select a partition key with high cardinality and even request/storage distribution.
- Queries that include the partition key are generally more efficient than cross-partition fan-out.
- A hot partition remains a bottleneck even when the overall account has spare capacity.
- Stronger consistency can increase latency or reduce availability/throughput flexibility, especially across regions.
- Multi-region writes improve write locality/availability but require conflict-resolution design.
- Autoscale addresses changing RU demand; it does not correct a poor partition key.
- Change feed supports downstream processing; it is not historical backup by itself.

```text
Globally distributed NoSQL + tunable consistency + low latency → Cosmos DB
Simple Azure key/attribute store + lowest feature/cost need → Table Storage
Relational joins/transactions/schema → relational database, not Table Storage by default
```

## PostgreSQL and other relational services

Choose PostgreSQL flexible server when engine compatibility, PostgreSQL ecosystem, extensions, and portability are mandatory. Validate supported extensions and HA/read-replica topology. Flexible Server provides managed patching, backups, private networking choices, and HA options, but the application still owns query/schema efficiency and connection resiliency.

Do not migrate between engines solely to reduce license cost. Assess SQL dialect, stored procedures, extensions, drivers, collation, transaction semantics, operational tools, and team skills.

## Cost-aware rules

- Serverless can lower idle compute cost but cold-start/auto-pause behavior and feature constraints must meet RTO/latency needs.
- Elastic pools reduce waste only when database demand is sufficiently noncorrelated.
- Read replicas, zone redundancy, and cross-region copies improve resilience/scale but add compute, storage, and transfer cost.
- PaaS normally lowers operational effort; IaaS can be cheaper only when control, licensing, or compatibility value outweighs administration.
- Reserved capacity and Azure Hybrid Benefit can reduce predictable cost but do not fix poor sizing or architecture.

## When not to choose

- Do not choose SQL Managed Instance if database-scoped Azure SQL Database features meet the workload; MI adds instance scope, network, and cost complexity.
- Do not choose SQL Server on VM merely because the source is SQL Server; first test PaaS compatibility.
- Do not choose serverless for a continuously busy or latency-intolerant workload without validating behavior.
- Do not choose Cosmos DB without a credible partition key and access model.
- Do not use geo-replication as the sole defense against user deletion or corruption.

## Common Trap

- Managed service HA does not equal regional DR.
- Active geo-replication/failover groups do not replace backup.
- Scaling compute is not the same as sharding data.
- Azure SQL logical server is a management endpoint, not a customer-managed SQL Server instance.
- Read replicas may be asynchronous; read-after-write and failover RPO need validation.
- Table Storage and Cosmos DB for Table are not interchangeable feature/cost models.

Official references: [Azure SQL service comparison](https://learn.microsoft.com/en-us/azure/azure-sql/database/features-comparison), [Azure SQL purchasing models](https://learn.microsoft.com/en-us/azure/azure-sql/database/purchasing-models), [Azure SQL business continuity](https://learn.microsoft.com/en-us/azure/azure-sql/database/business-continuity-high-availability-disaster-recover-hadr-overview), [SQL Server on Azure VM HADR](https://learn.microsoft.com/en-us/azure/azure-sql/virtual-machines/windows/business-continuity-high-availability-disaster-recovery-hadr-overview), [Cosmos DB resource model](https://learn.microsoft.com/en-us/azure/cosmos-db/resource-model), [PostgreSQL flexible server overview](https://learn.microsoft.com/en-us/azure/postgresql/flexible-server/overview).
