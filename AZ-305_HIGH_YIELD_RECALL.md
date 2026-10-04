# AZ-305 High-Yield Recall

Use after the domain guides. Requirement clues indicate a direction, not an automatic answer.

## Reasoning order

```text
Mandatory function
→ security/compliance
→ availability and RTO/RPO
→ performance/scale
→ compatibility
→ operations
→ cost
```

## Service responsibility model

| Model | Azure manages | Customer still designs/manages | Use when |
|---|---|---|---|
| IaaS | Physical facilities, hardware, and base virtualization | Guest OS, patching, middleware/runtime, application, data, identity, network configuration, resilience | OS control, vendor appliance, legacy compatibility, or rehost is mandatory |
| PaaS | Infrastructure plus service platform/runtime operations | Application/data, identities, access, configuration, scaling choices, recovery design, and service limits | Managed operations fit the workload and compatibility constraints |
| Containers | Hosting responsibility varies by service | Image, application, dependencies, supply chain, data, and workload security | Portable packaging or orchestration behavior is required |
| Serverless | Infrastructure and runtime scaling within service limits | Code/workflow, data, identity, configuration, retries, idempotency, and observability | Event-driven or intermittent execution fits the latency and execution model |

PaaS reduces platform work; it does not transfer responsibility for architecture, data, identity, configuration, or recovery outcomes.

## Identity and governance

| Authentication | Authorization |
|---|---|
| Proves which identity is making the request | Determines which actions that identity may perform |
| Entra sign-in, MFA, federation, managed/workload identity | Entra roles, Azure RBAC, data-plane roles, application permissions |

| Requirement clue | Think about | Exception/check |
|---|---|---|
| Azure resource authorization | Azure RBAC | Data-plane access may need a data role |
| Directory administration | Microsoft Entra role | Not Azure resource control |
| Sign-in context/risk/device policy | Conditional Access | It does not grant resource permission |
| Azure workload needs token without secret | Managed identity | Host/service must support it |
| External CI/CD workload without secret | Workload identity federation | Scope issuer/subject/audience tightly |
| Partner access to workforce resources | External ID B2B | Govern guest lifecycle |
| New customer identity/CIAM | External ID external tenant | Azure AD B2C is legacy for existing customers |
| Temporary privileged activation | PIM | Access review answers a different question |
| Recertify access | Access reviews | Entitlement management packages access |
| Secrets/certificates/keys | Key Vault | App Configuration is for non-secret settings |

| Control | What it answers |
|---|---|
| Azure RBAC | Who can perform which Azure action at which scope? |
| Azure Policy | Is this resource state allowed/compliant? |
| Resource lock | Can ARM modify/delete this resource? |
| Tag | What business/operational metadata describes it? |

Common traps:

- Authentication is not authorization.
- Contributor does not automatically have permission to create role assignments.
- A managed identity has no useful access until roles/policies grant it.
- Resource groups are lifecycle containers, not security boundaries.
- Tags on a resource group do not automatically inherit to resources.
- Locks do not prevent authorized data-plane deletion.

## Monitoring

| Need | Direction |
|---|---|
| Azure control-plane change | Activity Log |
| Service-specific resource operation | Resource logs + diagnostic settings |
| Numeric low-latency signal | Metrics |
| Guest OS telemetry | Azure Monitor Agent + DCR |
| Request/dependency/exception tracing | Application Insights |
| Query/correlate logs | Log Analytics workspace + KQL |
| Archive | Storage |
| Stream to SIEM | Event Hubs |
| Reusable response target | Action group |

Azure Monitor is the umbrella; Log Analytics is the log-query/store capability. Workbooks visualize data; they do not collect it.

## Relational data

```text
Maximum SQL Server compatibility / OS control → SQL Server on Azure VM
Instance features + managed PaaS                → SQL Managed Instance
Cloud-native database-scoped managed SQL        → Azure SQL Database
PostgreSQL/MySQL engine                          → Flexible Server
```

| Requirement | Direction |
|---|---|
| Intermittent single database | SQL Database serverless, if latency/feature constraints fit |
| Many databases with noncorrelated demand | Elastic pool |
| Very large SQL DB/read scale/fast restore | Evaluate Hyperscale |
| Instance-scoped SQL Agent/cross-database compatibility | Managed Instance |
| Unsupported PaaS feature/OS access | SQL Server on Azure VM |
| Cross-region group failover | Failover group where supported |
| Historical recovery | PITR/LTR/backup, not replica |

