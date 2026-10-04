# AZ-305 Master Mental Map

## Constraint-first reasoning

Evaluate requirements in this order:

1. Business outcome and mandatory functional requirement
2. Security, identity, compliance, and prohibited designs
3. Availability, recovery time objective (RTO), and recovery point objective (RPO)
4. Data model, consistency, durability, and residency
5. Network location, connectivity, and exposure
6. Scalability, latency, throughput, and performance
7. Compatibility and migration constraints
8. Operational responsibility and team capability
9. Cost constraint
10. Recommended solution and explicit reasons competing solutions fail

A cheaper option is wrong if it misses a mandatory requirement. A highly capable option is also wrong when its added complexity has no requirement.

## Connected workload model

```text
Business outcomes, users, data classification, RTO/RPO, budget
                              ↓
Identity → Governance → Landing zone → Observability
                              ↓
Users / devices / partner systems / on-premises
                              ↓
             Global or regional network entry point
                              ↓
            Web Application Firewall (WAF) / firewall / routing / private access
                              ↓
                 Application compute platform
                              ↓
          API / messages / events / cache / configuration
                              ↓
                Operational and analytical data
                              ↓
             Zone high availability (HA) / regional disaster recovery (DR) / backup history
                              ↓
                Monitoring, response, and optimization
```

Each arrow is a dependency and a possible failure/security boundary.

Use this review order to test whether a design is complete:

```text
Business requirement
→ identity → governance → network → compute
→ application integration → data → availability → disaster recovery
→ monitoring → operations and cost
```

The order is a review discipline, not a fixed deployment sequence. Revisit earlier decisions when a downstream constraint changes.

## 1. Identity

```text
User or workload
      ↓ authentication
Microsoft Entra ID
      ↓ sign-in policy
Conditional Access / Identity Protection
      ↓ authorization
├── Entra role: directory administration
├── Azure role-based access control (Azure RBAC): Azure resource actions
├── Data-plane role: blobs, secrets, messages, data
└── Application role/claim: application behavior
```

- Human workforce: Entra ID, multifactor authentication (MFA)/passwordless, Conditional Access.
- Partner access: External ID business-to-business (B2B) collaboration.
- Customer-facing customer identity and access management (CIAM): External ID external tenant for new designs.
- Azure workload: managed identity first; workload federation/service principal when necessary.
- Legacy domain protocols: Active Directory Domain Services (AD DS) or Microsoft Entra Domain Services according to administrative requirements.
- Secrets/keys/certificates: Key Vault; non-secret dynamic configuration: App Configuration.

## 2. Governance

```text
Tenant root
  → management groups: policy/RBAC inheritance
    → subscriptions: billing, quota, administrative boundary
      → resource groups: lifecycle container
        → resources
```

- RBAC: who can do what.
- Policy: which resource state is compliant/allowed.
- Lock: guard Azure Resource Manager (ARM) modification/deletion.
- Tag: business/operational metadata; no automatic inheritance.
- Landing zone: identity + hierarchy + networking + governance + security + management.
- Microsoft Entra Privileged Identity Management (PIM): time-bound privileged access; access review: recertification; entitlement management: packaged access lifecycle.

## 3. Observability

```text
Control-plane events → Activity Log
Resource operations   → resource logs + diagnostic settings
Guest operating system (OS) → Azure Monitor Agent + Data Collection Rule (DCR)
Application           → Application Insights
Numeric signals       → metrics
                            ↓
Log Analytics / metrics / Storage / Event Hubs
                            ↓
Queries + workbooks + alerts → action groups/automation
```

Choose collection, store, retention, access, alert, and response together. Monitoring that cannot trigger an owned response is incomplete.

## 4. Data

```text
Relational transactions?
  ├── SQL Server compatibility
  │     ├── OS/full control → SQL Server on Azure VM
  │     ├── instance compatibility + platform as a service (PaaS) → SQL Managed Instance
  │     └── database-scoped cloud PaaS → Azure SQL Database
  └── PostgreSQL/MySQL engine → Flexible Server

Nonrelational?
  ├── global operational NoSQL → Cosmos DB
  ├── simple key/attribute → Table Storage
  ├── objects → Blob Storage
  ├── analytics filesystem → Azure Data Lake Storage Gen2 (ADLS Gen2)
  ├── Server Message Block (SMB)/Network File System (NFS) share → Azure Files
  └── VM block storage → managed disks
```

