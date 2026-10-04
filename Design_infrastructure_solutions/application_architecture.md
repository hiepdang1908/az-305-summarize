# Application architecture

## Messages versus events

| Concept | Meaning | Consumer relationship |
|---|---|---|
| Command/message | Sender expects work to be performed | Often targeted; receiver owns completion/failure semantics |
| Discrete event notification | Fact that state changed | Publisher does not direct consumer behavior |
| Event stream | Ordered sequence of observations/telemetry | Consumers read/replay partitions independently |

## Messaging and eventing matrix

| Dimension | Service Bus | Event Grid | Event Hubs | Queue Storage |
|---|---|---|---|---|
| Primary pattern | Enterprise brokered commands/messages | Reactive event distribution | High-throughput event stream/telemetry log | Simple durable work queue |
| Consumer model | Competing consumers; topics/subscriptions | Push delivery or supported pull model; fan-out subscriptions | Pull/read stream by partition and consumer group | Poll/receive competing consumers |
| Ordering | Sessions support related-message ordering | No global ordering guarantee | Ordered within partition | No strict FIFO guarantee |
| Transactions | Broker transactions in supported scope | No enterprise message transaction model | Not a transactional command queue | No multi-operation broker transactions |
| Pub/sub | Topics and subscriptions | Native event subscriptions/fan-out | Multiple consumer groups read same stream | Queue only |
| Dead-letter | Built-in DLQ for queues/subscriptions | Dead-letter destination/configuration for undeliverable events | Consumer/checkpoint/error design; not a command DLQ | Dequeue-count/poison queue is application pattern |
| Replay | Message until settled/expired; duplicate detection options | Event delivery/retry window, not a long event log | Retention and replay by offset/time | Message remains until delete/expiry |
| Throughput | Enterprise messaging | High-scale discrete event routing | Very high telemetry/stream ingestion | High-scale simple queue |
| Best fit | Ordering, sessions, transactions, duplicate detection, queues/topics | Resource/domain event notification | Logs, telemetry, clickstream, IoT streaming | Low-cost asynchronous backlog |

```text
Enterprise queue/topic + transactions/sessions/DLQ → Service Bus
Discrete event notification and fan-out → Event Grid
High-throughput telemetry with partitions/replay → Event Hubs
Simple inexpensive work queue → Queue Storage
```

### Selection constraints

- Service Bus sessions require sender/receiver agreement on session IDs. Duplicate detection and transactions have tier/configuration constraints.
- Event Hubs order exists only within a partition. Choose partition key to keep related events together; more partitions improve parallelism but add design constraints.
- Event Grid retries delivery but consumers must remain idempotent. Dead-letter storage must be configured and accessible.
- Queue Storage uses visibility timeouts and pop receipts. A message can be processed more than once; design idempotently.
- Do not select on "supports topics" alone: Service Bus topics distribute commands/messages with broker semantics; Event Grid distributes event notifications.

## Reliability patterns

| Concern | Pattern |
|---|---|
| Duplicate delivery | Idempotency key, deduplication store, Service Bus duplicate detection where applicable |
| Poison message | Dead-letter/quarantine after bounded retries |
| Producer and database update must agree | Transactional outbox; avoid naive dual write |
| Consumer temporarily down | Durable broker/retention sized for outage |
| Backpressure | Queue/stream buffering plus consumer autoscale and quota monitoring |
| Ordering | Partition/session by business aggregate, avoid global ordering requirement |
| Request crosses unavailable dependency | Queue-based load leveling, retry with jitter, circuit breaker |

At-least-once delivery is common. "Exactly once" requires end-to-end application semantics, not only a broker checkbox.

## Event-driven architecture

```text
Source emits fact
       ↓
Event Grid routes notification
       ↓
Function / Logic App / webhook / supported handler
       ↓
Handler performs idempotent action
       ↓
Failure → retry → dead-letter → alert/reprocess
```

Use Event Grid for discrete changes such as object creation or business-domain events. Use Event Hubs when the event stream itself is the dataset and consumers need partitioned ingestion/replay. Use Service Bus when work must be completed with brokered command semantics.

## API integration

Azure API Management (APIM) provides an API gateway, policy layer, developer experience, and management plane.

| Requirement | APIM capability/direction |
|---|---|
| Stable facade over changing backends | Gateway routing and revisions/versions |
| Authenticate/authorize callers | Validate tokens/certificates/subscriptions with policies; backend still enforces its trust model |
| Rate-limit or quota consumers | Policies |
| Transform headers/payload/protocol shape | Policies, while avoiding excessive business logic in gateway |
| Internal/private APIs | VNet/private endpoint/service tier topology according to requirements |
| Multi-region gateway availability | Supported premium/managed gateway topology or self-hosted gateway; validate tier |
| Partner/developer onboarding | Developer portal and products/subscriptions where appropriate |
| Govern APIs across non-Azure environments | Self-hosted gateway under APIM control plane where supported |

