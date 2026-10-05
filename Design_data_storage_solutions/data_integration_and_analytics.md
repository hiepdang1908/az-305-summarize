# Data integration and analytics

## Data-path model

```text
Sources
  ├── batch/files/databases → orchestration and copy → lake/warehouse
  └── events/telemetry      → ingestion broker       → stream processing
                                                        ↓
                         serving / business intelligence (BI) / machine learning (ML)
```

Separate ingestion, storage, transformation, serving, and orchestration. One service may cover several stages, but that does not eliminate the design decisions.

## Service decision matrix

| Service | Primary role | Choose when | Not primarily for |
|---|---|---|---|
| Azure Data Factory | Managed data integration/orchestration and copy | Hybrid connectors, scheduled pipelines, data movement, mapping data flows | Low-latency event broker or interactive BI engine |
| Azure Data Lake Storage Gen2 | Durable analytics storage | Open files, hierarchical namespace, multiple engines, lake/lakehouse foundation | Pipeline orchestration or compute |
| Azure Databricks | Apache Spark-based lakehouse/data engineering/machine learning | Complex transformations, notebooks, Spark ecosystem, collaborative data/artificial intelligence (AI) engineering | Simple copy-only pipelines |
| Azure Synapse Analytics | Integrated enterprise analytics workspace | SQL analytics, Spark, pipelines, warehouse/lake integration | Operational online transaction processing (OLTP) database |
| Azure Stream Analytics | Managed real-time stream processing with SQL-like queries | Windowing, filtering, joins, aggregations, low-operations stream jobs | Durable event broker or broad batch orchestration |
| Event Hubs | High-throughput event-stream ingestion | Telemetry/log streams, partitions, consumer groups, replay within retention | Enterprise commands/transactions |
| Microsoft Fabric | Software as a service (SaaS) analytics platform spanning ingestion, lake, engineering, warehouse, and business intelligence | Organization wants integrated SaaS analytics and OneLake/Power BI experience | Workloads requiring Azure-resource-level control not offered by SaaS model |

The current AZ-305 Learn module emphasizes Data Factory, Data Lake, Databricks, Synapse, and Stream Analytics. Fabric may be relevant to a current production decision, but validate the current exam blueprint before treating it as an exam replacement for named services.

### Integration, messaging, and analytics boundaries

| Requirement family | Primary direction | Eliminate when |
|---|---|---|
| Scheduled/hybrid data copy and workflow orchestration | Data Factory or Synapse pipelines | Subsecond broker delivery or transactional command semantics are mandatory |
| Enterprise command/message delivery | Service Bus | The workload is data movement, analytical transformation, or telemetry replay |
| Discrete event notification and fan-out | Event Grid | Consumers need a durable partitioned event log or enterprise command broker |
| High-throughput event ingestion and replay | Event Hubs | Per-message transactions, sessions, and command completion are mandatory |
| Windowed streaming transformation | Stream Analytics or another stream engine | The need is durable ingestion only; retain Event Hubs/lake separately |

