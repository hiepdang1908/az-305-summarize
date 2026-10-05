# Cloud Adoption Framework

The Microsoft Cloud Adoption Framework (CAF) is decision guidance for establishing an Azure foundation and operating model that supports workloads over time. It is broader than migration.

## Current Azure adoption phases

| Phase | Architecture question | Relevant outputs |
|---|---|---|
| Strategy | What business outcomes justify adoption, and what constraints shape it? | Motivations, measurable outcomes, financial/technical constraints, and guiding principles |
| Plan | What estate, skills, dependencies, and sequencing are needed to reach those outcomes? | Inventory and rationalization, skills plan, ownership, dependencies, and an adoption backlog |
| Ready | What governed Azure foundation must exist before workloads are placed? | Landing zone, management hierarchy, identity, connectivity, governance, security, and automation |
| Adopt | Which workload approach delivers the intended outcome: migrate, modernize, or build cloud-native? | A workload-specific target, delivery path, validation, and business outcome |
| Govern | How will the organization control cost, compliance, and resource consistency over time? | Policy guardrails, cost management, compliance evidence, and governance review |
| Secure | How will risks to identities, platform, workloads, and data be reduced and detected? | Security baseline, Zero Trust controls, threat protection, posture management, and response |
| Manage | How will services meet operational commitments and improve after deployment? | Monitoring, reliability operations, support, incident response, and continuous optimization |

Adopt is the lifecycle phase; **Migrate**, **Modernize**, and **Cloud-native** are distinct workload paths within it, not additional top-level CAF phases. Microsoft's prerequisite module separates these paths because they address different architectural constraints:

| Adopt path | Architectural problem it addresses | Typical choice signal | Main trade-off |
|---|---|---|---|
| Migrate | Move an existing workload to Azure while preserving more of its current behavior | Time, compatibility, or low-change tolerance favors rehost/replatform before redesign | Faster transition can retain technical debt and IaaS operations |
| Modernize | Change application, data, or hosting architecture to gain managed capabilities or improve scale/resilience | Existing workload has value, but its current design misses cloud goals | Requires compatibility remediation, redesign effort, and migration risk |
| Cloud-native | Build a new workload around cloud services and practices | No legacy implementation needs to be preserved, or a new capability is required | Greater design freedom brings new platform, skills, and operational choices |

The paths can be combined across an estate or revisited per workload. Govern, Secure, and Manage apply throughout adoption; they are not post-migration cleanup. The lifecycle phases are connected and iterative, not a one-way checklist.

## Landing zones

An Azure landing zone is an approved, governed destination for workloads. It is not merely a virtual network.

| Landing-zone concept | Role |
|---|---|
| Platform landing zone | Shared enterprise foundation: management hierarchy, policies, identity integration, connectivity, security, monitoring, and automation |
| Application/workload landing zone | Subscription or set of subscriptions where an application team deploys and operates a workload under inherited guardrails |
| Platform subscription | Hosts shared identity, connectivity, management, or security services according to the target architecture |
| Subscription vending | Standardized request, creation, placement, configuration, and handoff of workload subscriptions |

```text
Tenant
├── Platform management group
│   ├── identity subscription
│   ├── connectivity subscription
│   └── management/security subscription
└── Landing-zone management groups
    ├── corporate-connected application subscriptions
    ├── online/application subscriptions
    └── other governed workload archetypes
```

The exact hierarchy must follow governance requirements rather than copying a reference diagram unchanged.

### Landing-zone design areas

- Tenant and billing enrollment
- Identity and access management
- Resource organization: management groups and subscriptions
- Network topology and connectivity
- Security
- Management and monitoring
- Governance
- Platform automation and DevOps

Use policy-driven governance and subscription democratization: platform teams provide consistent guardrails and shared services; workload teams make workload-level choices within those boundaries.

### Subscription vending and placement

Subscription vending automates approved subscription creation and baseline configuration. A request can capture workload owner, environment, cost metadata, management-group placement, network model, budgets, policy, Azure role-based access control (Azure RBAC), Defender configuration, and monitoring.

```text
Workload requirements
→ choose landing-zone product line/archetype
→ create and place subscription
→ apply inherited guardrails and baseline services
→ hand off to workload team
```

Vending improves consistency and speed. It does not remove the need for workload architecture, least privilege, cost ownership, or lifecycle governance.

## Migration and modernization connection

```text
CAF Strategy
→ business outcomes and constraints

CAF Plan
→ inventory, rationalization, dependencies, waves

CAF Ready
→ landing zone and operational foundation

CAF Adopt
→ migrate, modernize, or build workload

CAF Govern + Secure + Manage
→ control, protect, operate, and improve
```

Do not start large migration waves before identity, connectivity, policies, monitoring, recovery responsibilities, and subscription placement are ready. Conversely, do not overbuild a platform without confirmed workload requirements.

Detailed migration targets and tools: [Migrations](../Design_infrastructure_solutions/migrations.md). Detailed resource hierarchy and guardrails: [Governance](../Design_identity_governance_and_monitoring/governance_and_identity_governance.md).

Official references: [Cloud Adoption Framework overview](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/overview), [Azure landing zones](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ready/landing-zone/), [Landing-zone design areas](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ready/landing-zone/design-areas), [Subscription vending](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ready/landing-zone/design-area/subscription-vending).

---

## Navigation

| Previous | Home | Next |
|---|---|---|
| [← Identity, access, and security foundations](identity_access_security_foundations.md) | [Prerequisites home](README.md) | [Well-Architected Framework →](well_architected_framework.md) |
