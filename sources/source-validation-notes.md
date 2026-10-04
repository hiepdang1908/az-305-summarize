# Source validation notes

## Validation policy

- Microsoft Learn blueprint and current Azure documentation are authoritative.
- Practice files are untrusted secondary signals only.
- No practice question, answer, scenario, or long explanation was copied or reconstructed.
- Numeric limits, prices, SLA percentages, regional availability, and preview/GA status were omitted unless they materially changed a durable architecture decision.
- Every official blueprint bullet maps to substantive content in `AZ-305_OBJECTIVE_MAP.md`.

## Practice-file inventory and method

All 13 files under `example/` were parsed recursively as MHTML. The scan extracted rendered text internally, counted service/concept occurrence by file, and reviewed recurring comparison patterns. Counts are signals of emphasis, not correctness.

Frequency bands use file coverage:

- Very frequent: present in 10–13 files
- Frequent: present in 7–9 files
- Moderate: present in 4–6 files
- Uncommon: present in 1–3 files

## Generalized frequency model

| Band | Repeated validated concepts |
|---|---|
| Very frequent | Azure SQL Database, RPO, App Service, Azure Monitor, Azure Policy, Cosmos DB, Data Lake Storage, Conditional Access, Application Gateway, Front Door, Load Balancer, ExpressRoute, Key Vault, management groups, Blob Storage, Synapse |
| Frequent | DNS, AKS, Log Analytics, availability zones/sets, Azure Backup, Site Recovery, Data Factory, Traffic Manager, SQL Managed Instance, Azure Files, NSG, Microsoft Entra ID, managed identity, Databricks, Functions, geo-replication, PIM, RBAC, access reviews, Table Storage, Private Link, VPN Gateway |
| Moderate | Azure Migrate, Private Endpoint, Application Insights, service principals, Azure Batch, Service Bus, Event Hubs, Event Grid, API Management, Azure Firewall, AzCopy, service endpoints, VM Scale Sets, diagnostic settings, Data Box, Container Instances |
| Uncommon | Container Apps, Azure Managed Redis/older Redis naming, App Configuration, deployment slots, Stream Analytics, Storage Mover, Database for PostgreSQL, object replication, DDoS Protection, entitlement management, DCR, Azure Data Explorer |

Uncommon does not mean unimportant. Several uncommon topics are explicit current blueprint/Learn objectives and therefore have full repository coverage.

## Practice-derived comparison patterns retained

- Entra roles vs Azure RBAC
- Policy vs RBAC vs locks vs tags
- Managed identity vs service principal
- Activity Log vs resource logs vs metrics vs Application Insights
- Azure SQL Database vs Managed Instance vs SQL Server on VM
- Cosmos DB vs Table Storage
- Blob vs Files vs ADLS Gen2 vs managed disks
- LRS/ZRS/GRS/RA-GRS/GZRS/RA-GZRS
- Backup vs Site Recovery vs replication
- Zones vs availability sets vs regional DR
- Front Door vs Application Gateway vs Load Balancer vs Traffic Manager
- VPN Gateway vs ExpressRoute vs Virtual WAN
- Private Endpoint vs service endpoint
- NSG vs Azure Firewall vs WAF vs DDoS Protection
- Service Bus vs Event Grid vs Event Hubs vs Queue Storage
- VM/VMSS vs App Service vs Functions vs Container Apps vs AKS vs ACI vs Batch
- Azure Migrate vs DMS vs Storage Mover/AzCopy/Data Box

## Outdated or conflicting material detected

| Practice/older-learning pattern | Current validated treatment |
|---|---|
| Azure Active Directory naming | Use **Microsoft Entra ID**; note old name only when interpreting legacy material |
| Azure AD B2C as default new CIAM service | Use **Microsoft Entra External ID external tenant** for new designs. Azure AD B2C is unavailable for purchase by new customers since May 1, 2025; existing-customer support follows Microsoft's lifecycle notice |
| Azure Cache for Redis as forward default | Use **Azure Managed Redis** for new decisions. Azure Cache for Redis tiers are on published retirement timelines; migration and feature availability must be checked |
| Implicit outbound access assumed for new VNets | As of March 31, 2026, new VNets use private subnets by default; design NAT Gateway, Firewall, public IP, or Load Balancer outbound explicitly |
| PostgreSQL/MySQL Single Server references | Use **Flexible Server** for current managed designs; legacy Single Server material is not a current target architecture |
| GRS secondary assumed readable | Only RA-GRS/RA-GZRS provides secondary read access before failover; GRS/GZRS secondary is not normally readable/writable |
| Zones treated as DR | Zones address datacenter/zone failure within one region; regional DR needs a second region and recovery design |
| Replication treated as backup | Replication can copy deletion/corruption; historical recovery needs backup/version/PITR/immutability as appropriate |
| Private Endpoint and service endpoint treated as equivalent | Private Endpoint provides a private IP; service endpoint secures access from a subnet to the service's public endpoint |
| Azure Migrate treated as a universal data migration tool | Azure Migrate centers on discovery, assessment, and supported server/database scenarios; choose DMS, Storage Mover, AzCopy, or Data Box for their validated source-target use cases |

## Objective gap analysis

The first content pass was checked against all 49 blueprint bullets.

| Domain | Initial gaps found | Remediation | Final status |
|---|---|---|---|
| Identity/governance/monitoring | On-premises authorization distinction; CIAM lifecycle; DCR routing | Added explicit authorization planes, External ID guidance, observability flow | COMPLETE |
| Data storage | Open-source relational targets; HNS protection caveats; data temperature | Added Flexible Server, feature validation, hot/warm/cold processing | COMPLETE |
| Business continuity | Workload-wide RTO; ransomware isolation; recovery dependency order | Added failure matrix, vault controls, runbook, end-to-end RTO | COMPLETE |
| Infrastructure | Container Apps; explicit VNet egress; current Redis; Storage Mover | Added compute/app/network/migration sections and lifecycle notes | COMPLETE |

## Remaining production-time verification

These are intentional conditional checks, not missing exam content:

- Region and availability-zone support for each service/SKU
- Current preview/GA state and quotas
- Exact pricing and SLA commitments
- DMS/Storage Mover source-target support and online/offline status
- Private Endpoint, backup, redundancy, HNS, and customer-managed-key feature combinations
- Azure Managed Redis migration parity for a specific existing cache
- Licensing prerequisites for Microsoft Entra governance and security capabilities
- Database engine versions and supported extensions

## Final quality gate

- [x] 49/49 current AZ-305 objective bullets mapped to real content
- [x] All required Learn paths and linked instructional units reviewed
- [x] All 13 practice files analyzed without reproducing questions
- [x] Microsoft documentation used as source of truth
- [x] Strong comparison matrices and decision rules included
- [x] HA, backup, DR, replication, RTO, and RPO distinguished
- [x] Identity, governance, monitoring, data, compute, integration, migration, and networking covered
- [x] Current lifecycle/name corrections documented
- [x] No labs, exam dumps, answer keys, or placeholder sections
