# Design business continuity solutions

Weight: **15–20%** of the AZ-305 blueprint.

Foundation review: [Azure architecture fundamentals](../AZ_305_architect_prerequisites/azure_architecture_fundamentals.md), [Storage foundations](../AZ_305_architect_prerequisites/storage_foundations.md), and [Well-Architected Framework](../AZ_305_architect_prerequisites/well_architected_framework.md).

## Study sequence

1. [Backup and disaster recovery](backup_and_disaster_recovery.md)
2. [High availability](high_availability.md)

## One consistent model

```text
High availability = keep a workload running through local failures
Backup            = recover data from deletion, corruption, or history
Disaster recovery = restore service after a major failure
Replication       = maintain additional copies; may copy bad changes
Recovery time objective (RTO)  = maximum acceptable recovery time
Recovery point objective (RPO) = maximum acceptable data-loss window
```

Availability is an end-to-end workload property. The weakest dependency—identity, DNS, network entry point, compute, data, secret store, or operations—can determine the achieved RTO.

Related guides: [Relational data](../Design_data_storage_solutions/relational_data.md), [Storage](../Design_data_storage_solutions/semi_structured_and_unstructured_data.md), and [Networking](../Design_infrastructure_solutions/networking.md).

---

## Navigation

| Previous | Home | Next |
|---|---|---|
| [← Data integration and analytics](../Design_data_storage_solutions/data_integration_and_analytics.md) | [AZ-305 Home](../README.md) | [Backup and disaster recovery →](backup_and_disaster_recovery.md) |
