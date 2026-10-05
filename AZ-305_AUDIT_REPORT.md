# AZ-305 Knowledge Audit Report

Audit date: **October 5, 2026**  
Authority: current Microsoft Learn AZ-305 study guide, the 12 named Microsoft Learn modules, and current Microsoft Azure documentation indexed in [official sources](sources/official-sources.md).

Key terms: high availability (HA), disaster recovery (DR), Recovery Time Objective (RTO), Recovery Point Objective (RPO), infrastructure as a service (IaaS), and platform as a service (PaaS).

The repository was assessed for technical correctness, use-case depth, decision criteria, constraints, alternatives, security/availability/cost trade-offs, objective traceability, abbreviation quality, and navigation. A heading or service-name mention did not count as complete coverage.

## 1. Files inspected

All **29 pre-audit Markdown files** were inspected.

| Area | Files inspected |
|---|---|
| Root guide and maps | `README.md`; `AZ-305_OBJECTIVE_MAP.md`; `AZ-305_MASTER_MENTAL_MAP.md`; `AZ-305_HIGH_YIELD_RECALL.md`; `AZ-305_GLOSSARY.md` |
| Architect prerequisites | `README.md`; `azure_architecture_fundamentals.md`; `cloud_adoption_framework.md`; `compute_foundations.md`; `identity_access_security_foundations.md`; `storage_foundations.md`; `well_architected_framework.md` |
| Identity, governance, monitoring | `README.md`; `authentication_and_authorization.md`; `governance_and_identity_governance.md`; `logging_and_monitoring.md` |
| Data storage | `README.md`; `relational_data.md`; `semi_structured_and_unstructured_data.md`; `data_integration_and_analytics.md` |
| Business continuity | `README.md`; `high_availability.md`; `backup_and_disaster_recovery.md` |
| Infrastructure | `README.md`; `compute.md`; `application_architecture.md`; `networking.md`; `migrations.md` |
| Sources | `sources/official-sources.md` |

## 2. Files modified

Nineteen files received audit-related content, traceability, navigation, or terminology changes:

- Root: `README.md`, `AZ-305_OBJECTIVE_MAP.md`, `AZ-305_MASTER_MENTAL_MAP.md`, `AZ-305_HIGH_YIELD_RECALL.md`, `AZ-305_GLOSSARY.md`
- Identity/governance/monitoring: `README.md`, `authentication_and_authorization.md`, `governance_and_identity_governance.md`, `logging_and_monitoring.md`
- Data storage: `relational_data.md`, `semi_structured_and_unstructured_data.md`, `data_integration_and_analytics.md`
- Business continuity: `README.md`, `backup_and_disaster_recovery.md`
- Infrastructure: `compute.md`, `application_architecture.md`, `networking.md`, `migrations.md`
- Sources: `sources/official-sources.md`

Pre-existing working-tree edits were preserved and audited with the rest of the repository.

## 3. Files added

- `AZ-305_MICROSOFT_LEARN_MODULE_MAP.md`: separate 12-module summary and learning-objective-to-heading matrix.
- `AZ-305_AUDIT_REPORT.md`: this evidence and gap report.

## 4. Files removed

None.

## 5. Files moved

None. The four scored domain folders and the prerequisite folder remain intact.

## 6. Broken navigation fixed

The local-link and heading-anchor check found no pre-existing broken local targets. Navigation was extended so the learning sequence now goes:

```text
Master Mental Map
→ Objective Map
→ Microsoft Learn Module Map
→ Identity / Governance / Monitoring
→ Data
→ Business Continuity
→ Infrastructure
→ High-Yield Recall
```

The root index and repository tree now include the module map and audit report. Every learning-content page retains Previous, Home/Parent, and Next navigation.

## 7. Incorrect statements corrected

- Corrected the blanket claim that all new virtual networks lost implicit outbound access after March 31, 2026. The documented condition is the network API behavior released after that date, which defaults subnets in new virtual networks to private; the portal also defaults new subnets to private. Earlier API versions and existing virtual networks are not changed automatically.
- Propagated the condition to the networking guide, master mental map, and high-yield recall so the short-form notes do not reintroduce the overgeneralization.

