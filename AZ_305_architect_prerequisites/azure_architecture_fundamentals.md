# Azure architecture fundamentals

## Azure physical architecture

| Scope | Architectural meaning | Design consequence |
|---|---|---|
| Datacenter | Physical facility containing compute, storage, and network infrastructure | A single-facility failure must not take down a workload with zone-level requirements |
| Availability zone | Physically separate datacenter group within one region, with independent power, cooling, and networking | Protects against a zonal/datacenter failure; adds distribution and possible interzone latency/cost |
| Region | Deployment area containing one or more datacenters and, where supported, zones | Determines latency, residency, service/SKU availability, pricing, and regional failure scope |
| Geography | Data-residency and market boundary containing regions | Constrains data placement and disaster-recovery choices for regulated workloads |
| Region pair | Microsoft pairing used by many, but not all, regions/services | Can influence platform replication and update sequencing; never assume automatic workload replication or failover |
| Sovereign cloud/region | Isolated cloud environment for specific regulatory or national requirements | Service catalog, identities, endpoints, tooling, and cross-cloud connectivity can differ |

```text
Availability Zone
→ datacenter-level isolation inside one Azure region

Secondary region
→ protection against regional disaster when the workload is deliberately deployed and replicated there
```

These solve different failure scopes. Not every region supports zones, and not every service or SKU supports zonal or zone-redundant deployment. Global services also have service-specific failure and data-residency models; "global" does not mean every dependency is automatically multi-region.

### Placement decision rules

```text
Low latency between application tiers
→ colocate in a region and measure zone-to-zone latency

Datacenter failure must not interrupt service
→ use zonal instances or a zone-redundant service where supported

Region failure must be recoverable
→ design secondary-region application, data, routing, identity, capacity, and operations

Data must remain in a geography
→ select permitted regions and validate every replication/backup destination
```

Detailed resilience decisions: [High availability](../Design_business_continuity/high_availability.md) and [Backup and disaster recovery](../Design_business_continuity/backup_and_disaster_recovery.md).

## Azure resource architecture

```text
Microsoft Entra tenant
└── Tenant root management group
    └── Management groups
        └── Subscriptions
            └── Resource groups
                └── Resources
```

| Scope | Primary architectural purpose | Important boundary |
|---|---|---|
| Tenant | Identity directory and top-level trust/administration | A subscription trusts one tenant at a time; cross-tenant design adds identity and governance complexity |
| Management group | Policy and Azure RBAC inheritance across subscriptions | Design from governance archetypes, not a volatile organization chart |
| Subscription | Billing, quota/scale, policy, and delegated-management unit | Useful workload/environment boundary; not automatic network isolation |
| Resource group | Management and lifecycle container for resources | Not a security or network boundary; deletion affects contained resources |
| Resource | Azure service instance | Service-specific management and data-plane behavior |

Azure Policy and Azure RBAC assignments can inherit downward. Tags applied at a resource group do not automatically inherit to resources. Resources in one resource group can be in different regions and communicate with resources in other groups if networking and authorization permit.

Detailed governance: [Governance and identity governance](../Design_identity_governance_and_monitoring/governance_and_identity_governance.md).

## Control plane versus data plane

| Operation | Plane | Typical authorization/logging concern |
|---|---|---|
| Create or delete a storage account | Azure Resource Manager control plane | Azure RBAC management action; Activity Log |
| Change a virtual network or firewall rule | Control plane | Azure RBAC and Policy; Activity Log |
| Read or write a blob | Storage data plane | Storage data role/SAS/key; resource/data logs where configured |
| Read a Key Vault secret | Key Vault data plane | Key Vault data permission; vault audit logs |
| Query database rows | Database data plane | Database identity/roles and auditing |

A management-plane Contributor role does not automatically grant every data-plane permission. Conversely, a data role such as Storage Blob Data Reader does not allow creation or deletion of the storage account.

Architecture consequences:

- Design management and data access separately.
- Route Activity Log and resource/data logs through their distinct collection mechanisms.
- A resource lock protects supported control-plane changes, not valid data-plane deletion.
- Private network reachability does not grant data-plane authorization.

## Azure Resource Manager

Azure Resource Manager (ARM) is the management plane used to create, update, organize, and govern Azure resources.

Architect-relevant concepts:

- **Resource providers:** namespaces expose resource types and API operations. Required providers must be registered, and feature/API support can vary.
- **Scopes:** management group, subscription, resource group, and resource scopes determine deployment, Policy, and RBAC effects.
- **Declarative model:** Bicep/ARM and other IaC tools describe desired resources and dependencies, enabling repeatable environments and reviewable change.
- **Dependencies:** control deployment ordering, but successful deployment does not prove application/data readiness.
- **Idempotency:** repeated declarative deployment should converge toward desired state; out-of-band changes can create drift.
- **Consistent governance:** Policy, locks, tags, and RBAC apply through the same management hierarchy.
- **Regional metadata dependency:** resource-group and service control planes can be temporarily unavailable even while deployed data-plane workloads continue.

Do not treat IaC as an availability mechanism by itself. Recovery still requires durable data, images/artifacts, keys, DNS/network configuration, capacity, and tested orchestration.

## Foundation decision checklist

1. Which geography and regions meet residency and latency requirements?
2. Which failure scopes must the workload survive?
3. Which services/SKUs support the required zones and regions?
4. Which tenant, management group, subscription, and resource group own the workload?
5. Which permissions are control-plane versus data-plane?
6. Can the environment be rebuilt declaratively, and is state protected separately?

Official references: [Azure physical infrastructure](https://learn.microsoft.com/en-us/training/modules/describe-core-architectural-components-of-azure/5-describe-azure-physical-infrastructure), [Azure management infrastructure](https://learn.microsoft.com/en-us/training/modules/describe-core-architectural-components-of-azure/6-describe-azure-management-infrastructure), [Azure Resource Manager overview](https://learn.microsoft.com/en-us/azure/azure-resource-manager/management/overview), [Azure control plane and data plane](https://learn.microsoft.com/en-us/azure/azure-resource-manager/management/control-plane-and-data-plane).
