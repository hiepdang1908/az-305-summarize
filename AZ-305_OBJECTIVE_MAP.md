# AZ-305 Objective Map

Blueprint: Microsoft skills measured as of **April 17, 2026**.

Key terms: high availability (HA), disaster recovery (DR), infrastructure as a service (IaaS), platform as a service (PaaS), and application programming interface (API).

`COMPLETE` means the mapped material includes a correct explanation, use case, decision criteria, important constraints, meaningful alternatives, and AZ-305-level reasoning. Confidence reflects this audit against current Microsoft Learn/Azure documentation, not a guarantee that a product surface will never change.

| Exam domain | Official objective | Repository file | Exact heading(s) | Coverage status | Confidence |
|---|---|---|---|---|---|
| Identity, governance, monitoring | Recommend a logging solution | `Design_identity_governance_and_monitoring/logging_and_monitoring.md` | `## Logging design`; `### High-value comparisons` | COMPLETE | High |
| Identity, governance, monitoring | Recommend a solution for routing logs | `Design_identity_governance_and_monitoring/logging_and_monitoring.md` | `### Routing decisions` | COMPLETE | High |
| Identity, governance, monitoring | Recommend a monitoring solution | `Design_identity_governance_and_monitoring/logging_and_monitoring.md` | `## Monitoring and alerting design` | COMPLETE | High |
| Identity, governance, monitoring | Recommend an authentication solution | `Design_identity_governance_and_monitoring/authentication_and_authorization.md` | `## Authentication and identity choices`; `### Authentication control comparison` | COMPLETE | High |
| Identity, governance, monitoring | Recommend an identity management solution | `Design_identity_governance_and_monitoring/authentication_and_authorization.md` | `## Hybrid identity trade-offs`; `## Identity governance` | COMPLETE | High |
| Identity, governance, monitoring | Recommend a solution for authorizing access to Azure resources | `Design_identity_governance_and_monitoring/authentication_and_authorization.md` | `## Authorization planes` | COMPLETE | High |
| Identity, governance, monitoring | Recommend a solution for authorizing access to on-premises resources | `Design_identity_governance_and_monitoring/authentication_and_authorization.md` | `### Authorizing access to on-premises resources` | COMPLETE | High |
| Identity, governance, monitoring | Recommend a solution to manage secrets, certificates, and keys | `Design_identity_governance_and_monitoring/authentication_and_authorization.md` | `## Secrets, keys, and certificates` | COMPLETE | High |
| Identity, governance, monitoring | Recommend a structure for management groups, subscriptions, and resource groups, and a strategy for resource tagging | `Design_identity_governance_and_monitoring/governance_and_identity_governance.md` | `## Resource hierarchy`; `## Structure decisions`; `## Tagging strategy` | COMPLETE | High |
| Identity, governance, monitoring | Recommend a solution for managing compliance | `Design_identity_governance_and_monitoring/governance_and_identity_governance.md` | `## Governance control comparison`; `### Policy design choices`; `## Compliance architecture` | COMPLETE | High |
| Identity, governance, monitoring | Recommend a solution for identity governance | `Design_identity_governance_and_monitoring/governance_and_identity_governance.md` | `## Identity governance model` | COMPLETE | High |
| Data storage | Recommend a solution for storing relational data | `Design_data_storage_solutions/relational_data.md` | `## Relational service decision matrix` | COMPLETE | High |
| Data storage | Recommend a database service tier and compute tier | `Design_data_storage_solutions/relational_data.md` | `## Azure SQL Database service and compute choices` | COMPLETE | Medium |
| Data storage | Recommend a solution for database scalability | `Design_data_storage_solutions/relational_data.md` | `### Compute is not data scalability`; `### Cosmos DB partitioning and consistency` | COMPLETE | High |
| Data storage | Recommend a solution for data protection | `Design_data_storage_solutions/relational_data.md` | `## High availability and disaster recovery`; `## Database data protection` | COMPLETE | High |
| Data storage | Recommend a solution for storing semi-structured data | `Design_data_storage_solutions/semi_structured_and_unstructured_data.md` | `## Storage decision matrix`; `## Semi-structured data choices` | COMPLETE | High |
| Data storage | Recommend a solution for storing unstructured data | `Design_data_storage_solutions/semi_structured_and_unstructured_data.md` | `## Storage decision matrix`; `## Blob types and tiers`; `## Azure Files decisions` | COMPLETE | High |
| Data storage | Recommend a data storage solution to balance features, performance, and costs | `Design_data_storage_solutions/semi_structured_and_unstructured_data.md` | `## Performance and cost decisions`; `## When not to choose` | COMPLETE | High |
| Data storage | Recommend a data solution for protection and durability | `Design_data_storage_solutions/semi_structured_and_unstructured_data.md` | `## Storage redundancy`; `## Data protection layers`; `## Storage security` | COMPLETE | High |
| Data storage | Recommend a solution for data integration | `Design_data_storage_solutions/data_integration_and_analytics.md` | `## Service decision matrix`; `## Azure Data Factory decisions`; `### ETL, ELT, orchestration, and transformation` | COMPLETE | High |
| Data storage | Recommend a solution for data analysis | `Design_data_storage_solutions/data_integration_and_analytics.md` | `## Databricks versus Synapse`; `## Hot, warm, and cold paths`; `## Stream processing design` | COMPLETE | High |
| Business continuity | Recommend a recovery solution for Azure and hybrid workloads that meets recovery objectives | `Design_business_continuity/backup_and_disaster_recovery.md` | `## Start with business objectives`; `## Azure Site Recovery design`; `## Active-active versus active-passive DR` | COMPLETE | High |
| Business continuity | Recommend a backup and recovery solution for compute | `Design_business_continuity/backup_and_disaster_recovery.md` | `## Compute backup and recovery` | COMPLETE | High |
| Business continuity | Recommend a backup and recovery solution for databases | `Design_business_continuity/backup_and_disaster_recovery.md` | `## Database backup and recovery` | COMPLETE | Medium |
| Business continuity | Recommend a backup and recovery solution for unstructured data | `Design_business_continuity/backup_and_disaster_recovery.md` | `## Unstructured data protection` | COMPLETE | Medium |
| Business continuity | Recommend a high availability solution for compute | `Design_business_continuity/high_availability.md` | `## Compute HA`; `## Availability zones versus availability sets` | COMPLETE | High |
| Business continuity | Recommend a high availability solution for relational data | `Design_business_continuity/high_availability.md` | `## Relational data HA`; `### Synchronous versus asynchronous` | COMPLETE | High |
| Business continuity | Recommend a high availability solution for semi-structured and unstructured data | `Design_business_continuity/high_availability.md` | `## Semi-structured and unstructured data HA` | COMPLETE | High |
| Infrastructure | Specify components of a compute solution based on workload requirements | `Design_infrastructure_solutions/compute.md` | `## Compute decision matrix`; `## Decision tree`; `### Workload-dimension comparison` | COMPLETE | High |
| Infrastructure | Recommend a virtual machine-based solution | `Design_infrastructure_solutions/compute.md` | `## Virtual machines`; `### Virtual Machine Scale Sets` | COMPLETE | High |
| Infrastructure | Recommend a container-based solution | `Design_infrastructure_solutions/compute.md` | `## Container services`; `### Focused compute comparisons` | COMPLETE | High |
| Infrastructure | Recommend a serverless-based solution | `Design_infrastructure_solutions/compute.md` | `## Azure Functions`; `### Focused compute comparisons` | COMPLETE | Medium |
| Infrastructure | Recommend a compute solution for batch processing | `Design_infrastructure_solutions/compute.md` | `## Azure Batch`; `### Focused compute comparisons` | COMPLETE | High |
| Infrastructure | Recommend a messaging architecture | `Design_infrastructure_solutions/application_architecture.md` | `## Messaging and eventing matrix`; `### Selection constraints`; `## Reliability patterns` | COMPLETE | High |
| Infrastructure | Recommend an event-driven architecture | `Design_infrastructure_solutions/application_architecture.md` | `## Event-driven architecture` | COMPLETE | High |
| Infrastructure | Recommend a solution for API integration | `Design_infrastructure_solutions/application_architecture.md` | `## API integration` | COMPLETE | High |
| Infrastructure | Recommend a caching solution for applications | `Design_infrastructure_solutions/application_architecture.md` | `## Caching` | COMPLETE | Medium |
| Infrastructure | Recommend an application configuration management solution | `Design_infrastructure_solutions/application_architecture.md` | `## Configuration and secrets` | COMPLETE | High |
| Infrastructure | Recommend an automated deployment solution for applications | `Design_infrastructure_solutions/application_architecture.md` | `## Automated application deployment` | COMPLETE | High |
| Infrastructure | Evaluate a migration solution that leverages the Microsoft Cloud Adoption Framework for Azure | `Design_infrastructure_solutions/migrations.md` | `## Cloud Adoption Framework (CAF) focus`; `### Migration framework and program terminology` | COMPLETE | High |
| Infrastructure | Evaluate on-premises servers, data, and applications for migration | `Design_infrastructure_solutions/migrations.md` | `## Discovery and assessment` | COMPLETE | High |
| Infrastructure | Recommend a solution for migrating workloads to IaaS and PaaS | `Design_infrastructure_solutions/migrations.md` | `## Migration strategies`; `## Server migration`; `## Application migration` | COMPLETE | High |
| Infrastructure | Recommend a solution for migrating databases | `Design_infrastructure_solutions/migrations.md` | `## Database migration` | COMPLETE | Medium |
| Infrastructure | Recommend a solution for migrating unstructured data | `Design_infrastructure_solutions/migrations.md` | `## Unstructured data migration` | COMPLETE | Medium |
| Infrastructure | Recommend a connectivity solution that connects Azure resources to the internet | `Design_infrastructure_solutions/networking.md` | `## Internet ingress and egress`; `## Application delivery matrix` | COMPLETE | High |
| Infrastructure | Recommend a connectivity solution that connects Azure resources to on-premises networks | `Design_infrastructure_solutions/networking.md` | `## Hybrid connectivity matrix` | COMPLETE | High |
| Infrastructure | Recommend a solution to optimize network performance | `Design_infrastructure_solutions/networking.md` | `## Network performance`; `## Application delivery matrix`; `## Hybrid connectivity matrix` | COMPLETE | High |
| Infrastructure | Recommend a solution to optimize network security | `Design_infrastructure_solutions/networking.md` | `## Network security layers`; `## Private Endpoint versus service endpoint` | COMPLETE | High |
| Infrastructure | Recommend a load-balancing and routing solution | `Design_infrastructure_solutions/networking.md` | `## Application delivery matrix`; `## Topology and routing` | COMPLETE | High |


## Microsoft Learn preparation path coverage

This is a separate curriculum measure, not an exam-weighting model. The prerequisite path supports the four scored domain paths.

| Microsoft Learn path | Repository location | Coverage |
|---|---|---|
| AZ-305 architect design prerequisites | `AZ_305_architect_prerequisites/` | All six modules represented; see module mapping above |
| Design identity, governance, and monitoring solutions | `Design_identity_governance_and_monitoring/` | All published objectives mapped; depth varies by topic |
| Design data storage solutions | `Design_data_storage_solutions/` | All published objectives mapped; depth varies by topic |
| Design business continuity solutions | `Design_business_continuity/` | All published objectives mapped; depth varies by topic |
| Design infrastructure solutions | `Design_infrastructure_solutions/` | All published objectives mapped; depth varies by topic |

Path representation and objective mapping measure navigation/traceability. They do not certify complete technical coverage or candidate readiness.

## Navigation

| Previous | Home | Next |
|---|---|---|
| [← Master mental map](AZ-305_MASTER_MENTAL_MAP.md) | [AZ-305 Home](README.md) | [Identity, governance, and monitoring →](Design_identity_governance_and_monitoring/README.md) |
