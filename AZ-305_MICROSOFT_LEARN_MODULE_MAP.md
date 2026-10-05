# AZ-305 Microsoft Learn Module Map

Validated against the current Microsoft Learn module learning objectives on October 5, 2026. This map is separate from the [exam objective map](AZ-305_OBJECTIVE_MAP.md): Learn modules organize instruction, while the exam study guide defines scored objectives.

Key terms: infrastructure as a service (IaaS), platform as a service (PaaS), recovery time objective (RTO), and recovery point objective (RPO).

`COMPLETE` is used only when the mapped material contains explanation, selection criteria, constraints, alternatives, and architecture-level reasoning. Percentages below reserve room for SKU, regional, support-matrix, and lifecycle details that must be revalidated for a production design.

## Module summary

| Microsoft Learn module | Repository file(s) | Coverage | Confidence | Notes |
|---|---|---:|---|---|
| Design governance | `governance_and_identity_governance.md` | 98% | High | Covers hierarchy, organization drivers, tags, Policy/RBAC, effects, initiatives, custom policy, landing zones, cost, and identity governance |
| Design authentication and authorization solutions | `authentication_and_authorization.md` | 97% | High | Covers workforce/external/hybrid identity, Conditional Access, risk, lifecycle, workload identity, authorization planes, and Key Vault |
| Design a solution to log and monitor Azure resources | `logging_and_monitoring.md` | 96% | High | Covers signals, collection, workspace topology, routing, visualization, alerting, and Azure Data Explorer boundaries |
| Describe high availability and disaster recovery strategies | `high_availability.md`; `backup_and_disaster_recovery.md` | 96% | High | Separates HA, DR, backup, fault tolerance, resilience, RTO/RPO, IaaS/PaaS, and hybrid recovery |
| Design a solution for backup and disaster recovery | `backup_and_disaster_recovery.md` | 97% | High | Covers vault design, Azure Backup, supported workload categories, Site Recovery, recovery tiers, and runbooks |
| Design a data storage solution for non-relational data | `semi_structured_and_unstructured_data.md` | 97% | High | Covers accounts, Blob, Files, disks, redundancy, protection, and security decisions |
| Design a data storage solution for relational data | `relational_data.md` | 97% | High | Covers SQL service compatibility, tier/scale/availability/protection decisions, Cosmos DB, and Table Storage |
| Design data integration | `data_integration_and_analytics.md` | 96% | High | Covers Data Factory, ADLS Gen2, Databricks, Synapse, data paths, ETL/ELT, and stream processing |
| Design an Azure compute solution | `compute.md` | 97% | High | Covers the named compute services plus control, state, scaling, duration, availability, deployment, cost, and operations |
| Design an application architecture | `application_architecture.md` | 97% | High | Covers command/event/stream semantics, APIs, caching, configuration, and safe application deployment |
| Design network solutions | `networking.md` | 97% | High | Covers topology, hybrid/private connectivity, egress, routing, delivery, protection, DNS, performance, availability, and cost |
| Design migrations | `migrations.md` | 96% | High | Covers CAF/program context, assessment, strategies, tool selection, database/storage transfer, waves, rollback, and validation |

## Detailed coverage matrix

### Design governance

| Official learning objective | Repository file | Exact section | Status |
|---|---|---|---|
| Design for governance | `Design_identity_governance_and_monitoring/governance_and_identity_governance.md` | `## Governance control comparison`; `## Compliance architecture` | COMPLETE |
| Design for management groups | Same file | `## Resource hierarchy`; `## Structure decisions` | COMPLETE |
| Design for Azure subscriptions | Same file | `### Subscription decision factors` | COMPLETE |
| Design for resource groups | Same file | `### Resource-group decision factors` | COMPLETE |
| Design for resource tagging | Same file | `## Tagging strategy` | COMPLETE |
| Design for Azure Policy | Same file | `## Governance control comparison`; `### Policy design choices` | COMPLETE |
| Design for Azure role-based access control | Same file | `## Governance control comparison`; `### Important boundaries` | COMPLETE |
| Design for Azure landing zones | Same file | `## Landing zones and enterprise scale` | COMPLETE |

### Design authentication and authorization solutions

| Official learning objective | Repository file | Exact section | Status |
|---|---|---|---|
| Design for identity and access management | `Design_identity_governance_and_monitoring/authentication_and_authorization.md` | `## Identity model`; `## Authorization planes`; `## Identity governance` | COMPLETE |
| Design for Microsoft Entra ID | Same file | `## Authentication and identity choices`; `## Hybrid identity trade-offs` | COMPLETE |
| Design for Microsoft Entra business-to-business (B2B) | Same file | `## Authentication and identity choices`; `### Current CIAM terminology` | COMPLETE |
| Design for customer identity/Azure AD B2C scenarios | Same file | `### Current CIAM terminology` | COMPLETE |
| Design for Conditional Access | Same file | `### Authentication control comparison` | COMPLETE |
| Design for identity protection | Same file | `### Authentication control comparison`; `## Identity governance` | COMPLETE |
| Design for access reviews | Same file | `## Identity governance` | COMPLETE |
| Design service principals for applications | Same file | `## Workload identities` | COMPLETE |
| Design managed identities | Same file | `## Workload identities` | COMPLETE |
| Design for Azure Key Vault | Same file | `## Secrets, keys, and certificates` | COMPLETE |

