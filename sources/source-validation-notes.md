# Source validation notes

## Validation policy

- Microsoft Learn blueprint and current Azure documentation are authoritative.
- Practice files are untrusted secondary signals only.
- No practice question, answer, scenario, or long explanation was copied or reconstructed.
- Numeric limits, prices, SLA percentages, regional availability, and preview/GA status were omitted unless they materially changed a durable architecture decision.
- Every official blueprint bullet maps to substantive content in `AZ-305_OBJECTIVE_MAP.md`.

## Practice-file inventory and method

All 13 files under `example/` were parsed recursively as MHTML. The scan extracted rendered text internally, counted service/concept occurrence by file, and reviewed recurring comparison patterns. Counts are signals of emphasis, not correctness.

The complete 13-file scan was repeated after the prerequisite-layer review. It found no newly omitted recurring concept and introduced no question-derived prose.

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

## Improvement-pass content-strength audit

Strength definitions:

- **STRONG**: direct decision guidance, meaningful constraints/trade-offs, and traps or boundaries.
- **ADEQUATE**: correct substantive coverage, but thinner decision support.
- **WEAK**: mentioned without enough content to make an architecture choice.
- **MISSING**: no substantive mapped coverage.

Before the improvement pass, the scored blueprint had **49 STRONG, 0 ADEQUATE, 0 WEAK, and 0 MISSING** objectives. The improvement pass therefore preserved the domain detail and fixed the separate curriculum/navigation gap instead of manufacturing scored-domain gaps. The targeted cost-governance addition strengthens an existing STRONG compliance objective without changing its classification.

| # | Official objective (condensed) | Final strength |
|---:|---|---|
| 1 | Recommend a logging solution | STRONG |
| 2 | Recommend a solution for routing logs | STRONG |
| 3 | Recommend a monitoring solution | STRONG |
| 4 | Recommend an authentication solution | STRONG |
| 5 | Recommend an identity management solution | STRONG |
| 6 | Authorize access to Azure resources | STRONG |
| 7 | Authorize access to on-premises resources | STRONG |
| 8 | Manage secrets, certificates, and keys | STRONG |
| 9 | Recommend hierarchy, subscriptions, resource groups, and tags | STRONG |
| 10 | Recommend a compliance-management solution | STRONG |
| 11 | Recommend an identity-governance solution | STRONG |
| 12 | Store relational data | STRONG |
| 13 | Choose database service and compute tiers | STRONG |
| 14 | Design database scalability | STRONG |
| 15 | Protect relational data | STRONG |
| 16 | Store semi-structured data | STRONG |
| 17 | Store unstructured data | STRONG |
| 18 | Balance storage features, performance, and cost | STRONG |
| 19 | Protect semi-structured and unstructured data | STRONG |
| 20 | Design data integration | STRONG |
| 21 | Design data analysis | STRONG |
| 22 | Meet recovery objectives for Azure and hybrid workloads | STRONG |
| 23 | Back up and recover compute | STRONG |
| 24 | Back up and recover databases | STRONG |
| 25 | Back up and recover unstructured data | STRONG |
| 26 | Design high availability for compute | STRONG |
| 27 | Design high availability for relational data | STRONG |
| 28 | Design high availability for semi/unstructured data | STRONG |
| 29 | Specify compute components from workload requirements | STRONG |
| 30 | Recommend a virtual-machine solution | STRONG |
| 31 | Recommend a container solution | STRONG |
| 32 | Recommend a serverless solution | STRONG |
| 33 | Recommend compute for batch processing | STRONG |
| 34 | Recommend a messaging architecture | STRONG |
| 35 | Recommend an event-driven architecture | STRONG |
| 36 | Recommend API integration | STRONG |
| 37 | Recommend application caching | STRONG |
| 38 | Recommend application configuration management | STRONG |
| 39 | Recommend automated application deployment | STRONG |
| 40 | Evaluate migration with the Cloud Adoption Framework | STRONG |
| 41 | Evaluate servers, data, and applications for migration | STRONG |
| 42 | Migrate workloads to IaaS and PaaS | STRONG |
| 43 | Migrate databases | STRONG |
| 44 | Migrate unstructured data | STRONG |
| 45 | Recommend internet connectivity | STRONG |
| 46 | Recommend Azure-to-on-premises connectivity | STRONG |
| 47 | Optimize network performance | STRONG |
| 48 | Optimize network security | STRONG |
| 49 | Recommend load balancing and routing | STRONG |

```text
Before improvement: STRONG 49 | ADEQUATE 0 | WEAK 0 | MISSING 0
After improvement:  STRONG 49 | ADEQUATE 0 | WEAK 0 | MISSING 0
```

## Repository file audit disposition

| Area | Disposition | Result |
|---|---|---|
| Detailed domain guides | KEEP + VERIFY | Substantive, current, and objective-mapped; no broad rewrite |
| Root navigation and objective map | EXPAND | Added prerequisite-first sequence and separate 5/5 path metric |
| Domain landing pages | EXPAND | Added foundation cross-links |
| High-yield recall | EXPAND | Added explicit responsibility model and focused comparison tables |
| Governance guide | EXPAND | Clarified budgets, cost controls, allocation, and Policy boundaries |
| Source records | VERIFY + EXPAND | Added prerequisite/framework sources and this second-pass audit |
| Duplicate content | REMOVE only if found | No material duplicate section found; none removed |

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
- [x] 5/5 Microsoft Learn preparation paths covered, separately from exam weighting
- [x] All required Learn paths and linked instructional units reviewed
- [x] Seven-file prerequisite foundation path added without treating it as a scored domain
- [x] All 13 practice files analyzed without reproducing questions
- [x] Microsoft documentation used as source of truth
- [x] Strong comparison matrices and decision rules included
- [x] HA, backup, DR, replication, RTO, and RPO distinguished
- [x] Identity, governance, monitoring, data, compute, integration, migration, and networking covered
- [x] Current lifecycle/name corrections documented
- [x] No labs, exam dumps, answer keys, or placeholder sections