APIM is not a web application firewall, general load balancer, or message broker. Common edge chain:

```text
Internet → Front Door + WAF → APIM → private application/API → data
```

Application Gateway + WAF can be used for regional/private HTTP ingress. Avoid duplicating TLS, routing, and policy layers without a requirement.

## Caching

Azure Managed Redis is the current managed Redis offering for new architecture decisions. Azure Cache for Redis is on a published retirement path; migration timing varies by tier, so validate current Microsoft lifecycle dates.

| Pattern | Use | Risk/control |
|---|---|---|
| Cache-aside | App reads cache, on miss reads source and populates | Stale data; expiration/invalidation |
| Write-through | Update cache and source in write path | Coupling and partial failure |
| Distributed session | Share session state across stateless app instances | Cache availability/persistence becomes critical |
| Rate limit/counter | Low-latency atomic data structures | Durability expectations must match Redis configuration |

Decision rules:

- Cache data that is expensive/frequent to compute or read and tolerates bounded staleness.
- Keep source of truth in durable storage unless Redis durability architecture explicitly satisfies the workload.
- Prevent cache stampede with request coalescing, jittered expiration, or background refresh.
- Size for memory overhead and eviction policy, not only raw dataset.
- Choose clustering, HA, persistence, zone, and active geo-replication options based on supported Azure Managed Redis tier/features.
- Private networking and Entra authentication reduce exposure; clients still require resilient connection/retry logic.

Do not add cache where hit rate is low, consistency must be immediate, or invalidation complexity exceeds backend benefit.

## Configuration and secrets

| Data | Service | Reason |
|---|---|---|
| Non-secret application settings, feature flags, key-values | Azure App Configuration | Central configuration, labels, feature management, refresh patterns |
| Passwords, connection strings, private keys, certificates | Key Vault | Protected secret/key/certificate lifecycle and audit |
| Deployment-specific immutable config | Environment variables/IaC/app settings as appropriate | Simple and versioned with release |

App Configuration can reference Key Vault values; the application identity needs access to both. Central configuration improves consistency but creates a runtime dependency, so use client caching, last-known-good values, retry, and regional design. Feature flags are operational controls, not authorization controls.

## Automated application deployment

| Workload | Safe deployment direction |
|---|---|
| App Service/Functions | Deployment slot, warm-up, validation, swap, rollback |
| Container Apps | Revisions and weighted traffic |
| AKS | Rolling, blue-green, or canary with probes and disruption budgets |
| VM/VMSS | Immutable image and rolling/health-aware upgrade |
| API | APIM revisions for nonbreaking changes; versions for breaking API contracts |

Pipeline architecture:

```text
Source → build/test/scan → immutable artifact
       → deploy infrastructure/config
       → deploy to nonproduction/staging
       → health/security validation
       → progressive production traffic
       → observe → promote or rollback
```

Use separate service connections/identities per environment, approvals for high-risk stages, workload identity federation, and auditable artifacts. Database migrations must be backward-compatible during rolling/slot deployments.

## Cost and operations

- Service Bus premium/dedicated capacity is justified by predictable performance, isolation, or feature needs; simple queues can cost less.
- Event Hubs cost follows throughput/capacity, retention, capture, and dedicated features.
- Event Grid fits pay-per-event notification; it is inefficient as a telemetry-stream replacement.
- APIM tier/topology drives cost and feature availability; consumption fits bursty simple gateways but may not meet network/availability requirements.
- Cache adds cost and another failure mode; quantify database load/latency benefit.

## Common Trap

- Event Grid is not the service for high-throughput stream replay.
- Event Hubs is not a replacement for transactional enterprise queues.
- Storage Queue has no Service Bus topics, sessions, or broker transactions.
- APIM manages API concerns; WAF filters web attacks; they can be complementary.
- App Configuration is not Key Vault.
- A cache is not automatically durable or authoritative.
- CI/CD automation without health validation and rollback is not a safe deployment architecture.

Official references: [Azure messaging service comparison](https://learn.microsoft.com/en-us/azure/service-bus-messaging/compare-messaging-services), [Service Bus overview](https://learn.microsoft.com/en-us/azure/service-bus-messaging/service-bus-messaging-overview), [Event Grid overview](https://learn.microsoft.com/en-us/azure/event-grid/overview), [Event Hubs overview](https://learn.microsoft.com/en-us/azure/event-hubs/event-hubs-about), [API Management overview](https://learn.microsoft.com/en-us/azure/api-management/api-management-key-concepts), [Azure Managed Redis overview](https://learn.microsoft.com/en-us/azure/azure-cache-for-redis/managed-redis/managed-redis-overview), [App Configuration overview](https://learn.microsoft.com/en-us/azure/azure-app-configuration/overview).