Do not select an analytics pipeline to act as an operational message broker. Detailed broker semantics are in [Application architecture](../Design_infrastructure_solutions/application_architecture.md#messaging-and-eventing-matrix).

## Batch versus streaming

### ETL, ELT, orchestration, and transformation

| Concept | Flow/responsibility | Choose when |
|---|---|---|
| Extract, transform, load (ETL) | Transform before loading the serving target | Target requires curated shape on arrival, transformation must occur outside it, or sensitive fields must be removed before load |
| Extract, load, transform (ELT) | Land source data, then transform with lake/warehouse/lakehouse compute | Scalable target compute and retained raw data enable replay, multiple models, or iterative analytics |
| Orchestration | Schedules, coordinates, retries, and observes activities/dependencies | The problem is workflow/control flow across copy and compute steps |
| Transformation | Changes schema, quality, aggregation, or business meaning of data | The problem is computation over the data itself |

Azure Data Factory and Synapse pipelines primarily orchestrate and move data; Mapping Data Flows or invoked Databricks/Synapse/database compute perform transformations. A pipeline calling compute does not make orchestration and transformation the same responsibility.

| Requirement | Direction |
|---|---|
| Minutes/hours acceptable, bounded dataset, scheduled processing | Batch |
| Continuous telemetry, seconds-level decisions, unbounded events | Streaming |
| Both historical correctness and immediate insight | Hot path plus cold/batch path |
| Reprocess when transformation logic changes | Retain raw immutable data and replay from broker/lake |

```text
Operational source → Data Factory copy/orchestration → Azure Data Lake Storage Gen2 (ADLS Gen2) raw zone
ADLS raw → Databricks/Synapse transformation → curated/serving zone

Producers → Event Hubs → Stream Analytics/Databricks streaming
                          ├── hot output → alert/dashboard/store
                          └── capture/raw → lake → later batch analysis
```

## Azure Data Factory decisions

- Integration Runtime is the compute bridge for data movement/activity dispatch. Choose Azure, self-hosted, or managed virtual network patterns according to source location and network isolation.
- Use self-hosted integration runtime to reach private/on-premises sources when appropriate; design its availability and credentials.
- Pipelines orchestrate activities; linked services describe connection information; datasets describe data shape/location.
- Copy Activity moves data. Mapping Data Flows provide managed Spark transformations. External compute activities can invoke Databricks, Synapse, stored procedures, and other services.
- Use triggers for schedule, tumbling-window, or event-driven starts as supported.
- Store secrets in Key Vault and use managed identity where supported.

Do not use Azure Data Factory (ADF) as an operational message broker. Pipeline startup and batch semantics usually do not satisfy low-latency event processing.

### Data Factory versus Synapse pipelines

Both use closely related pipeline and integration-runtime concepts. Choose Data Factory when data integration/orchestration is the primary standalone platform and broad integration features or reusable integration-runtime topology drive the design. Choose Synapse pipelines when orchestration belongs inside an existing Synapse workspace with its SQL/Spark analytics lifecycle. Validate current feature differences rather than assuming artifact parity or frictionless migration between them.

## Lake and lakehouse design

| Zone | Purpose | Controls |
|---|---|---|
| Raw/landing | Preserve source fidelity and replay | Append/immutable patterns, limited writers, retention |
| Validated/standardized | Correct schema/quality and common formats | Quality checks, lineage, partition design |
| Curated/serving | Consumer-optimized tables/files | Business semantics, performance, governed access |

Use columnar formats such as Parquet for analytical scans where suitable. Partition by common selective predicates but avoid extremely high-cardinality directory layouts and tiny files. The lake's low-cost durability does not by itself supply transactions, governance, catalog, or fast SQL serving.

## Databricks versus Synapse

| Dimension | Azure Databricks | Azure Synapse Analytics |
|---|---|---|
| Center of gravity | Lakehouse, Spark, data/AI engineering | Integrated SQL/Spark/pipelines analytics workspace |
| Developer experience | Collaborative notebooks, jobs, Spark/Delta ecosystem | Synapse Studio, SQL pools, Spark pools, pipelines |
| SQL warehouse | Databricks SQL | Dedicated/serverless SQL capabilities |
| Best signal | Deep Spark/lakehouse/ML ecosystem need | SQL warehouse and integrated Azure analytics workspace need |
| Trade-off | Platform skill/cost/governance design | Multiple compute engines and workload management choices |

The services can coexist. Choose based on operating model, existing skills, governance, performance tests, SQL/Spark mix, and integration rather than assuming one is universally superior.

## Hot, warm, and cold paths

| Path | Latency | Typical implementation | Trade-off |
|---|---|---|---|
| Hot | Seconds or less | Event Hubs + Stream Analytics/streaming engine + operational sink | Higher always-on cost; limited time for complex processing |
| Warm | Minutes | Micro-batch/Spark, near-real-time warehouse/lakehouse | Balance freshness and cost |
| Cold | Hours/days or on demand | ADLS + batch transformation | Lowest urgency/cost; longest insight delay |

Data temperature is a business/access property, not only a Blob tier. Archive Blob cannot directly satisfy analytical queries until rehydrated.

## Stream processing design

- Partition input for parallelism and preserve related-event ordering only within the relevant partition.
- Define event time versus processing time, late-arrival tolerance, out-of-order handling, and window type.
- Check output idempotency and duplicate behavior; end-to-end exactly-once claims require every stage to support the semantics.
- Retain/capture raw events when replay, audit, or model retraining is required.
- Use separate consumer groups so consumers track positions independently.
- Scale ingestion and processing independently; a fast broker does not prevent a slow consumer backlog.

## Security, reliability, and cost

- Use managed identities and least-privilege data roles. Avoid embedding storage keys in notebooks/pipelines.
- Private endpoints require end-to-end DNS/network design across data sources, integration runtime, compute, and sinks.
- Classify data, minimize copies, and govern catalogs/lineage/retention.
- Define restart, checkpoint, retry, idempotency, dead-letter/quarantine, and replay behavior.
- Major cost drivers include data movement, always-on clusters/pools, SQL compute, streaming units, orchestration activity, storage transactions, and duplicate datasets.
- Separate storage from compute when independent scaling and pause/resume economics are important.

## Common Trap

- Event Hubs ingests and retains streams; Stream Analytics processes them.
- Data Factory orchestrates/moves data; it is not a stream broker.
- Data Lake Storage is storage, not an analytics engine.
- A hot/cold architecture describes processing latency as well as storage temperature.
- "Real time" must be quantified; seconds, minutes, and hours lead to different designs.
- A readable analytics replica does not automatically solve data transformation or governance.

Official references: [Data Factory introduction](https://learn.microsoft.com/en-us/azure/data-factory/introduction), [Data Factory versus Synapse pipelines](https://learn.microsoft.com/en-us/azure/synapse-analytics/data-integration/concepts-data-factory-differences), [ADLS Gen2 introduction](https://learn.microsoft.com/en-us/azure/storage/blobs/data-lake-storage-introduction), [Azure Databricks documentation](https://learn.microsoft.com/en-us/azure/databricks/), [Azure Synapse overview](https://learn.microsoft.com/en-us/azure/synapse-analytics/overview-what-is), [Stream Analytics overview](https://learn.microsoft.com/en-us/azure/stream-analytics/stream-analytics-introduction), [Event Hubs overview](https://learn.microsoft.com/en-us/azure/event-hubs/event-hubs-about).

---

## Navigation

| Previous | Home | Next |
|---|---|---|
| [← Semi-structured and unstructured data](semi_structured_and_unstructured_data.md) | [Domain home](README.md) | [Business continuity solutions →](../Design_business_continuity/README.md) |
