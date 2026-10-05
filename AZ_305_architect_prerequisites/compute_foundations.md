# Compute foundations

## Responsibility model

Compute selection is primarily a decision about required control, workload shape, scale unit, and operational responsibility.

| Model | Customer control | Microsoft manages | Common architect reason |
|---|---|---|---|
| Infrastructure as a service (IaaS) | Guest operating system (OS), runtime, middleware, application, patch/configuration strategy | Physical datacenter, host, virtualization fabric | Legacy compatibility, custom OS/software, specialized hardware, rehost |
| Managed application platform as a service (PaaS) | Application and supported runtime/configuration choices | OS, platform patching, host placement, much of scaling/availability | Reduce platform operations for web/API workloads |
| Containers | Image and application dependencies; orchestration responsibility varies by service | Host and, for managed platforms, portions of orchestration | Portable packaging and microservice/job isolation |
| Serverless | Function/workflow/container logic and external state | Instance provisioning and event-based scale within plan limits | Bursty or event-driven execution with minimal infrastructure management |

PaaS and serverless trade infrastructure control for service constraints. Containers package software consistently but do not determine the orchestrator or remove operational responsibility.

## Foundational service comparison

| Service | Compute model | Conceptual use | Differentiating requirement |
|---|---|---|---|
| Virtual Machines | IaaS | Individually controlled Windows/Linux hosts | Full OS/software control or rehost compatibility |
| Virtual Machine Scale Sets | IaaS fleet | Model-managed homogeneous VM instances | Horizontal VM scaling and coordinated fleet lifecycle |
| App Service | Application PaaS | Managed HTTP web applications and APIs | Supported web runtime/container without Kubernetes control |
| Azure Functions | Serverless code | Triggered functions, timers, event processing | Event-driven execution and plan-based automatic scaling |
| Azure Container Apps | Managed/serverless containers | APIs, microservices, workers, and jobs | Container model without direct Kubernetes management |
| Azure Kubernetes Service (AKS) | Managed Kubernetes | Kubernetes-orchestrated applications | Kubernetes API, ecosystem, scheduling, or extensibility is mandatory |
| Azure Container Instances (ACI) | Direct container groups | Simple isolated or short-lived containers | Fast container execution without a full application platform |
| Azure Batch | Managed batch scheduler | Parallel, high-performance computing (HPC), rendering, or scheduled jobs on pools | Job/task scheduling over elastic compute pools |
| Azure Virtual Desktop (AVD) | Managed virtual desktop infrastructure (VDI) | Cloud-hosted Windows desktops and remote applications | Centralized desktop/app delivery for user sessions; not a general-purpose web application host |

### VM availability choices

| Choice | What it provides | Choose when | Boundary |
|---|---|---|---|
| Availability set | Distributes VMs across fault and update domains within a datacenter-scale deployment | Existing design or region without availability zones | Does not span zones or protect against regional failure |
| Availability zones | Places instances in physically separate datacenter locations within one region | Zonal failure protection is required and the service/SKU supports it | Does not by itself provide regional disaster recovery |
| Virtual Machine Scale Sets (VMSS) | Manages a fleet of VM instances, with scaling and lifecycle capabilities | A repeatable, elastic VM fleet is needed | A scale set is not itself a failure-isolation level; configure instance count and zone/placement policy |

## Mental decision model

```text
Requires arbitrary OS, agent, driver, or vendor image?
→ Virtual Machines

Requires many identical, elastic VMs?
→ Virtual Machine Scale Sets

Deliver Windows desktops or remote applications to users?
→ Azure Virtual Desktop

Managed HTTP application on supported platform?
→ App Service

Trigger-driven code with externalized state?
→ Azure Functions

Container image but no Kubernetes requirement?
→ Container Apps or Container Instances, based on platform/orchestration needs

Kubernetes API or ecosystem is mandatory?
→ AKS

Large parallel job/task workload?
→ Azure Batch
```

These are starting directions. Network isolation, startup latency, execution duration, state, zones, GPU/HPC needs, licensing, team capability, and cost can change the selection.

## State, scale, and availability

- **Scale up** increases instance size; **scale out** increases instance count.
- Autoscale manages capacity. It is not high availability if the minimum topology still contains one instance or one regional dependency.
- Stateless instances are easier to replace and distribute. Put durable state in an appropriate data service.
- Platform-managed does not remove application retry, health, deployment, data protection, and regional-recovery design.
- A service supporting zones does not mean every tier, region, or existing deployment is zone redundant.

## Adjacent AI and edge workloads

- Azure AI services provide ready-made AI capabilities; Azure Machine Learning supports the lifecycle for building, training, deploying, and managing custom models. Select from model/customization needs, data boundaries, latency, and operations rather than treating either as a generic compute host.
- Internet of Things (IoT) and edge designs place device connectivity and, when required, local processing near devices. Choose edge execution when latency, intermittent connectivity, data-volume, or local-processing requirements make cloud-only execution unsuitable; account for device lifecycle and synchronization with cloud services.
- These categories change the workload architecture and data path; they do not replace the VM, application-platform, container, or serverless responsibility-model decisions above.

## Workload questions

1. Does software require OS, kernel, driver, or privileged access?
2. Is the workload request-serving, event-driven, scheduled, or massively parallel?
3. Is Kubernetes a mandatory interface or merely a possible implementation?
4. Can state be externalized and operations made idempotent?
5. What are scale latency, minimum capacity, and cold-start limits?
6. Which zone/region failures must it survive?
7. Who patches, upgrades, observes, and secures each layer?

Detailed selection, constraints, deployment, and cost analysis: [Compute](../Design_infrastructure_solutions/compute.md).

Official references: [Choose an Azure compute service](https://learn.microsoft.com/en-us/training/modules/design-compute-solution/2-choose-compute-service), [Azure compute technology choices](https://learn.microsoft.com/en-us/azure/architecture/guide/technology-choices/compute-decision-tree).

---

## Navigation

| Previous | Home | Next |
|---|---|---|
| [← Azure architecture fundamentals](azure_architecture_fundamentals.md) | [Prerequisites home](README.md) | [Storage foundations →](storage_foundations.md) |