### Design a solution to log and monitor Azure resources

| Official learning objective | Repository file | Exact section | Status |
|---|---|---|---|
| Design for Azure Monitor data sources | `Design_identity_governance_and_monitoring/logging_and_monitoring.md` | `## Observability model`; `## Telemetry and destination decisions` | COMPLETE |
| Design for Azure Monitor Logs (Log Analytics) workspaces | Same file | `### Workspace topology`; `### Routing decisions` | COMPLETE |
| Design for Azure Workbooks and Azure insights | Same file | `## Telemetry and destination decisions`; `## Monitoring and alerting design` | COMPLETE |
| Design for Azure Data Explorer | Same file | `### High-value comparisons`; `## When not to choose a component` | COMPLETE |

### Describe high availability and disaster recovery strategies

| Official learning objective | Repository file | Exact section | Status |
|---|---|---|---|
| Define recovery time objective and recovery point objective | `Design_business_continuity/backup_and_disaster_recovery.md` | `## Start with business objectives` | COMPLETE |
| Explore available high availability and disaster recovery options for IaaS and PaaS | `Design_business_continuity/high_availability.md` | `## Availability scopes`; `## Compute HA`; `## Relational data HA` | COMPLETE |
| Devise an appropriate high availability and disaster recovery strategy | `Design_business_continuity/backup_and_disaster_recovery.md` | `## Failure-to-control matrix`; `## Active-active versus active-passive DR`; `## Recovery runbook` | COMPLETE |

### Design a solution for backup and disaster recovery

| Official learning objective | Repository file | Exact section | Status |
|---|---|---|---|
| Design for backup and recovery | `Design_business_continuity/backup_and_disaster_recovery.md` | `## Start with business objectives`; `## Failure-to-control matrix`; `### Recovery mechanism comparisons` | COMPLETE |
| Design for Azure Backup | Same file | `## Azure Backup versus Azure Site Recovery`; `## Vault and protection design` | COMPLETE |
| Design for Azure Blob backup and recovery | Same file | `## Unstructured data protection` | COMPLETE |
| Design for Azure Files backup and recovery | Same file | `## Unstructured data protection` | COMPLETE |
| Design for Azure virtual machine backup and recovery | Same file | `## Compute backup and recovery` | COMPLETE |
| Design for Azure SQL backup and recovery | Same file | `## Database backup and recovery` | COMPLETE |
| Design for Azure Site Recovery | Same file | `## Azure Site Recovery design`; `## Recovery runbook` | COMPLETE |

### Design a data storage solution for non-relational data

| Official learning objective | Repository file | Exact section | Status |
|---|---|---|---|
| Design for data storage | `Design_data_storage_solutions/semi_structured_and_unstructured_data.md` | `## Storage decision matrix`; `## Performance and cost decisions` | COMPLETE |
| Design for Azure storage accounts | Same file | `## Storage decision matrix`; `## Storage redundancy`; `## Storage security` | COMPLETE |
| Design for Azure Blob Storage | Same file | `## Blob types and tiers`; `## Data protection layers` | COMPLETE |
| Design for data redundancy | Same file | `## Storage redundancy`; `## Data protection layers` | COMPLETE |
| Design for Azure Files | Same file | `## Azure Files decisions` | COMPLETE |
| Design an Azure disk solution | Same file | `## Managed disks` | COMPLETE |
| Design for storage security | Same file | `## Storage security`; `## Data protection layers` | COMPLETE |

### Design a data storage solution for relational data

| Official learning objective | Repository file | Exact section | Status |
|---|---|---|---|
| Design for Azure SQL Database | `Design_data_storage_solutions/relational_data.md` | `## Relational service decision matrix`; `## Azure SQL Database service and compute choices` | COMPLETE |
| Design for Azure SQL Managed Instance | Same file | `## Relational service decision matrix`; `## High availability and disaster recovery` | COMPLETE |
| Design for SQL Server on Azure Virtual Machines | Same file | `## Relational service decision matrix`; `## High availability and disaster recovery` | COMPLETE |
| Recommend a solution for database scalability | Same file | `### Compute is not data scalability`; `### Cosmos DB partitioning and consistency` | COMPLETE |
| Recommend a solution for database availability | Same file | `## High availability and disaster recovery` | COMPLETE |
| Design protection for data at rest, in transmission, and in use | Same file | `## Database data protection` | COMPLETE |
| Design for Azure Cosmos DB | Same file | `## Cosmos DB and Table Storage`; `### Cosmos DB partitioning and consistency` | COMPLETE |
| Design for Azure Table Storage | Same file | `## Cosmos DB and Table Storage` | COMPLETE |

### Design data integration