Then decide partition key, consistency, tier, redundancy, encryption, private access, backup, and region. Compute scaling does not replace data partitioning. Replication does not replace backup.

## 5. Compute

```text
Custom OS / vendor dependency / rehost → VM
Homogeneous elastic VM fleet           → VM Scale Sets
Managed web/API                        → App Service
Event-triggered code                   → Functions
Managed container microservices/jobs   → Container Apps
Kubernetes control/ecosystem           → Azure Kubernetes Service (AKS)
Simple isolated container              → Container Instances
Mass parallel/high-performance computing (HPC) jobs → Azure Batch
Connector-based workflow               → Logic Apps
```

State placement, startup time, scale unit, health, deployment model, zone support, networking, and team skills change the choice.

## 6. Application integration

```text
Transactional command/enterprise queue/topic → Service Bus
Discrete event notification/fan-out          → Event Grid
High-throughput telemetry stream/replay       → Event Hubs
Simple low-cost work queue                    → Queue Storage
API facade/policy/developer access            → API Management
Low-latency distributed cache                 → Azure Managed Redis
Dynamic non-secret configuration              → App Configuration
```

Design for duplicates, retries, idempotency, ordering scope, poison data, backpressure, and observability.

## 7. Networking

```text
Global HTTP(S), WAF, acceleration → Front Door
Regional/private HTTP(S), WAF     → Application Gateway
Regional Transmission Control Protocol (TCP)/User Datagram Protocol (UDP) → Load Balancer
Global DNS-based routing          → Traffic Manager

Encrypted hybrid over internet   → virtual private network (VPN) Gateway
Private provider connectivity     → ExpressRoute
Managed many-branch transit       → Virtual WAN

Private Internet Protocol (IP) address for PaaS → Private Endpoint + Domain Name System (DNS)
Subnet identity to public PaaS    → Service Endpoint
Stable scalable outbound source network address translation (SNAT) → NAT Gateway
Central network filtering         → Azure Firewall
Distributed Layer 3/Layer 4 segmentation → network security group (NSG)
```

New Azure Virtual Networks (VNets) require explicit outbound connectivity. VNet peering is nontransitive. Private connectivity still requires identity authorization.

## 8. Availability, backup, and DR

```text
HA     → survive instance/host/zone failure with redundant live capacity
Backup → recover historical state after deletion/corruption/attack
DR     → recover service after region/site failure
```

RTO drives standby capacity and automation. RPO drives backup frequency and replication mode. Synchronous local/zone replication favors low RPO but adds latency. Asynchronous cross-region replication accepts possible data loss. Test failover and restore; configuration is not evidence of recovery.

## 9. Migration

```text
Discover inventory/dependencies
      ↓
Assess compatibility, sizing, cost, RTO/RPO
      ↓
Choose rehost / replatform / refactor / replace / retain / retire
      ↓
Prepare landing zone and wave
      ↓
Replicate/copy → test → cut over → validate → decommission
```

- Servers: Azure Migrate.
- Databases: current assessment plus Azure Database Migration Service (DMS)/database-specific supported path.
- Managed online file migration: Storage Mover.
- Scripted copy: AzCopy.
- Offline bulk: Data Box.

Choose target architecture first, then tool.

## 10. Cost and operations

| Decision | Cost/operations relationship |
|---|---|
| Infrastructure as a service (IaaS) to PaaS | Usually less platform operation, but compatibility and service constraints increase |
| Single region to multi-region | Higher compute/data/transfer/testing cost; lower outage risk |
| Locally redundant storage (LRS) to zone-redundant storage (ZRS), geo-redundant storage (GRS), or geo-zone-redundant storage (GZRS) | Higher durability/availability and cost; not backup |
| Provisioned to serverless | Better for intermittent use; cold start/feature/latency constraints |
| Central hub/governance | Consistency and scale; shared failure domain and platform-team dependency |
| Cache/replica | Higher component cost; lower latency/load if hit/read patterns justify |

Use the Well-Architected pillars as a review, not independent checklists:

