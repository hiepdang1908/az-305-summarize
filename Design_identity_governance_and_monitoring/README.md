# Design identity, governance, and monitoring solutions

Weight: **25–30%** of the AZ-305 blueprint.

Foundation review: [Identity, access, and security foundations](../AZ_305_architect_prerequisites/identity_access_security_foundations.md) and [Cloud Adoption Framework](../AZ_305_architect_prerequisites/cloud_adoption_framework.md).

## Study sequence

1. [Logging and monitoring](logging_and_monitoring.md)
2. [Authentication and authorization](authentication_and_authorization.md)
3. [Governance and identity governance](governance_and_identity_governance.md)

## Architecture questions

- Which identity authenticates, and where is its lifecycle managed?
- Which authorization plane is involved: directory, Azure control plane, or service data plane?
- At which scope should access, policy, and cost controls inherit?
- Which telemetry is required, where must it be routed, and for how long?
- Which identities need just-in-time, reviewed, or package-based access?
- Can applications avoid credentials by using managed identity?

## Domain decision rules

```text
Human or workload signs in
        ↓
Microsoft Entra ID authenticates
        ↓
Conditional Access evaluates sign-in context
        ↓
Entra role / Azure role-based access control (Azure RBAC) / application or data-plane authorization
        ↓
Microsoft Entra Privileged Identity Management (PIM), access reviews, logs, and alerts govern continuing access
```

```text
Resource telemetry
        ↓
Diagnostic settings or collection configuration
        ↓
Log Analytics / Storage / Event Hubs / supported partner destination
        ↓
Queries, workbooks, alerts
        ↓
Action group or automation
```

Related cross-domain guides: [Networking](../Design_infrastructure_solutions/networking.md), [Business continuity](../Design_business_continuity/README.md), and [Master Mental Map](../AZ-305_MASTER_MENTAL_MAP.md).

---

## Navigation

| Previous | Home | Next |
|---|---|---|
| [← Objective map](../AZ-305_OBJECTIVE_MAP.md) | [AZ-305 Home](../README.md) | [Logging and monitoring →](logging_and_monitoring.md) |
