# Logging and monitoring

## Observability model

Azure Monitor is the platform service for collecting, analyzing, visualizing, and acting on telemetry from Azure and hybrid resources. It is not one database: different data types use different collection paths and stores.

```text
Resource / application / guest OS / control plane
        ↓
Platform collection, diagnostic settings, Application Insights,
Azure Monitor Agent + Data Collection Rules
        ↓
Metrics store | Log Analytics workspace | Storage | Event Hubs
        ↓
Metrics Explorer / KQL / Workbooks / Insights / external SIEM
        ↓
Metric, log-search, activity-log, resource-health, or smart alert
        ↓
Action group: notification, webhook, ITSM, function, logic app, automation
```

## Telemetry and destination decisions

| Signal or component | What it represents | Choose it when | Important boundary |
|---|---|---|---|
| Azure Monitor metrics | Numeric, time-series platform/application measurements | Low-latency detection, charts, threshold or dynamic alerts | Not a substitute for detailed event records |
| Azure Activity Log | Subscription-level control-plane events and service health | Who changed/deleted a resource, policy/administrative events | Not guest OS, application, or resource data-plane telemetry |
| Resource logs | Service-specific operations emitted by an Azure resource | Auditing or troubleshooting a service's data/control operations | Usually must be routed with diagnostic settings |
| Log Analytics workspace | Managed log store queried with KQL | Correlation, operational investigation, alerting, workbooks | Workspace design affects access, residency, retention, and cost |
| Application Insights | Application performance management built on Azure Monitor | Requests, dependencies, exceptions, traces, availability, distributed tracing | Prefer workspace-based deployments; instrument the application |
| Azure Monitor Agent (AMA) | Guest OS telemetry collector | Windows/Linux logs and performance data from Azure or Arc machines | Data Collection Rules define what is collected and where it goes |
| Data Collection Rule (DCR) | Collection, transformation, and destination configuration | Reuse filtered collection rules and control ingestion | It does not replace service diagnostic settings for platform resource logs |
| Diagnostic settings | Resource routing configuration | Send supported platform logs/metrics to Log Analytics, Storage, Event Hubs, or partners | Availability and categories are resource-type dependent |
| Workbooks | Interactive, query-driven visualization | Shared operational reports across metrics/logs/resources | A visualization layer, not a telemetry store |
| Azure Monitor Insights | Curated monitoring experiences | Rapid workload-specific views such as VM, container, network, or application insights | Coverage and prerequisites vary by workload |
| Azure Data Explorer | High-scale, low-latency analytics over telemetry/time-series data | Custom analytics platform with very large ingestion or retention needs | More engineering/operations than Log Analytics for routine Azure monitoring |

## Logging design

### Workspace topology

| Requirement | Recommended direction |
|---|---|
| Central security operations and cross-workload queries | Central or regional workspaces with controlled access and standardized collection |
| Strict data residency or sovereign boundary | Workspace in the required geography; confirm feature and retention availability |
| Separate billing or operational ownership | Separate workspaces where the separation benefit exceeds query/access complexity |
| Workload team must access only its logs | Resource-context access and Azure RBAC where supported; separate workspace if hard isolation is required |
| Long-term, low-cost retention or immutable archive | Route to Storage and configure retention/immutability as required |
| External SIEM or near-real-time stream processing | Route supported logs to Event Hubs |
| Fast operational querying and alerting | Log Analytics workspace |

Avoid one workspace per resource. Also avoid a single global workspace without checking residency, access, ingestion, and regional-dependency requirements. Centralization improves correlation; separation improves isolation and ownership.

### Routing decisions

| Requirement | Direction |
|---|---|
| Query and correlate with KQL | Log Analytics |
| Archive for audit or later processing | Storage account |
| Stream to SIEM or custom consumer | Event Hubs |
| Retain Activity Log beyond its default platform retention | Export through a subscription diagnostic setting |
| Reduce ingestion of noisy guest data | DCR filtering/transformation where supported |
| Private ingestion/query paths | Evaluate Azure Monitor Private Link Scope and private endpoints; validate DNS and service support |

Diagnostic settings are independent per resource and do not retroactively collect events generated before configuration. Use Azure Policy to deploy them consistently, while accounting for resource-specific log categories.

## Monitoring and alerting design

| Requirement | Recommended direction |
|---|---|
| Fast numeric threshold or platform health signal | Metric alert |
| Condition derived from KQL across logs | Log-search alert |
| Administrative event, service health, or resource health | Activity Log alert |
| Application latency, failure, dependency, or trace correlation | Application Insights |
| Standard response reused by many alerts | Action group |
| Multi-step remediation | Logic Apps, Functions, Automation, or incident-management integration from the action group |

Design alerts around user-impacting symptoms and actionable causes. Define severity, ownership, deduplication, suppression, escalation, and runbooks. An alert without an owner or response is telemetry noise.

## Cost, security, and operations

- Major cost drivers: ingestion volume, query volume, retention, export, and duplicate collection.
- Collect required data, not every available category. Separate operational retention from compliance archive.
- Use least privilege for query and configuration access. Workspace access can expose data from many resources.
- Scrub or avoid sensitive fields before ingestion when possible; monitoring platforms are not secret stores.
- Treat monitoring-region failure as an architecture dependency. Critical cross-region workloads can require resilient collection and an external notification path.
- Use Service Health for Azure incidents affecting subscriptions, Resource Health for an individual resource, and workload telemetry for end-user behavior.

## When not to choose a component

- Do not choose Activity Log to diagnose application exceptions.
- Do not choose metrics when individual audit records or request details are required.
- Do not choose Storage alone when interactive KQL investigation and Azure Monitor log alerts are required.
- Do not choose Azure Data Explorer merely to replace a normal Log Analytics deployment; use it when custom scale, control, or analytics justifies the additional platform.
- Do not use Application Insights as an infrastructure inventory or compliance-policy engine.

## Common Trap

- Azure Monitor is the umbrella service; Log Analytics is a log-query/store capability within the monitoring architecture.
- Metrics, Activity Log, resource logs, and application telemetry are different signals.
- Enabling a diagnostic setting routes supported platform telemetry; it does not automatically instrument application code or collect arbitrary guest OS logs.
- Alerts detect and initiate responses; action groups define reusable notification/action targets.
- A dashboard or workbook displays data but does not collect or retain it.

## Decision rules

```text
Control-plane change → Activity Log
Service-specific platform event → resource log + diagnostic setting
Guest OS event/performance → AMA + DCR
Request/dependency/exception → Application Insights
KQL correlation → Log Analytics workspace
Massive custom telemetry analytics → evaluate Azure Data Explorer
```

Official references: [Azure Monitor overview](https://learn.microsoft.com/en-us/azure/azure-monitor/fundamentals/overview), [Azure Monitor data sources](https://learn.microsoft.com/en-us/azure/azure-monitor/fundamentals/data-sources), [Diagnostic settings](https://learn.microsoft.com/en-us/azure/azure-monitor/platform/diagnostic-settings), [Log Analytics workspace architecture](https://learn.microsoft.com/en-us/azure/azure-monitor/logs/workspace-design), [Azure Monitor alerts](https://learn.microsoft.com/en-us/azure/azure-monitor/alerts/alerts-overview).
