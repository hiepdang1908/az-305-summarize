# AZ-305 architect prerequisites

This folder represents Microsoft Learn's **AZ-305 Microsoft Azure Architect Design Prerequisites** learning path. It provides preparation material and architectural foundations for the four scored AZ-305 domains.

It is **not a fifth scored exam domain** and has no separate exam weighting.

## Modules and study sequence

1. Describe the core architectural components of Azure → [Azure architecture fundamentals](azure_architecture_fundamentals.md)
2. Describe Azure compute services → [Compute foundations](compute_foundations.md)
3. Describe Azure storage services → [Storage foundations](storage_foundations.md)
4. Describe Azure identity, access, and security → [Identity, access, and security foundations](identity_access_security_foundations.md)
5. Introduction to the Microsoft Cloud Adoption Framework → [Cloud Adoption Framework](cloud_adoption_framework.md)
6. Introduction to the Microsoft Azure Well-Architected Framework → [Well-Architected Framework](well_architected_framework.md)

These files establish mental models. The four domain folders contain the detailed service comparisons, constraints, and exam decision rules.

## Relationship to scored domains

| Prerequisite topic | Why an architect needs it | Primary AZ-305 domains affected |
|---|---|---|
| Azure physical and resource architecture | Select failure boundaries, regions, scopes, ownership, and governance inheritance | Identity/Governance/Monitoring; Business Continuity; Infrastructure |
| Compute models | Select the correct responsibility, control, and scaling model | Infrastructure |
| Storage models | Select data access, durability, redundancy, performance, and cost model | Data Storage; Business Continuity |
| Identity, access, and security | Separate authentication, authorization, workload identity, and secret management | Identity/Governance/Monitoring; all workload designs |
| Cloud Adoption Framework | Establish strategy, landing zones, governance, migration, security, and operations | Infrastructure; Identity/Governance/Monitoring |
| Well-Architected Framework | Evaluate trade-offs across workload quality attributes | All four domains |

## Boundary with AZ-104 knowledge

This folder does not teach portal procedures, command syntax, resource deployment steps, or administrator troubleshooting. It includes a concept only when it changes an architecture choice, responsibility boundary, failure scope, or operational trade-off.

## Continue into the repository

After these foundations, read the [Master Mental Map](../AZ-305_MASTER_MENTAL_MAP.md), then use the [Objective Map](../AZ-305_OBJECTIVE_MAP.md) to navigate the scored domains.

Official path: [AZ-305 Microsoft Azure Architect Design Prerequisites](https://learn.microsoft.com/en-us/training/paths/microsoft-azure-architect-design-prerequisites/).

---

## Navigation

| Previous | Home | Next |
|---|---|---|
| [← Repository overview](../README.md) | [AZ-305 Home](../README.md) | [Azure architecture fundamentals →](azure_architecture_fundamentals.md) |
