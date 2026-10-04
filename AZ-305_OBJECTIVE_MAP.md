# AZ-305 Objective Map

Blueprint: Microsoft skills measured as of **April 17, 2026**.

| Exam domain | Skill measured | Repository file | Section |
|---|---|---|---|
| Identity, governance, monitoring | Recommend a logging solution | `Design_identity_governance_and_monitoring/logging_and_monitoring.md` | Logging design |
| Identity, governance, monitoring | Recommend a solution for routing logs | `Design_identity_governance_and_monitoring/logging_and_monitoring.md` | Routing decisions |
| Identity, governance, monitoring | Recommend a monitoring solution | `Design_identity_governance_and_monitoring/logging_and_monitoring.md` | Monitoring and alerting design |
| Identity, governance, monitoring | Recommend an authentication solution | `Design_identity_governance_and_monitoring/authentication_and_authorization.md` | Authentication and identity choices |
| Identity, governance, monitoring | Recommend an identity management solution | `Design_identity_governance_and_monitoring/authentication_and_authorization.md` | Hybrid identity trade-offs; Identity governance |
| Identity, governance, monitoring | Recommend a solution for authorizing access to Azure resources | `Design_identity_governance_and_monitoring/authentication_and_authorization.md` | Authorization planes |
| Identity, governance, monitoring | Recommend a solution for authorizing access to on-premises resources | `Design_identity_governance_and_monitoring/authentication_and_authorization.md` | Authorizing access to on-premises resources; Hybrid identity trade-offs |
| Identity, governance, monitoring | Recommend a solution to manage secrets, certificates, and keys | `Design_identity_governance_and_monitoring/authentication_and_authorization.md` | Secrets, keys, and certificates |
| Identity, governance, monitoring | Recommend management-group, subscription, resource-group structure and tagging | `Design_identity_governance_and_monitoring/governance_and_identity_governance.md` | Resource hierarchy; Structure decisions; Tagging strategy |
| Identity, governance, monitoring | Recommend a solution for managing compliance | `Design_identity_governance_and_monitoring/governance_and_identity_governance.md` | Governance control comparison; Compliance architecture |
| Identity, governance, monitoring | Recommend a solution for identity governance | `Design_identity_governance_and_monitoring/governance_and_identity_governance.md` | Identity governance model |
| Data storage | Recommend a solution for storing relational data | `Design_data_storage_solutions/relational_data.md` | Relational service decision matrix |
| Data storage | Recommend a database service tier and compute tier | `Design_data_storage_solutions/relational_data.md` | Azure SQL Database service and compute choices |
| Data storage | Recommend a solution for database scalability | `Design_data_storage_solutions/relational_data.md` | Compute is not data scalability; Cosmos DB partitioning and consistency |
| Data storage | Recommend a solution for relational data protection | `Design_data_storage_solutions/relational_data.md` | High availability and disaster recovery; Database data protection |
| Data storage | Recommend a solution for storing semi-structured data | `Design_data_storage_solutions/semi_structured_and_unstructured_data.md` | Storage decision matrix; Semi-structured data choices |
| Data storage | Recommend a solution for storing unstructured data | `Design_data_storage_solutions/semi_structured_and_unstructured_data.md` | Storage decision matrix; Blob types and tiers; Azure Files decisions |
| Data storage | Recommend storage balancing features, performance, and cost | `Design_data_storage_solutions/semi_structured_and_unstructured_data.md` | Performance and cost decisions |
| Data storage | Recommend a semi/unstructured data solution for protection and durability | `Design_data_storage_solutions/semi_structured_and_unstructured_data.md` | Storage redundancy; Data protection layers |
| Data storage | Recommend a solution for data integration | `Design_data_storage_solutions/data_integration_and_analytics.md` | Service decision matrix; Azure Data Factory decisions |
| Data storage | Recommend a solution for data analysis | `Design_data_storage_solutions/data_integration_and_analytics.md` | Databricks versus Synapse; Hot, warm, and cold paths; Stream processing design |
| Business continuity | Recommend a recovery solution for Azure and hybrid workloads meeting recovery objectives | `Design_business_continuity/backup_and_disaster_recovery.md` | Start with business objectives; Azure Site Recovery design; Active-active versus active-passive DR |
| Business continuity | Recommend a backup and recovery solution for compute | `Design_business_continuity/backup_and_disaster_recovery.md` | Compute backup and recovery |
| Business continuity | Recommend a backup and recovery solution for databases | `Design_business_continuity/backup_and_disaster_recovery.md` | Database backup and recovery |
| Business continuity | Recommend a backup and recovery solution for unstructured data | `Design_business_continuity/backup_and_disaster_recovery.md` | Unstructured data protection |
| Business continuity | Recommend a high availability solution for compute | `Design_business_continuity/high_availability.md` | Compute HA; Availability zones versus availability sets |
| Business continuity | Recommend a high availability solution for relational data | `Design_business_continuity/high_availability.md` | Relational data HA |
| Business continuity | Recommend a high availability solution for semi-structured and unstructured data | `Design_business_continuity/high_availability.md` | Semi-structured and unstructured data HA |
| Infrastructure | Specify components of a compute solution based on workload requirements | `Design_infrastructure_solutions/compute.md` | Compute decision matrix; Decision tree |
| Infrastructure | Recommend a virtual machine-based solution | `Design_infrastructure_solutions/compute.md` | Virtual machines; Virtual Machine Scale Sets |
| Infrastructure | Recommend a container-based solution | `Design_infrastructure_solutions/compute.md` | Container services |
| Infrastructure | Recommend a serverless-based solution | `Design_infrastructure_solutions/compute.md` | Azure Functions; Container services; Compute decision matrix |
| Infrastructure | Recommend a compute solution for batch processing | `Design_infrastructure_solutions/compute.md` | Azure Batch |
| Infrastructure | Recommend a messaging architecture | `Design_infrastructure_solutions/application_architecture.md` | Messaging and eventing matrix; Reliability patterns |
| Infrastructure | Recommend an event-driven architecture | `Design_infrastructure_solutions/application_architecture.md` | Event-driven architecture |
| Infrastructure | Recommend a solution for API integration | `Design_infrastructure_solutions/application_architecture.md` | API integration |
| Infrastructure | Recommend a caching solution for applications | `Design_infrastructure_solutions/application_architecture.md` | Caching |
| Infrastructure | Recommend an application configuration management solution | `Design_infrastructure_solutions/application_architecture.md` | Configuration and secrets |
| Infrastructure | Recommend an automated deployment solution for applications | `Design_infrastructure_solutions/application_architecture.md` | Automated application deployment |
| Infrastructure | Evaluate a migration solution that uses the Cloud Adoption Framework | `Design_infrastructure_solutions/migrations.md` | Cloud Adoption Framework focus; Migration is an architecture decision |
| Infrastructure | Evaluate on-premises servers, data, and applications for migration | `Design_infrastructure_solutions/migrations.md` | Discovery and assessment |
| Infrastructure | Recommend a solution for migrating workloads to IaaS and PaaS | `Design_infrastructure_solutions/migrations.md` | Server migration; Application migration; Migration strategies |
| Infrastructure | Recommend a solution for migrating databases | `Design_infrastructure_solutions/migrations.md` | Database migration |
| Infrastructure | Recommend a solution for migrating unstructured data | `Design_infrastructure_solutions/migrations.md` | Unstructured data migration |
| Infrastructure | Recommend internet connectivity for Azure resources | `Design_infrastructure_solutions/networking.md` | Internet ingress and egress; Application delivery matrix |
| Infrastructure | Recommend connectivity between Azure and on-premises networks | `Design_infrastructure_solutions/networking.md` | Hybrid connectivity matrix |
| Infrastructure | Recommend a solution to optimize network performance | `Design_infrastructure_solutions/networking.md` | Network performance |
| Infrastructure | Recommend a solution to optimize network security | `Design_infrastructure_solutions/networking.md` | Network security layers; Private Endpoint versus service endpoint |
| Infrastructure | Recommend a load-balancing and routing solution | `Design_infrastructure_solutions/networking.md` | Application delivery matrix; Topology and routing |

