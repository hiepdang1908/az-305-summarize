# Compute foundations

## Responsibility model

Compute selection is primarily a decision about required control, workload shape, scale unit, and operational responsibility.

| Model | Customer control | Microsoft manages | Common architect reason |
|---|---|---|---|
| IaaS | Guest OS, runtime, middleware, application, patch/configuration strategy | Physical datacenter, host, virtualization fabric | Legacy compatibility, custom OS/software, specialized hardware, rehost |
| Managed application PaaS | Application and supported runtime/configuration choices | OS, platform patching, host placement, much of scaling/availability | Reduce platform operations for web/API workloads |
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
| Azure Kubernetes Service | Managed Kubernetes | Kubernetes-orchestrated applications | Kubernetes API, ecosystem, scheduling, or extensibility is mandatory |
| Azure Container Instances | Direct container groups | Simple isolated or short-lived containers | Fast container execution without a full application platform |
| Azure Batch | Managed batch scheduler | Parallel, HPC, rendering, or scheduled jobs on pools | Job/task scheduling over elastic compute pools |

## Mental decision model

```text
Requires arbitrary OS, agent, driver, or vendor image?
→ Virtual Machines

Requires many identical, elastic VMs?
→ Virtual Machine Scale Sets

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