| Official learning objective | Repository file | Exact section | Status |
|---|---|---|---|
| Design a data integration solution with Azure Data Factory | `Design_data_storage_solutions/data_integration_and_analytics.md` | `## Azure Data Factory decisions`; `### ETL, ELT, orchestration, and transformation` | COMPLETE |
| Design a data integration solution with Azure Data Lake | Same file | `## Lake and lakehouse design` | COMPLETE |
| Design a data integration and analytics solution with Azure Databricks | Same file | `## Databricks versus Synapse`; `## Lake and lakehouse design` | COMPLETE |
| Design a data integration and analytics solution with Azure Synapse Analytics | Same file | `### Data Factory versus Synapse pipelines`; `## Databricks versus Synapse` | COMPLETE |
| Design strategies for hot, warm, and cold data paths | Same file | `## Hot, warm, and cold paths` | COMPLETE |
| Design an Azure Stream Analytics solution for data analysis | Same file | `## Batch versus streaming`; `## Stream processing design` | COMPLETE |

### Design an Azure compute solution

| Official learning objective | Repository file | Exact section | Status |
|---|---|---|---|
| Choose an Azure compute service | `Design_infrastructure_solutions/compute.md` | `## Compute decision matrix`; `## Decision tree`; `### Workload-dimension comparison` | COMPLETE |
| Design for Azure Virtual Machines solutions | Same file | `## Virtual machines`; `### Virtual Machine Scale Sets` | COMPLETE |
| Design for Azure Batch solutions | Same file | `## Azure Batch`; `### Focused compute comparisons` | COMPLETE |
| Design for Azure App Service solutions | Same file | `## App Service`; `### Focused compute comparisons` | COMPLETE |
| Design for Azure Container Instances solutions | Same file | `### Container Instances`; `### Focused compute comparisons` | COMPLETE |
| Design for Azure Kubernetes Service solutions | Same file | `### AKS`; `### Focused compute comparisons` | COMPLETE |
| Design for Azure Functions solutions | Same file | `## Azure Functions`; `### Focused compute comparisons` | COMPLETE |
| Design for Azure Logic Apps solutions | Same file | `## Compute decision matrix`; `### Focused compute comparisons` | COMPLETE |

### Design an application architecture

| Official learning objective | Repository file | Exact section | Status |
|---|---|---|---|
| Describe message and event scenarios | `Design_infrastructure_solutions/application_architecture.md` | `## Messages versus events`; `## Messaging and eventing matrix` | COMPLETE |
| Design a messaging solution | Same file | `## Messaging and eventing matrix`; `### Selection constraints`; `## Reliability patterns` | COMPLETE |
| Design an Azure Event Hubs messaging solution | Same file | `## Messaging and eventing matrix`; `### Selection constraints` | COMPLETE |
| Design an event-driven solution | Same file | `## Event-driven architecture` | COMPLETE |
| Design an automated app deployment solution | Same file | `## Automated application deployment` | COMPLETE |
| Design API integration | Same file | `## API integration` | COMPLETE |
| Design an application configuration management solution | Same file | `## Configuration and secrets` | COMPLETE |
| Design a caching solution | Same file | `## Caching` | COMPLETE |

### Design network solutions

| Official learning objective | Repository file | Exact section | Status |
|---|---|---|---|
| Recommend a network architecture solution based on workload requirements | `Design_infrastructure_solutions/networking.md` | `## Start with traffic flows`; `## Topology and routing` | COMPLETE |
| Design for on-premises connectivity to Azure Virtual Network | Same file | `## Hybrid connectivity matrix`; `### VPN choices`; `### ExpressRoute choices` | COMPLETE |
| Design for Azure network connectivity services | Same file | `## Topology and routing`; `## Private Endpoint versus service endpoint`; `## Internet ingress and egress` | COMPLETE |
| Design for application delivery services | Same file | `## Application delivery matrix`; `### Health and routing` | COMPLETE |
| Design for application protection services | Same file | `## Network security layers`; `### NSG versus Azure Firewall` | COMPLETE |

### Design migrations

| Official learning objective | Repository file | Exact section | Status |
|---|---|---|---|
| Evaluate migration with the Microsoft Cloud Adoption Framework for Azure | `Design_infrastructure_solutions/migrations.md` | `## Cloud Adoption Framework (CAF) focus` | COMPLETE |
| Describe the Azure Migration and Modernization Program (Azure Migration Framework) | Same file | `### Migration framework and program terminology` | COMPLETE |
| Assess your on-premises workloads | Same file | `## Discovery and assessment` | COMPLETE |
| Select a migration tool | Same file | `## Tool selection matrix` | COMPLETE |
| Migrate your databases | Same file | `## Database migration` | COMPLETE |
| Select an online storage migration tool | Same file | `## Tool selection matrix`; `## Unstructured data migration` | COMPLETE |
| Migrate offline data | Same file | `## Tool selection matrix`; `## Unstructured data migration` | COMPLETE |

## Navigation

| Previous | Home | Next |
|---|---|---|
| [← Objective map](AZ-305_OBJECTIVE_MAP.md) | [AZ-305 Home](README.md) | [Identity, governance, and monitoring →](Design_identity_governance_and_monitoring/README.md) |