Traps:

- Vertical compute scale is not data partitioning.
- Geo-replication is not backup.
- Managed service local HA is not regional DR.
- Read replicas can lag.
- Dynamic data masking is not encryption or a security boundary.

## NoSQL and storage

| Need | Direction |
|---|---|
| Global NoSQL, tunable consistency | Cosmos DB |
| Simple key/attribute store | Table Storage |
| Objects/media/logs | Blob Storage |
| Analytics filesystem/hierarchy | ADLS Gen2 |
| SMB/NFS managed share | Azure Files |
| VM block device | Managed disks |

| Blob Storage | Azure Files | ADLS Gen2 |
|---|---|---|
| Object access for media, logs, backups, and application data | Managed SMB/NFS file shares for lift-and-shift and shared file access | Blob Storage with hierarchical namespace for analytics filesystem semantics |
| HTTP(S)/REST and SDK access | File protocol and mount semantics | Hadoop-compatible access, directory operations, and analytics engines |
| Choose tier, redundancy, lifecycle, protection | Validate protocol, identity, performance tier, and sync needs | Validate HNS-dependent feature compatibility and namespace design |

| Redundancy | Recall |
|---|---|
| LRS | Copies in one location; no zone protection |
| ZRS | Synchronous across zones; no regional DR |
| GRS | Asynchronous secondary region; secondary not readable before failover |
| RA-GRS | GRS plus secondary reads |
| GZRS | ZRS primary plus asynchronous secondary region |
| RA-GZRS | GZRS plus secondary reads |

Traps:

- Replication does not preserve history from logical deletion/corruption.
- RA secondary is read-only.
- Archive has rehydration delay and feature/redundancy constraints.
- Azure File Sync is not backup.
- Cosmos DB autoscale does not repair a hot partition.

## Data integration

| Need | Direction |
|---|---|
| Hybrid copy and pipeline orchestration | Data Factory |
| Durable analytics lake | ADLS Gen2 |
| Spark/lakehouse/data engineering | Azure Databricks |
| Integrated SQL/Spark/pipelines workspace | Synapse Analytics |
| Managed streaming query/window | Stream Analytics |
| High-throughput event ingestion/replay | Event Hubs |

Data Factory moves/orchestrates; ADLS stores; Databricks/Synapse process; Event Hubs ingests streams; Stream Analytics processes streams.

## Business continuity

```text
HA         = continue through local failure
Backup     = recover historical data/state
DR         = restore workload after major/site/region failure
Replication= maintain copies, including possibly bad changes
RTO        = acceptable recovery time
RPO        = acceptable data loss
```

| Failure | Think about |
|---|---|
| Process/VM | Multiple healthy instances + load balancer |
| Rack/host | Availability set or platform placement |
| Zone/datacenter | Zones/zone-redundant service |
| Region | Secondary-region app + data + routing + runbook |
| Accidental delete/corruption | Backup/version/PITR |
| Ransomware | Immutable/isolated recovery plus separate authorization |

| Azure Backup | Azure Site Recovery |
|---|---|
| Historical recovery | Replicated workload failover |
| Restore points | Recovery site/region |
| Deletion/corruption/retention | Site/region outage |
| Not live HA | Not historical backup |

Traps:

- Availability zones do not equal regional DR.
- Autoscale does not equal HA.
- A successful replication state does not prove recoverability.
- VM-level ASR may not satisfy database transaction RPO.
- Whole-workload RTO is constrained by the slowest dependency/recovery step.

## Compute

| Strong requirement | Direction |
|---|---|
| Custom OS/driver/vendor appliance | VM |
| Elastic homogeneous IaaS fleet | VM Scale Sets |
| Managed HTTP web/API | App Service |
| Trigger/event-driven code | Functions |
| Managed serverless containers/microservices/jobs | Container Apps |
| Kubernetes API/ecosystem/control | AKS |
| Simple isolated container | Container Instances |
| Parallel/HPC job scheduling | Azure Batch |
| Connector/workflow automation | Logic Apps |

Traps:

- Containers do not imply AKS.
- AKS still requires workload/node/network/upgrade operations.
- Container Instances is not a full orchestrator.
- App Service VNet integration is outbound; Private Endpoint is private inbound.
- Deployment slots reduce release risk, not regional failure.
- Scale-to-zero can conflict with immediate response time.

## Messaging and eventing

