# AZ-305 Prerequisite Audit Report

Audit date: **October 5, 2026**  
Scope: `AZ_305_architect_prerequisites/`  
Authority: current Microsoft Learn prerequisite path, its linked modules, and the current Azure VM size/naming documentation. This is a concise architectural coverage audit, not a reproduction of Microsoft Learn.

## 1. Files inspected

| File | Purpose | Official module mapping | Coverage before | Missing / shallow content | Incorrect, outdated, or duplicate content |
|---|---|---|---|---|---|
| [README.md](README.md) | Prerequisite sequence and relationship to scored domains | All six modules | COMPLETE as an index | Did not previously link the detailed file-level audit | No incorrect or outdated topic claims found; no substantive duplication |
| [azure_architecture_fundamentals.md](azure_architecture_fundamentals.md) | Physical Azure layout, resource hierarchy, ARM/control/data planes | Describe the core architectural components of Azure | COMPLETE | No material module objective missing; service/region availability remains conditional and is called out | No confirmed incorrect or outdated statement; no material duplication |
| [compute_foundations.md](compute_foundations.md) | Compute models and workload selection | Describe Azure compute services | PARTIAL / TOO SHALLOW | VM resources, workload-based sizing, size families and names, VMSS operations, and explicit hosting comparisons were absent or too brief | No confirmed incorrect pre-audit fact; family/SKU details are now qualified by series and region |
| [storage_foundations.md](storage_foundations.md) | Azure Storage services, data models, redundancy, security, cost | Describe Azure storage services | PARTIAL | Account-type trade-offs, AzCopy, Storage Explorer, Azure File Sync, Azure Migrate, and Data Box were not covered as module topics | LRS placement description was broad; refined to three copies in one datacenter. No outdated service claim found |
| [identity_access_security_foundations.md](identity_access_security_foundations.md) | Identity, authentication, authorization, security foundations | Describe Azure identity, access, and security | PARTIAL | SSO and explicit MFA-versus-Conditional-Access and RBAC-versus-Policy comparisons were absent | No confirmed incorrect or outdated statement; no material duplication |
| [cloud_adoption_framework.md](cloud_adoption_framework.md) | CAF lifecycle and landing-zone purpose | Introduction to the Microsoft Cloud Adoption Framework | COMPLETE | All module paths had purpose and workload trade-offs; no foundational gap found | No confirmed incorrect or outdated statement; migration detail links to the infrastructure domain |
| [well_architected_framework.md](well_architected_framework.md) | Five pillars and workload trade-offs | Introduction to the Microsoft Azure Well-Architected Framework | COMPLETE | All five pillars and cross-pillar consequences are explained | No confirmed incorrect or outdated statement; linked domain pages provide detail |
| [AUDIT_REPORT.md](AUDIT_REPORT.md) | This audit's evidence and coverage record | None; audit artifact, not a prerequisite module | N/A | Not learning content | No duplicate module material; navigation checked |

## 2. Microsoft prerequisite modules checked

