# Design data storage solutions

Weight: **20–25%** of the AZ-305 blueprint.

## Study sequence

1. [Relational data](relational_data.md)
2. [Semi-structured and unstructured data](semi_structured_and_unstructured_data.md)
3. [Data integration and analytics](data_integration_and_analytics.md)

## Architecture sequence

```text
Data shape and access pattern
        ↓
Engine/protocol and compatibility
        ↓
Consistency, latency, throughput, and scale
        ↓
Availability, durability, RTO, and RPO
        ↓
Identity, encryption, network isolation, and residency
        ↓
Operations and cost
```

Do not choose a database from the label "structured" or "NoSQL" alone. Start with queries, transactions, schema, partitioning, consistency, compatibility, and recovery requirements.

Related guides: [Business continuity](../Design_business_continuity/README.md), [Networking](../Design_infrastructure_solutions/networking.md), and [Migrations](../Design_infrastructure_solutions/migrations.md).