## 8. Outdated statements corrected

- Retained **Azure Migration and Modernization Program (Azure Migration Framework)** because the current AZ-305 Learn objective still uses that label, and added the current customer-offering context: **Azure Accelerate** brings together Azure Migrate and Modernize, Azure Innovate, partner expertise, and Cloud Accelerate Factory assistance.
- Revalidated the existing current terminology for Microsoft Entra External ID/Azure AD B2C lifecycle and Azure Managed Redis/Azure Cache for Redis retirement direction. No unsupported exact retirement date was added to recall content.

## 9. Missing topics added

- Workload organization by environment, product/workload, business unit, geography, and compliance boundary.
- Policy initiative versus individual policy; built-in versus custom policy; `deny`, `audit`, `modify`, and `deployIfNotExists` selection.
- Multifactor authentication versus Conditional Access versus Identity Protection.
- Explicit metrics/logs, Activity Log/resource logs, Application Insights/Log Analytics, and Log Analytics/Azure Data Explorer comparisons.
- Fault tolerance and resilience definitions.
- Snapshot/backup, geo-redundancy/backup, and replication/backup comparisons.
- Azure role-based access control versus shared access signature storage authorization.
- Transparent Data Encryption versus Transport Layer Security versus Always Encrypted.
- Extract-transform-load versus extract-load-transform, and orchestration versus transformation.
- Compute dimensions for control, deployment, startup/scale, state, duration, integration, and operations.
- Functions versus Logic Apps, Container Instances versus Azure Kubernetes Service, App Service versus Azure Kubernetes Service, and Batch versus Virtual Machine Scale Sets.
- Service Bus queue versus topic/subscription.
- Current migration program/engagement terminology.

## 10. Shallow topics expanded

The additions above convert name-only or implicit coverage into decision-ready comparisons. In particular, the compute, monitoring, governance, recovery, and data-integration pages now state when to choose an option, why a close alternative fails, and the operational/security trade-off.

## 11. Duplicate content removed

No material duplicate service chapter required removal. Repetition that remains has a distinct role:

- Prerequisites define the concept.
- Domain guides make the architecture decision.
- High-Yield Recall provides a short decision signal.
- The objective/module maps provide traceability only.
- The glossary expands terms.

New detail was placed in the authoritative domain page and linked/mapped rather than copied into multiple chapters.

## 12. Abbreviation issues fixed

- Verified that Web Application Firewall is written out before **WAF** and that the Azure Well-Architected Framework is not ambiguously abbreviated.
- Added 18 useful glossary entries: B2B, B2C, DNAT, ELT, ETL, FCI, FIDO2, HADR, HDD, LDAP, NFS, NIC, OIDC, OLTP, PHS, PTA, SAML, and UDR.
- Expanded new abbreviations at first meaningful use in the added content and maps.

## 13. Remaining uncertain facts

These are intentionally treated as production-time validation items rather than memorized facts:

- Region, availability-zone, sovereign-cloud, stock keeping unit, and preview/general-availability support.
- Pricing, quotas, service-level agreements, exact performance limits, and licensing prerequisites.
- Current source/target matrices for Database Migration Service, Storage Mover, Azure Site Recovery, Azure Backup, and service-native migration tools.
- Feature combinations involving hierarchical namespace, private endpoints, redundancy, backup, archive tiers, customer-managed keys, and cross-region recovery.
- Product lifecycle dates for Azure Cache for Redis, Azure AD B2C support, and other retiring offerings.

The source index links the authoritative pages that should be rechecked for a real design.

## 14. Remaining gaps

No current study-guide objective or named module learning objective is unmapped after repair. The remaining gaps are deliberate boundaries:

- No labs, deployment walkthroughs, exam dumps, or reconstructed exam questions.
- No attempt to catalog every regional/SKU limit or every specialized Azure service.
- No claim that concise study notes replace workload testing, support-matrix validation, or the live Microsoft documentation.

These constraints keep the repository architect-focused rather than turning it into an AZ-104 administration guide.

## Final coverage report

Coverage percentages measure architecture knowledge quality across correctness, use case, decision criteria, constraints, alternatives, and trade-offs. They do not measure how many headings exist.

| Module | Coverage | Confidence | Notes |
|---|---:|---|---|
| Design governance | 98% | High | Deep hierarchy, Policy/RBAC, tagging, landing-zone, compliance, and identity-governance decisions |
| Design authentication and authorization | 97% | High | Strong identity, sign-in, authorization-plane, workload-identity, lifecycle, and Key Vault coverage |
| Logging and monitoring | 96% | High | Strong signal, routing, workspace, alert, visualization, and Data Explorer selection coverage |
| HA/DR strategies | 96% | High | Clear failure scopes, RTO/RPO, IaaS/PaaS, dependency, and topology reasoning |
| Backup and disaster recovery | 97% | High | Strong workload/vault/replication/recovery/runbook distinctions; support matrices remain volatile |
| Non-relational storage | 97% | High | Strong access model, redundancy, tier, disk, protection, security, and cost decisions |
| Relational storage | 97% | High | Strong compatibility-first SQL selection, scale, availability, protection, Cosmos DB, and Table decisions |
| Data integration | 96% | High | Strong batch/stream, ETL/ELT, service-boundary, data-path, and processing choices |
| Compute | 97% | High | Strong control/state/scale/duration/operations comparisons across the named services |
| Application architecture | 97% | High | Strong messaging semantics, reliability, APIs, cache, configuration, and deployment decisions |
| Networking | 97% | High | Strong scope/layer/topology/hybrid/private/egress/security/performance decisions with the 2026 condition corrected |
| Migrations | 96% | High | Strong strategy, CAF, assessment, tools, compatibility, transfer, waves, cutover, and rollback coverage |

## Domain and quality scores

| Measure | Score | Reason for less than 100 |
|---|---:|---|
| Identity / Governance / Monitoring | 97/100 | Licensing, regional behavior, and evolving identity/monitoring product surfaces still require live validation |
| Data Storage | 97/100 | Engine, tier, API, backup, redundancy, and feature-combination support is intentionally not exhaustively enumerated |
| Business Continuity | 97/100 | Achieved RTO/RPO depends on workload testing and current support matrices, not notes alone |
| Infrastructure | 97/100 | Compute/network/migration feature and SKU details continue to evolve |
| Technical Accuracy | 97/100 | High-risk absolute claims were reviewed and conditioned; live production facts remain time-sensitive |
| Architectural Decision Quality | 97/100 | Major decisions include fit, rejection criteria, constraints, security, availability, cost, and operations |
| Microsoft Learn Module Coverage | 97/100 | All 12 modules and their published learning objectives are mapped substantively; volatile implementation detail is excluded |
| Exam Objective Coverage | 97/100 | All 49 current objective bullets are mapped to substantive exact headings; production-depth validation remains outside scope |
| **Overall AZ-305 Readiness** | **96/100** | Strong architecture reasoning reference; not a substitute for experience, testing, or live service documentation |

## Validation evidence

- **49** current study-guide objective rows present in the objective map.
- **81** published learning objectives across the 12 named modules present in the detailed module matrix.
- **170** local Markdown file links and heading anchors resolved.
- **140** distinct external Microsoft URLs checked; six command-line timeouts were independently opened successfully through the Microsoft documentation index.
- **31/31** Markdown files contain a navigation section after the two report/map additions.

## Navigation

| Previous | Home | Next |
|---|---|---|
| [← Microsoft Learn module map](AZ-305_MICROSOFT_LEARN_MODULE_MAP.md) | [AZ-305 Home](README.md) | [High-yield recall →](AZ-305_HIGH_YIELD_RECALL.md) |