## Coverage audit

| Domain | Covered | Total | Status |
|---|---:|---:|---|
| Identity, governance, and monitoring | 11 | 11 | COMPLETE |
| Data storage | 10 | 10 | COMPLETE |
| Business continuity | 7 | 7 | COMPLETE |
| Infrastructure | 21 | 21 | COMPLETE |

```text
Coverage status:
Covered objectives: 49
Total objectives: 49
Coverage: 49/49
```

Coverage means each official objective maps to substantive content. It does not freeze product availability, regional support, quotas, licensing, pricing, or preview/GA state.

## Microsoft Learn preparation path coverage

This is a separate curriculum measure, not an exam-weighting model. The prerequisite path supports the four scored domain paths.

| Microsoft Learn path | Repository location | Coverage |
|---|---|---|
| AZ-305 architect design prerequisites | `AZ_305_architect_prerequisites/` | COMPLETE |
| Design identity, governance, and monitoring solutions | `Design_identity_governance_and_monitoring/` | COMPLETE |
| Design data storage solutions | `Design_data_storage_solutions/` | COMPLETE |
| Design business continuity solutions | `Design_business_continuity/` | COMPLETE |
| Design infrastructure solutions | `Design_infrastructure_solutions/` | COMPLETE |

```text
Exam blueprint coverage: 49/49 objectives
Microsoft Learn path coverage: 5/5 paths
```
