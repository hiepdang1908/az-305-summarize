# Well-Architected Framework

The Azure Well-Architected Framework evaluates workload decisions across five pillars. It is a workload design and review model, not a list of Azure products.

## Pillar decision model

| Pillar | Architecture question | AZ-305 concerns | Example decision |
|---|---|---|---|
| Reliability | What happens when a component, zone, region, dependency, or operator action fails? | Redundancy, health, failover, replication, backup, RTO/RPO, recovery testing | Zone-redundant application plus cross-region DR and historical backup |
| Security | How are confidentiality, integrity, and availability protected? | Identity, least privilege, segmentation, private access, encryption, secrets, detection | Managed identity + Key Vault + data role + private endpoint where required |
| Cost Optimization | Does the design meet requirements without unnecessary capacity or operational complexity? | Sizing, service model, elasticity, reservations, data transfer, redundancy cost | Serverless for intermittent work only when cold-start and feature limits fit |
| Operational Excellence | Can the workload be deployed, observed, operated, and changed safely? | IaC, CI/CD, monitoring, alerts, runbooks, ownership, rollback | Progressive deployment with health validation and actionable alerts |
| Performance Efficiency | Does the architecture meet latency, throughput, concurrency, and scale needs efficiently? | Scale up/out, partitioning, caching, edge delivery, load testing | Partition data and use edge/cache layers after measuring bottlenecks |

## Reliability

```text
Business impact
→ define availability target and RTO/RPO
→ identify failure modes and dependencies
→ select redundancy and recovery mechanisms
→ test failover, restore, and degraded operation
```

Ask whether the workload survives process, instance, zone, region, data-corruption, and operator failures. Availability, backup, DR, and replication address different cases.

Detailed guidance: [High availability](../Design_business_continuity/high_availability.md) and [Backup and disaster recovery](../Design_business_continuity/backup_and_disaster_recovery.md).

## Security

Start with data classification and threat model. Use explicit identity, least privilege, secure defaults, segmentation, encryption, logging, and recovery. Private connectivity reduces exposure but does not replace authentication or authorization.

Detailed guidance: [Authentication and authorization](../Design_identity_governance_and_monitoring/authentication_and_authorization.md), [Governance](../Design_identity_governance_and_monitoring/governance_and_identity_governance.md), and [Networking security](../Design_infrastructure_solutions/networking.md#network-security-layers).

## Cost Optimization

Model total cost, including operations, licenses, support, monitoring, network transfer, backups, replicas, standby regions, and migration. Optimize after mandatory requirements are met.

```text
Cheaper SKU misses required zone support
→ reject it

Active-active region has no business RTO justification
→ consider active-passive or warm standby

Continuous workload placed on consumption/serverless
→ compare provisioned capacity and operational behavior
```

## Operational Excellence

Use declarative infrastructure, immutable/versioned artifacts, workload identities, safe deployment patterns, centralized telemetry, owned alerts, runbooks, and post-incident improvement. A design that a team cannot operate reliably is not well architected.

Detailed guidance: [Logging and monitoring](../Design_identity_governance_and_monitoring/logging_and_monitoring.md), [Application deployment](../Design_infrastructure_solutions/application_architecture.md#automated-application-deployment), and [Compute deployment](../Design_infrastructure_solutions/compute.md#automated-deployment).

## Performance Efficiency

Select technology from measured access patterns and scale characteristics. Test representative load, identify the bottleneck, and scale the constrained component. Compute scale cannot repair a poor database partition key; caching cannot repair an invalid consistency requirement.

Detailed guidance: [Compute](../Design_infrastructure_solutions/compute.md), [Data storage](../Design_data_storage_solutions/README.md), and [Network performance](../Design_infrastructure_solutions/networking.md#network-performance).

## Pillar trade-offs

| Decision | Improves | Costs or risks |
|---|---|---|
| Multi-region active-active | Reliability; user latency | Cost, data consistency, deployment and operational complexity |
| Private endpoints everywhere | Exposure reduction/security | DNS, endpoint cost, network operations, troubleshooting complexity |
| Strong cross-region consistency | Data correctness | Write latency, availability, or throughput flexibility |
| Aggressive autoscale/scale-to-zero | Cost efficiency | Startup latency, capacity lag, downstream pressure |
| Central shared platform | Governance and operational consistency | Shared failure domain, team dependency, quota/congestion risk |

No pillar automatically overrides the others. Rank mandatory business and security requirements first, make trade-offs explicit, and record which risk is accepted.

## Workload review questions

1. What are the critical user journeys and measurable requirements?
2. Which failure/security/performance risks are most consequential?
3. Which architecture decisions improve one pillar while weakening another?
4. Is the operational team able to deploy, monitor, recover, and evolve the design?
5. Has actual behavior been tested under load and failure?

Official references: [Well-Architected Framework pillars](https://learn.microsoft.com/en-us/azure/well-architected/pillars), [Well-Architected assessment](https://learn.microsoft.com/en-us/assessments/azure-architecture-review/).
