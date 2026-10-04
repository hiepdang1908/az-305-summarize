# Design business continuity solutions

Weight: **15–20%** of the AZ-305 blueprint.

## Study sequence

1. [Backup and disaster recovery](backup_and_disaster_recovery.md)
2. [High availability](high_availability.md)

## One consistent model

```text
High availability = keep a workload running through local failures
Backup            = recover data from deletion, corruption, or history
Disaster recovery = restore service after a major failure
Replication       = maintain additional copies; may copy bad changes
RTO               = maximum acceptable recovery time
RPO               = maximum acceptable data-loss window
```

Availability is an end-to-end workload property. The weakest dependency—identity, DNS, network entry point, compute, data, secret store, or operations—can determine the achieved RTO.

Related guides: [Relational data](../Design_data_storage_solutions/relational_data.md), [Storage](../Design_data_storage_solutions/semi_structured_and_unstructured_data.md), and [Networking](../Design_infrastructure_solutions/networking.md).
