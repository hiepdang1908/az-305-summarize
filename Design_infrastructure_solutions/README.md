# Design infrastructure solutions

Weight: **30–35%** of the AZ-305 blueprint.

## Study sequence

1. [Compute](compute.md)
2. [Application architecture](application_architecture.md)
3. [Migrations](migrations.md)
4. [Networking](networking.md)

## Cross-domain architecture sequence

```text
Users / systems
      ↓
Global or regional ingress + protection
      ↓
Compute model and deployment strategy
      ↓
Messaging, events, APIs, cache, configuration
      ↓
Private/public network path
      ↓
Data tier and consistency
      ↓
Zone/region availability + backup/DR
      ↓
Identity, governance, monitoring, cost
```

Select components only after ranking requirements: mandatory function, security/compliance, RTO/RPO, performance/scale, compatibility, operations, then cost.
