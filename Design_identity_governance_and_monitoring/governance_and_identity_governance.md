# Governance and identity governance

## Resource hierarchy

```text
Microsoft Entra tenant
└── Tenant root management group
    └── Management groups
        └── Subscriptions
            └── Resource groups
                └── Resources
```

Azure Policy and Azure RBAC assignments normally inherit downward. A resource group is a lifecycle and management container, not a network, tenant, or absolute security boundary.

## Structure decisions

| Scope | Design role | Choose a separate scope when |
|---|---|---|
| Tenant | Identity and top-level trust boundary | Legal/identity isolation requires a distinct directory; this is a major operational decision |
| Management group | Policy/RBAC inheritance across subscriptions | A set of subscriptions needs a consistently different governance archetype |
| Subscription | Billing, quota, deployment, administrative, and policy boundary | Environment, ownership, regulatory scope, cost, or scale limits require separation |
| Resource group | Resource lifecycle and delegated management | Resources share deployment/deletion lifecycle and administration |
| Resource | Individual service instance | Exception-level assignment is necessary and manageable |

Keep management-group hierarchies relatively flat and aligned to governance needs, not a frequently changing org chart. Common landing-zone archetypes include platform/connectivity/identity/management subscriptions and workload landing zones for production, nonproduction, sandbox, or regulated workloads.

### Subscription decision factors

- Environment isolation and blast radius
- Distinct administrators or product teams
- Regulatory and policy requirements
- Billing/showback/chargeback
- Service quotas and scale
- Network topology and shared-service ownership
- Deployment lifecycle

Subscriptions are useful management boundaries, but they are associated with one tenant and are not automatically isolated networks. Cross-subscription connectivity and delegated management are supported.

### Resource-group decision factors

- Put resources with a common lifecycle together.
- Resource-group region stores management metadata; contained resources may be in other regions.
- A resource belongs to one resource group at a time; groups cannot be nested.
- Moving resources is service-dependent. Do not base a design on an unvalidated move operation.
- Deleting the group deletes contained resources. Use separation and locks to manage blast radius.

## Governance control comparison

| Control | Primary question | Enforcement behavior | Scope/inheritance | Common use |
|---|---|---|---|---|
| Azure RBAC | Who may perform an action? | Allows control/data actions through role assignments | MG to resource | Least-privilege administration |
| Azure Policy | Is resource state allowed/compliant? | Audit, deny, modify, deploy related resources, and other effects | MG to resource | Regions, SKUs, diagnostics, security configuration |
| Policy initiative | Which policy set represents a standard? | Groups definitions and parameters | Same as Policy | Regulatory/control baseline |
| Resource lock | Can ARM delete or modify this scope? | `CanNotDelete` or `ReadOnly` control-plane protection | Inherits within ARM scope | Guard critical resources against accidents |
| Tag | What business/operational metadata describes it? | Metadata; can be audited/modified by Policy | No automatic resource inheritance from resource group | Cost owner, app, environment, criticality |
| Management group | Where should policy/RBAC inherit across subscriptions? | Hierarchical organization | Downward | Enterprise governance |

### Important boundaries

- Policy does not grant access. RBAC does not enforce resource configuration.
- A lock does not prevent data-plane operations such as deleting a blob with valid data permissions.
- A principal able to remove a lock can remove it and then modify/delete the resource.
- Tags do not automatically inherit from resource groups or subscriptions; use Policy when propagation is required.
- Policy compliance is not proof of full regulatory compliance. It is one technical control and evidence source.
- `Deny` prevents a noncompliant request; `audit` reports it. `modify` and `deployIfNotExists` commonly require a managed identity and remediation for existing resources.
- Exemptions and exclusions reduce coverage; govern, justify, expire, and review them.

## Tagging strategy

Start from decisions and automation, not from collecting every possible label.

| Tag | Purpose |
|---|---|
| `application` / `workload` | Cost and ownership rollup |
| `environment` | Production/nonproduction controls |
| `owner` / `technicalContact` | Operational accountability |
| `costCenter` | Allocation/showback |
| `dataClassification` | Governance and review signal; not an access control |
| `criticality` | Operations and recovery prioritization |