1. [Describe the core architectural components of Azure](https://learn.microsoft.com/en-us/training/modules/describe-core-architectural-components-of-azure/): module objectives and current physical/management infrastructure units.
2. [Describe Azure compute services](https://learn.microsoft.com/en-us/training/modules/describe-azure-compute-networking-services/): module objectives; [VMs](https://learn.microsoft.com/en-us/training/modules/describe-azure-compute-networking-services/2-virtual-machines), [Azure Virtual Desktop](https://learn.microsoft.com/en-us/training/modules/describe-azure-compute-networking-services/4-virtual-desktop), [containers](https://learn.microsoft.com/en-us/training/modules/describe-azure-compute-networking-services/5-containers), [Functions](https://learn.microsoft.com/en-us/training/modules/describe-azure-compute-networking-services/6-functions), and [application hosting](https://learn.microsoft.com/en-us/training/modules/describe-azure-compute-networking-services/7-describe-application-hosting-options). VM family/capability details were checked against [VM size overview](https://learn.microsoft.com/en-us/azure/virtual-machines/sizes/overview) and [VM naming conventions](https://learn.microsoft.com/en-us/azure/virtual-machines/vm-naming-conventions).
3. [Describe Azure storage services](https://learn.microsoft.com/en-us/training/modules/describe-azure-storage-services/): [accounts](https://learn.microsoft.com/en-us/training/modules/describe-azure-storage-services/2-accounts), [redundancy](https://learn.microsoft.com/en-us/training/modules/describe-azure-storage-services/3-redundancy), [services](https://learn.microsoft.com/en-us/training/modules/describe-azure-storage-services/4-describe-azure-storage-services), [data migration](https://learn.microsoft.com/en-us/training/modules/describe-azure-storage-services/6-identify-azure-data-migration-options), and [file movement](https://learn.microsoft.com/en-us/training/modules/describe-azure-storage-services/7-identify-azure-file-movement-options).
4. [Describe Azure identity, access, and security](https://learn.microsoft.com/en-us/training/modules/describe-azure-identity-access-security/): current module learning objectives and units.
5. [Introduction to the Microsoft Cloud Adoption Framework](https://learn.microsoft.com/en-us/training/modules/cloud-adoption-framework/) and the current [CAF overview](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/overview).
6. [Introduction to the Microsoft Azure Well-Architected Framework](https://learn.microsoft.com/en-us/training/modules/azure-well-architected-introduction/) and [current pillar guidance](https://learn.microsoft.com/en-us/azure/well-architected/pillars).

## 3. Missing and partial topics found

- **Compute:** the previous page introduced service names but did not explain the Azure VM resource graph; workload sizing dimensions; family fit for B, D, E, F, M, L, and N; naming clues; VM versus App Service/container/Functions trade-offs; or VMSS load-balancing, autoscale, and scale-out behavior.
- **Storage:** the previous page covered core services and redundancy, but not the official movement tools or migration choices. Storage-account performance/service trade-offs also lacked an explicit comparison.
- **Identity/security:** the previous page named authentication controls but did not clearly distinguish SSO, MFA, Conditional Access, Azure RBAC, and Policy in scenario terms.
- **Core architecture, CAF, Well-Architected:** no material objective-level gap was found in the current pages. They already connect definitions to placement, scope, governance, lifecycle, and trade-offs.

## 4. Incorrect and outdated content found

- **Incorrect facts confirmed:** none in the seven pre-existing prerequisite learning pages.
- **Outdated facts confirmed:** none.
- **Precision improvement:** LRS wording now states the current Microsoft Learn model of three copies within one datacenter in the primary region, clarifying its datacenter-failure limitation.
- No technology was labeled universally required where Microsoft describes optional deployment resources or configuration. VM resources now distinguish the VM, OS disk, and network attachment from optional data disks, NSGs, public IPs, boot diagnostics, and monitoring.

## 5. Files changed

- `compute_foundations.md`: added VM fundamentals/resources, sizing, families, naming, hosting decisions, container distinction, and expanded availability/VMSS reasoning; added direct Microsoft Learn references.
- `storage_foundations.md`: added account-type selection, file movement and migration tools, and precise LRS wording; linked current module units.
- `identity_access_security_foundations.md`: added SSO, MFA-versus-Conditional-Access, RBAC-versus-Policy, and encryption distinctions.
- `README.md`: linked this detailed report.
- `AUDIT_REPORT.md`: added this report.

No files were moved, renamed, or removed. The separate root-level `AZ-305_AUDIT_REPORT.md` was not replaced.

## 6. Sections added or substantially improved

- **Compute:** `Azure VM fundamentals`, `VM resources`, `VM sizing`, `VM size families`, `VM size names`, and `VM hosting and container decisions`; availability and scale sections now explain availability sets, zones, scale sets, scale up/out, balancing, autoscale, and statefulness.
- **Storage:** account options and `Moving and migrating data` distinguish AzCopy, Azure Storage Explorer, Azure File Sync, Azure Migrate, and Azure Data Box.
- **Identity/security:** authentication controls now explain SSO and MFA versus Conditional Access; a comparison explains authorization (Azure RBAC) versus configuration compliance (Azure Policy) and summarizes encryption boundaries.

## 7. Navigation

The prerequisite sequence remains core architecture → compute → storage → identity/security → CAF → Well-Architected Framework. The report is linked from the prerequisite index and has Previous, Parent/Index, and Next navigation. Existing local prerequisite links were valid during the audit; a final all-Markdown local-link/anchor check is recorded after edits.

## 8. Remaining gaps and limits

- Specific VM sizes, disk combinations, regions/zones, quotas, network limits, accelerators, prices, and service availability vary by region, series, subscription, and time; this guide intentionally teaches selection logic instead of cataloging SKUs.
- Storage feature combinations and migration source/target support change. Check the current service support matrix before a real design.
- The prerequisite pages remain conceptual and intentionally omit deployment walkthroughs, portal/CLI procedures, and exhaustive administration detail.

## Matrix — Official topic coverage

| Official Microsoft topic | Repo file | Coverage before | Coverage after | Notes |
|---|---|---|---|---|
| Regions, datacenters, availability zones, geography, region pairs | [azure_architecture_fundamentals.md](azure_architecture_fundamentals.md) | COMPLETE | COMPLETE | Failure scope, residency, latency, availability conditions, and DR distinction |
| Resources, resource groups, subscriptions, management groups, ARM hierarchy | [azure_architecture_fundamentals.md](azure_architecture_fundamentals.md) | COMPLETE | COMPLETE | Governance/RBAC/policy scopes, lifecycle, billing/quota, isolation limits |
| VM concept and architect use cases | [compute_foundations.md](compute_foundations.md) | PARTIAL | COMPLETE | IaaS, Azure-hosted virtualized capacity, OS control, use/avoid decisions |
| VM required and optional resources | [compute_foundations.md](compute_foundations.md) | MISSING | COMPLETE | VM/NIC/subnet/VNet/OS disk graph; optional data disk, NSG, public IP, diagnostics/monitoring |
| VM sizing dimensions and decision method | [compute_foundations.md](compute_foundations.md) | MISSING | COMPLETE | CPU, memory, I/O, network, accelerator, workload pattern, availability, quota, cost |
| B/D/E/F/M/L/N size-family fit | [compute_foundations.md](compute_foundations.md) | MISSING | COMPLETE | Conceptual workload signals, caveat that these are not the full SKU catalog |
| VM size naming concepts | [compute_foundations.md](compute_foundations.md) | MISSING | COMPLETE | Family, vCPU count, selected modifiers, generation; checked against current naming reference |
| Availability sets, zones, and VMSS | [compute_foundations.md](compute_foundations.md) | PARTIAL | COMPLETE | Failure domain versus fleet scaling/management, with limits stated |
| VMSS horizontal scale, autoscale, load balancing, stateless fit | [compute_foundations.md](compute_foundations.md) | PARTIAL | COMPLETE | Single VM vs fleet; scale up vs scale out; autoscale alone is not HA |
| Azure Virtual Desktop | [compute_foundations.md](compute_foundations.md) | PARTIAL | COMPLETE | Centralized virtual desktop/application delivery, not general web hosting |
| VM versus container and ACI/AKS level | [compute_foundations.md](compute_foundations.md) | PARTIAL | COMPLETE | Guest OS control versus shared host kernel; orchestration choice |
| Functions and App Service hosting selection | [compute_foundations.md](compute_foundations.md) | PARTIAL | COMPLETE | Event-triggered/serverless versus managed HTTP web/API versus VM control |
| Storage account options and services | [storage_foundations.md](storage_foundations.md) | PARTIAL | COMPLETE | Standard GPv2 and premium account directions plus service uses |
| LRS/ZRS/GRS/GZRS and RA variants | [storage_foundations.md](storage_foundations.md) | COMPLETE | COMPLETE | Failure scope, secondary readability, asynchronous geo-replication conditions |
| Blob, Files, Queues, Tables, managed disks, access tiers | [storage_foundations.md](storage_foundations.md) | COMPLETE | COMPLETE | Selection by access model, tier, performance, and recovery |
| AzCopy, Storage Explorer, Azure File Sync | [storage_foundations.md](storage_foundations.md) | MISSING | COMPLETE | Scripted copy, GUI movement, and synchronized file-server/cache roles distinguished |
| Azure Migrate and Data Box | [storage_foundations.md](storage_foundations.md) | MISSING | COMPLETE | Online assessment/migration hub versus offline bulk transfer |
| Entra ID/Domain Services, SSO, MFA, passwordless, external identity, Conditional Access | [identity_access_security_foundations.md](identity_access_security_foundations.md) | PARTIAL | COMPLETE | Authentication and authorization roles distinguished; MFA and Conditional Access compared |
| Entra roles, Azure RBAC, Azure Policy | [identity_access_security_foundations.md](identity_access_security_foundations.md) | PARTIAL | COMPLETE | Directory authorization, Azure resource authorization, and configuration compliance separated |
| Zero Trust, defense in depth, encryption, keys, secrets, certificates, Defender for Cloud | [identity_access_security_foundations.md](identity_access_security_foundations.md) | COMPLETE | COMPLETE | Includes security responsibility and key-lifecycle trade-offs |
| CAF Strategy, Plan, Ready, Migrate, Modernize, Cloud-native, Govern, Secure, Manage | [cloud_adoption_framework.md](cloud_adoption_framework.md) | COMPLETE | COMPLETE | Purpose, decision signal, and trade-off described; Adopt paths are not mislabeled as top-level lifecycle phases |
| Well-Architected five pillars and trade-offs | [well_architected_framework.md](well_architected_framework.md) | COMPLETE | COMPLETE | Reliability, Security, Cost Optimization, Operational Excellence, Performance Efficiency |

## Special compute report — `compute_foundations.md`

| Topic | Before | After |
|---|---|---|
| VM concepts | PARTIAL | COMPLETE |
| VM required resources | MISSING | COMPLETE |
| VM sizing | MISSING | COMPLETE |
| VM size families | MISSING | COMPLETE |
| VM naming concepts | MISSING | COMPLETE |
| B-family | MISSING | COMPLETE |
| D-family | MISSING | COMPLETE |
| E-family | MISSING | COMPLETE |
| F-family | MISSING | COMPLETE |
| M-family | MISSING | COMPLETE |
| L-family | MISSING | COMPLETE |
| N-family | MISSING | COMPLETE |
| Availability Sets | PARTIAL | COMPLETE |
| Availability Zones | PARTIAL | COMPLETE |
| VM Scale Sets | PARTIAL | COMPLETE |
| Azure Virtual Desktop | PARTIAL | COMPLETE |
| Containers | PARTIAL | COMPLETE |
| Azure Functions | PARTIAL | COMPLETE |
| Application hosting decisions | PARTIAL | COMPLETE |

## Final quality check

1. **Does the page explain what resources an Azure VM needs?** Yes. It distinguishes the VM, OS disk, and network attachment from optional data disks, NSGs, public IPs, diagnostics, and monitoring.
2. **Does it explain what VM size means?** Yes. It explains capacity and capability limits, not just machine scale.
3. **Does it explain why an architect chooses a family?** Yes. The family matrix and workload examples connect resource profiles to workload behavior.
4. **Does it cover B/D/E/F/M/L/N conceptually?** Yes, with decision signals and workload fits.
5. **Does it say these are not the complete SKU catalog?** Yes. Azure has many series/subfamilies and region/configuration constraints.
6. **Does it compare VM/App Service/containers/Functions?** Yes, in a decision table and explanatory text.
7. **Does it explain Availability Sets/Zones/VMSS correctly?** Yes. It distinguishes fault/update-domain placement, zone-level isolation, and fleet management/scaling.
8. **Are statements current according to Microsoft Learn?** The new/high-risk statements were checked against the current linked Microsoft Learn module and Azure VM size references on the audit date. Volatile SKU, regional, support, pricing, and quota facts are explicitly left for live verification.

---

## Navigation

| Previous | Parent / Index | Next |
|---|---|---|
| [← Well-Architected Framework](well_architected_framework.md) | [Prerequisites index](README.md) | [Master Mental Map →](../AZ-305_MASTER_MENTAL_MAP.md) |
