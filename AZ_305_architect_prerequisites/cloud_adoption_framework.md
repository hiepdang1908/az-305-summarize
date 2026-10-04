# Cloud Adoption Framework

The Microsoft Cloud Adoption Framework (CAF) is decision guidance for establishing an Azure foundation and operating model that supports workloads over time. It is broader than migration.

## Current Azure adoption phases

| Phase | Architecture question | Relevant outputs |
|---|---|---|
| Strategy | What should Azure adoption achieve? | Motivations, business outcomes, constraints, principles |
| Plan | How will the organization prepare? | Digital estate, rationalization, skills, ownership, adoption backlog |
| Ready | How will the Azure foundation be built? | Landing zone, management hierarchy, identity, connectivity, governance, automation |
| Adopt | How will workloads migrate, modernize, or be built cloud-native? | Workload target architecture, waves, delivery, validation |
| Govern | How will the Azure environment remain controlled? | Policies, cost governance, compliance, resource consistency |
| Secure | How will the platform and workloads be protected? | Security baseline, Zero Trust controls, posture and response |
| Manage | How will Azure be operated and optimized? | Operations baseline, monitoring, reliability, support, continuous optimization |

The phases are connected and iterative. Ready is not a one-time platform project, and Govern/Secure/Manage are not post-migration cleanup.

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

Subscription vending automates approved subscription creation and baseline configuration. A request can capture workload owner, environment, cost metadata, management-group placement, network model, budgets, policy, role-based access control (RBAC), Defender configuration, and monitoring.

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