Normalize allowed names/values. Avoid confidential values in tags because tags are broadly visible management metadata. Some resource types do not support tags; tagged costs and resource coverage must be validated.

## Landing zones and enterprise scale

An Azure landing zone is the target environment for workloads, including identity, subscription organization, networking, security, management, governance, and platform automation. It is not merely a virtual network.

```text
Tenant
  ├── Platform management groups/subscriptions
  │     ├── identity
  │     ├── connectivity
  │     └── management/security
  └── Landing-zone management groups
        ├── corporate/internal workloads
        ├── online/internet-facing workloads
        └── sandbox or regulated archetypes
```

Use policy-as-code and subscription vending to make governance repeatable. Separate platform ownership from workload ownership. Central controls should state outcomes; application teams retain freedom inside guardrails.

## Compliance architecture

| Requirement | Direction |
|---|---|
| Prevent deployment outside approved regions | Policy `deny` after assessing global/resource-location semantics |
| Inventory noncompliance before enforcement | Policy `audit` and staged rollout |
| Deploy diagnostic configuration | `deployIfNotExists` with identity/remediation |
| Group a regulatory baseline | Initiative with versioned assignments and parameters |
| Protect critical resource from accidental delete | `CanNotDelete` lock plus least privilege and backup |
| Measure security posture and recommendations | Microsoft Defender for Cloud regulatory compliance/security posture features |
| Preserve evidence | Route logs to protected retention/archive according to policy |

Test Policy effects before broad management-group assignment. Deny or modification policies can block legitimate deployment, break automation, or affect child resources unexpectedly.

## Identity governance model

Identity governance manages the lifecycle and continuing appropriateness of access.

```text
Joiner / mover / leaver event
        ↓
Group, role, or access-package assignment
        ↓
Conditional Access and least privilege
        ↓
PIM activation for privileged access
        ↓
Access review and expiration
        ↓
Audit, investigation, removal
```

| Requirement | Recommended capability |
|---|---|
| Reduce permanent privileged roles | PIM eligible assignments, approval, MFA/authentication context, time limit |
| Review role/group/application access | Access reviews |
| Govern external-user access lifecycle | Access packages, sponsor/approval, expiration, access reviews |
| Package access to groups, apps, and SharePoint sites | Entitlement management |
| Automate HR-driven identity lifecycle | Lifecycle workflows and provisioning where supported |

## Cost and operations

- Management groups, Policy, and tags reduce decentralized governance cost but require ownership and change control.
- More subscriptions improve isolation but increase networking, identity, policy, and operational overhead.
- Microsoft Entra governance capabilities have licensing prerequisites; validate current licenses rather than memorizing editions.
- Central log retention and Defender plans can be major cost drivers. Select coverage from risk and compliance requirements.
- Policy remediation can deploy resources and create cost; understand the resulting state before assignment.

## Common Trap

- Resource groups are not security boundaries and do not provide network isolation.
- Tags are metadata, not enforcement, unless Policy or automation acts on them.
- Subscription separation does not automatically prevent communication.
- Policy prevents/audits/remediates state; RBAC grants actions; locks guard ARM changes.
- Management-group design should follow governance archetypes, not mirror every department.
- PIM governs privileged activation; access reviews recertify access; entitlement management packages and governs access requests.

Official references: [Management groups](https://learn.microsoft.com/en-us/azure/governance/management-groups/overview), [Azure Policy overview](https://learn.microsoft.com/en-us/azure/governance/policy/overview), [Resource locks](https://learn.microsoft.com/en-us/azure/azure-resource-manager/management/lock-resources), [Tagging guidance](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ready/azure-best-practices/resource-tagging), [Azure landing zones](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ready/landing-zone/), [Microsoft Entra ID Governance](https://learn.microsoft.com/en-us/entra/id-governance/identity-governance-overview).
