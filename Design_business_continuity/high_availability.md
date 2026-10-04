# High availability

## Availability scopes

| Failure scope | Design mechanism | Notes |
|---|---|---|
| Process/instance | Health probes, restart, multiple instances | Autoscale alone is not HA if minimum capacity is one |
| Rack/host/maintenance domain | Availability set or platform-managed distribution | Single datacenter scope; mainly relevant where zones are unavailable or for existing designs |
| Availability zone/datacenter | Zonal instances across zones or zone-redundant service | Still one Azure region |
| Region | Multi-region deployment and data replication | DR or multi-region HA depending on architecture/operations |
| Global entry point | Front Door or Traffic Manager according to protocol/features | Backend health and data failover remain required |

## Availability zones versus availability sets

| Dimension | Availability zones | Availability sets |
|---|---|---|
| Isolation | Physically separate datacenter locations within region | Fault/update domains within a datacenter-scale deployment |
| Protection | Zone/datacenter failure | Host/rack and planned maintenance distribution |
| Placement | Zonal or zone-redundant service design | VMs assigned to same availability set |
| Best direction | Preferred for new critical regional designs where supported | Existing/region-without-zones VM designs |
| Regional disaster | No | No |

Availability sets and zones are alternative VM placement models; they are not combined for the same VM deployment. Zone numbers are logical per subscription and not a cross-subscription physical mapping guarantee.

## Compute HA

| Compute | HA design | Key constraints |
|---|---|---|
| Virtual Machines | At least two instances across zones where supported; Load Balancer/Application Gateway; application-aware data tier | Guest/application clustering, patching, state, and health probes remain customer-owned |
| Virtual Machine Scale Sets | Multiple instances, zone distribution, health and upgrade policy, autoscale | Scale set does not make a stateful application safe automatically |
| App Service | Multiple plan instances; zone redundancy where supported; Front Door for regional failover | Plan tier/region support, shared plan failure domain, deployment slots are release tools |
| Azure Functions | Plan-specific platform scale/zone features; idempotent triggers; durable state externalized | Consumption characteristics, retries, concurrency, and downstream limits |
| Container Apps | Multiple replicas, availability-zone support/environment design, revision traffic | Scale-to-zero may conflict with immediate availability; external dependencies dominate |
| AKS | Multi-zone node pools, multiple nodes, pod topology, disruption budgets, resilient control plane | Kubernetes does not automatically make applications or persistent data HA |
| Azure Batch | Multiple nodes/pools and retry/requeue design | Nodes are disposable; task checkpointing and output durability matter |

Health probes must test the dependency depth necessary for safe routing without creating cascading failure. A shallow probe can send users to broken instances; an overly deep probe can remove every instance during a shared downstream outage.

## Load balancing and state

- Layer 4 Load Balancer distributes TCP/UDP flows regionally.
- Application Gateway distributes regional HTTP(S) and can use WAF.
- Front Door is the global edge HTTP(S) entry point with acceleration/WAF.
- Traffic Manager is DNS-based global routing and cannot terminate or proxy the application connection.

Stateful sessions reduce failover flexibility. Prefer external durable state and idempotent operations. If affinity is mandatory, understand what happens when the selected instance fails.

## Relational data HA

| Service | Local HA | Zone HA | Regional DR |
|---|---|---|---|
| Azure SQL Database | Built into service | Zone redundancy in supported tiers/regions | Geo-replication/failover groups/geo-restore |
| SQL Managed Instance | Built into service | Zone redundancy where supported | Failover groups/geo-restore options |
| SQL Server on VM | Customer selects FCI/AG/platform placement | Place nodes across zones and validate latency/quorum | Cross-region AG/distributed AG/log shipping/backup/ASR as appropriate |
| PostgreSQL flexible server | Platform service plus optional HA | Zone-redundant HA where supported | Read replica/geo-backup/restore patterns as supported |

Applications must implement transient-fault handling and reconnect to stable endpoints. Local platform failover can still terminate connections and in-flight transactions.

### Synchronous versus asynchronous

| Replication | Benefit | Cost/trade-off |
|---|---|---|
| Synchronous | Lowest RPO for acknowledged writes | Adds commit latency; distance-sensitive |
| Asynchronous | Suitable across long distance, lower primary latency | Nonzero RPO and possible lag |

Use synchronous replication for local/zone HA when latency permits. Use asynchronous replication for cross-region DR unless the product and business latency requirements support otherwise.

## Semi-structured and unstructured data HA

- LRS protects from local hardware failures but not datacenter loss.
- ZRS keeps reads and writes available through a zone loss for supported storage services.
- GRS/GZRS add an asynchronously replicated region for DR; RA variants permit secondary reads.
- Cosmos DB distributes data across configured regions and can use multiple write regions; consistency and conflict resolution are architecture choices.
- Cache should normally be treated as reconstructable or have a deliberate persistence/geo-replication design. Cache HA is not source-of-truth durability.

## HA design checklist

- Identify every single point of failure, including NAT, DNS, identity, Key Vault, certificates, monitoring, and deployment pipeline.
- Use at least two healthy instances for any tier requiring continuity.
- Confirm zone support for every selected SKU and region.
- Separate replicas across failure domains and avoid shared state that reintroduces one failure point.
- Define capacity after one zone/instance fails; N instances are not useful if survivors cannot carry load.
- Use graceful retries with bounded exponential backoff and circuit breaking.
- Test chaos/failover and measure actual recovery, not only provider SLA.
- Plan maintenance and deployment failure separately from infrastructure failure.

## Cost-aware availability

- Higher availability consumes redundant capacity even when idle.
- Zone transfer, cross-region replication, standby compute, duplicate licenses, and testing add cost.
- Active-active may eliminate idle standby but greatly increases data/application complexity.
- Right-size by business impact: not every component requires the same RTO/RPO.
- Reserved capacity can reduce steady redundant-compute cost but reduces flexibility.

## Common Trap

- Autoscale adjusts capacity; it does not guarantee redundancy.
- Availability zones do not protect from every regional event.
- A load balancer cannot fix a single-instance backend or failed database.
- A higher service SLA does not automatically satisfy the workload SLA.
- Multi-region compute without multi-region data and routing is incomplete.
- Deployment slots reduce release risk but are not a regional DR mechanism.

Official references: [Azure availability zones](https://learn.microsoft.com/en-us/azure/reliability/availability-zones-overview), [VM availability options](https://learn.microsoft.com/en-us/azure/virtual-machines/availability), [Mission-critical architecture](https://learn.microsoft.com/en-us/azure/architecture/reference-architectures/containers/aks-mission-critical/mission-critical-intro), [Azure SQL availability](https://learn.microsoft.com/en-us/azure/azure-sql/database/high-availability-sla-local-zone-redundancy), [Azure Storage redundancy](https://learn.microsoft.com/en-us/azure/storage/common/storage-redundancy).