- Reliability: failure modes, RTO/RPO, redundancy, recovery testing.
- Security: identity, least privilege, network isolation, encryption, detection.
- Cost Optimization: pay only for requirements; right-size and remove waste.
- Operational Excellence: automation, observability, safe deployment, ownership.
- Performance Efficiency: scale model, latency, throughput, partitioning, caching.

## Generic cross-domain patterns

### Global web application

```text
Users → Front Door + WAF
      → App Service / Container Apps / AKS in two regions
      → managed identity → Key Vault
      → Private Endpoint → Azure SQL/Cosmos DB/Storage
      → geo-replication + backup
      → Azure Monitor + Application Insights
```

Front Door supplies global entry/routing, not database DR. Private Endpoint removes public data ingress, not authorization. Geo-replication provides a regional copy, while backup supplies history.

### Hybrid private application

```text
On-premises → ExpressRoute or VPN
            → hub firewall/DNS resolver
            → spoke application
            → Private Endpoint + private DNS
            → PaaS data
```

The design needs nonoverlapping addresses, routing, DNS forwarding, authorization, egress, and a redundant hybrid path.

### Event analytics

```text
Producers → Event Hubs → Stream Analytics/Databricks
                         ├── hot result/alert
                         └── capture to ADLS → batch/lakehouse analytics
```

Event Hubs retains the stream; processing and durable analytical storage are separate responsibilities.

## Cross-domain dependency checks

| Selected component | Dependencies that can change or invalidate the choice |
|---|---|
| [Application Gateway](Design_infrastructure_solutions/networking.md#application-delivery-matrix) | Regional placement, public/private frontend, certificates, WAF policy, backend reachability, probes, zone design, and backend capacity |
| [SQL Managed Instance](Design_data_storage_solutions/relational_data.md#relational-service-decision-matrix) | Subnet/DNS/routing, identity, SQL instance compatibility, migration path, service tier, HA/DR topology, backup, and cost |
| [Private Endpoint](Design_infrastructure_solutions/networking.md#private-endpoint-versus-service-endpoint) | Consumer network path, private DNS, endpoint approval, service firewall/public-access state, data authorization, and regional recovery |
| [AKS](Design_infrastructure_solutions/compute.md#aks) | Network model/IP capacity, workload identity, ingress, node pools/zones, persistent data, upgrades, observability, policy, and operator capability |

Do not accept a component-level recommendation until its cross-domain dependencies also satisfy the scenario.

## Candidate-elimination examples

| Mandatory requirement | Choose/evaluate | Why close alternatives fail |
|---|---|---|
| Global HTTP(S) edge routing and WAF | Front Door | Traffic Manager is DNS-only; Application Gateway is regional; Load Balancer is Layer 4 |
| Managed SQL with instance-scoped compatibility | SQL Managed Instance | SQL Database can miss instance dependencies; SQL VM adds OS/SQL operations without a control requirement |
| Private-IP access to a PaaS resource from connected VNets/on-premises | Private Endpoint + private DNS | Service Endpoint keeps the public service endpoint and is subnet-oriented; public IP allowlists do not provide private addressing |
| Transactional commands requiring sessions, topics, and dead-lettering | Service Bus | Event Grid distributes notifications; Event Hubs is a stream; Queue Storage lacks the required broker features |

## Universal scenario-solving model

1. What is mandatory?
2. What is prohibited?
3. Where are users and resources located?
4. Is access public, private, or both?
5. What is the authentication model?
6. What is the authorization model?
7. What is the data model and required consistency/durability?
8. What is the compute model?
9. What is the integration model: request, command, event, stream, or batch?
10. Which failure scopes must remain available?
11. What is the RTO?
12. What is the RPO?
13. What are the scaling, latency, and throughput requirements?
14. Which compatibility constraints are mandatory?
15. What must migrate, with how much downtime and rollback capability?
16. Which layers may Azure manage, and which must the team control?
17. What is the cost constraint after mandatory requirements are met?
18. Which candidates remain?
19. Why are the other candidates wrong?

---

## Navigation

| Previous | Home | Next |
|---|---|---|
| [← Well-Architected Framework](AZ_305_architect_prerequisites/well_architected_framework.md) | [AZ-305 Home](README.md) | [Objective map →](AZ-305_OBJECTIVE_MAP.md) |