| Requirement | Direction |
|---|---|
| Transactions, sessions/ordering, queues/topics, DLQ | Service Bus |
| Discrete event notification/fan-out | Event Grid |
| Telemetry stream, partitions, consumer groups, replay | Event Hubs |
| Simple low-cost work backlog | Queue Storage |

Traps:

- Event Grid is not a telemetry stream.
- Event Hubs is not a transactional command queue.
- Ordering in Event Hubs is within a partition, not global.
- Service Bus ordering normally uses sessions.
- Design all consumers for duplicates/idempotency.

## APIs, cache, configuration, deployment

| Need | Direction |
|---|---|
| API gateway/policy/versions/developer onboarding | API Management |
| Web attack filtering | WAF on Front Door/Application Gateway |
| Low-latency distributed cache | Azure Managed Redis |
| Non-secret settings/feature flags | App Configuration |
| Secrets/keys/certificates | Key Vault |
| App Service staged release | Deployment slots |
| Container progressive release | Revisions/rolling/canary |

Azure Cache for Redis is retiring; prefer Azure Managed Redis for new decisions and validate migration/feature availability.

## Networking: application delivery

| Service | Scope/layer | Choose when |
|---|---|---|
| Front Door | Global L7 proxy | Global HTTP(S), edge acceleration, WAF |
| Application Gateway | Regional L7 proxy | Regional/private HTTP(S), path routing, WAF |
| Load Balancer | Regional L4 | TCP/UDP and internal/public load balancing |
| Traffic Manager | Global DNS | DNS-based endpoint selection, varied protocols |

Traps:

- Traffic Manager is not a proxy and does not terminate TLS.
- Load Balancer cannot route by URL.
- Front Door's global role and Application Gateway's regional/VNet role often justify using both.

## Networking: hybrid, private access, security

| Private Endpoint | Service Endpoint |
|---|---|
| A private IP/NIC in the consumer VNet represents the PaaS resource | The service keeps its public endpoint; the subnet identity is extended to it |
| Requires deliberate private DNS and endpoint lifecycle design | Requires service firewall rules and supported VNet/subnet configuration |
| Supports private connectivity patterns including supported cross-network/on-premises access | Primarily secures service access from selected Azure virtual-network subnets |

| Requirement | Direction |
|---|---|
| Encrypted hybrid tunnel over internet | VPN Gateway |
| Dedicated private provider connectivity | ExpressRoute |
| Managed multi-branch/global transit | Virtual WAN |
| Private IP to PaaS | Private Endpoint + private DNS |
| Restrict public PaaS endpoint to subnet | Service Endpoint |
| Stable scalable outbound SNAT | NAT Gateway |
| Central L3–L7 network filtering | Azure Firewall |
| Distributed subnet/NIC L3/L4 filtering | NSG |
| HTTP attack filtering | WAF |
| Volumetric network attack mitigation | DDoS Protection |

Traps:

- ExpressRoute privacy does not imply encryption.
- VNet peering is nontransitive.
- NAT Gateway is outbound translation, not firewall inspection.
- Private Endpoint does not grant data permission and fails without correct DNS.
- Service Endpoint does not assign a private IP to the service.
- New VNets require explicit outbound design after March 31, 2026.

## Migration

| Requirement | Direction |
|---|---|
| Fastest minimal-change move | Rehost |
| Limited change to PaaS | Replatform |
| Redesign for cloud capabilities | Refactor/rearchitect |
| Replace with SaaS/product | Repurchase |
| Server discovery/assessment/migration | Azure Migrate |
| Supported online/offline database move | DMS/current database-specific path |
| Managed online files/folders | Storage Mover |
| Scripted small/one-off storage copy | AzCopy |
| Offline bulk data | Data Box |

Traps:

- Select target architecture before migration tool.
- Online migration minimizes but does not eliminate cutover downtime.
- Data Box does not provide delta synchronization.
- Rehost preserves technical debt.
- Assess identity, DNS, latency, hard-coded IPs, and dependency groups.

## Final cross-domain checks

- Where does identity live during an on-premises or regional outage?
- Does private networking include DNS, routing, authorization, and egress?
- Can every tier survive the same failure scope?
- Is replicated data also backed up historically?
- Does monitoring survive and alert during the event?
- Can the secondary region supply quota, keys, certificates, configuration, and dependencies?
- Is failover tested, reversible, and owned?
- Does the lowest-cost option still satisfy every mandatory requirement?
