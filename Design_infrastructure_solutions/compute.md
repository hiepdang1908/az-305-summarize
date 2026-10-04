# Compute

## Compute decision matrix

| Service | Infrastructure control | Scale/orchestration | Best fit | Avoid when | Operations |
|---|---|---|---|---|---|
| Virtual Machines (VMs) | Full guest operating system (OS) and software control | Manual/automation; individual VMs | Custom OS, legacy/commercial off-the-shelf (COTS), special agents/drivers, rehost | Platform as a service (PaaS) can meet requirements | Highest |
| Virtual Machine Scale Sets (VMSS) | VM control with fleet model | Autoscale, zones, rolling upgrades | Homogeneous VM fleet and elastic infrastructure as a service (IaaS) | Each server is unique or application cannot scale horizontally | High |
| App Service | Managed web platform | Plan instances, autoscale, deployment slots | HTTP web apps/APIs, supported runtimes or web containers | Kubernetes/control/custom OS required | Low |
| Azure Functions | Managed event-driven code | Trigger-based, plan-dependent scale | Short/event-driven units, integrations, timers | Full host control, unsuitable execution/runtime pattern | Low |
| Azure Container Apps | Managed serverless container platform | Revisions, replicas, KEDA-based scaling, jobs, Dapr options | HTTP/event microservices and jobs without managing Kubernetes | Direct Kubernetes API/control is mandatory | Low–moderate |
| Azure Kubernetes Service (AKS) | Managed Kubernetes control plane | Kubernetes orchestration, node pools, autoscaler | Complex microservices and Kubernetes ecosystem/control | Team does not need Kubernetes complexity | High |
| Azure Container Instances (ACI) | Direct container groups | Simple/manual or external orchestration | Burstable isolated task, build job, simple short-lived container | Full application platform/orchestration required | Low per instance, limited platform |
| Azure Batch | Managed scheduling over compute pools | Jobs/tasks, autoscale pools, retries | Large-scale parallel/high-performance computing (HPC)/batch processing | Request-serving application or workflow integration | Moderate |
| Logic Apps | Managed workflow/integration runtime | Connector/workflow execution | Low-code integration and business workflows | General-purpose custom compute | Low |

## Decision tree

```text
Requires arbitrary OS/kernel/software or minimal application change?
  └─ Yes → VM; repeatable elastic fleet → VM Scale Sets

Container image required?
  ├─ Kubernetes API/ecosystem/control required → AKS
  ├─ Managed microservices, revisions, event scale/jobs → Container Apps
  └─ Simple isolated container/task → Container Instances

HTTP web/API on supported managed platform?
  └─ App Service

Event/trigger-driven code with serverless execution?
  └─ Functions

Massively parallel scheduled computation?
  └─ Azure Batch
```

## Virtual machines

Choose VMs when compatibility or control is mandatory:

- Custom OS configuration, kernel/driver, agent, or privileged software
- Legacy application with unsupported PaaS runtime
- Vendor certification tied to OS/VM
- Lift-and-shift with limited change window
- Specialized CPU, memory, GPU, HPC, confidential, or storage configuration

Design responsibilities:

- Image lifecycle, patching, endpoint protection, backups, configuration drift
- VM size and quota; accelerated networking and placement where required
- Managed disk tier, caching, input/output operations per second (IOPS), throughput, encryption, backup
- Availability zones/sets and at least two instances for high availability (HA)
- Load balancing, health probes, autoscale/fleet orchestration
- Just-in-time/admin access, Azure Bastion, managed identity, NSGs

Stopped versus deallocated matters for compute billing; disks and other allocated resources continue to cost. Ephemeral OS disks improve reimage speed/cost for disposable instances but do not preserve OS state.

### Virtual Machine Scale Sets

Use scale sets for identical or model-managed VM instances. Prefer stateless instances, externalize durable data, use health-based rolling upgrades, and spread capacity across zones where supported. Autoscale needs enough quota and startup time to meet demand; it does not replace minimum redundant capacity.

## App Service

Choose for managed HTTP applications and APIs when supported runtime/container and platform constraints fit.

Architecture factors:

- App Service plan is the compute/scale boundary; apps in one plan share workers and failure/cost effects.
- Scale up changes worker size; scale out changes instance count.
- Deployment slots provide staged validation and swap. Slot availability and count depend on tier.
- Slot settings remain with a slot; other settings may swap. Verify database/schema compatibility before swap.
- Built-in authentication can reduce application auth code but still requires correct authorization.
- Virtual network (VNet) integration is for outbound access from the app. Private Endpoint is for private inbound access. These are different features.
- App Service Environment is for dedicated, isolated hosting requirements; it has more cost/operations than multitenant App Service.
- Use Front Door or another cross-region entry point for regional resilience; a single regional plan is not regional disaster recovery (DR).

## Azure Functions

Choose when triggers/events/timers drive small units of code and platform-managed scale is valuable. Select hosting plan from:

- Scale behavior, cold-start tolerance, execution duration, networking, instance size, and predictable capacity
- Zone support, VNet/private access, deployment slots, and always-ready requirements
- Cost profile: consumption for bursty use; dedicated/premium-style capacity for predictable/latency-sensitive workloads

Functions can retry, duplicate, or concurrently process events depending on trigger. Make handlers idempotent, bound concurrency, use poison/dead-letter handling, and place durable state outside the function. Durable Functions coordinates stateful workflows; it does not turn arbitrary long-running code into a safe function.

## Container services

### Container Apps

- Managed environment for APIs, microservices, event-driven workers, and jobs.
- Revisions allow traffic splitting and safe rollout.
- Scale rules can respond to HTTP or event sources; scale-to-zero is possible for supported patterns.
- Dapr integration is optional, not required.
- External versus internal environment and VNet design determine exposure/connectivity.

Choose Container Apps when container portability is needed but direct cluster operations are not.

### AKS

Choose AKS only when Kubernetes capabilities are requirements: Kubernetes API/tooling, custom controllers/operators, sophisticated scheduling/network policy, broad ecosystem, or portability governance.

Customer responsibilities still include node pools, upgrades, workload identity, network model, ingress, policy, secrets integration, observability, persistent storage, pod disruption budgets, and application availability. Use managed control-plane capabilities but do not call AKS "serverless" as a general architecture assumption.

### Container Instances

ACI provides fast container-group execution without managing VMs. Containers in a group share lifecycle, network, and resources. It is useful for simple tasks and burst capacity, including virtual nodes in supported AKS patterns. It lacks the full deployment, revision, discovery, and orchestration platform of Container Apps or AKS.

## Azure Batch

```text
Input/application packages in storage
        ↓
Batch account → pool of compute nodes
        ↓
Job → tasks scheduled/retried across nodes
        ↓
Output/checkpoints to durable storage
```

Use Batch for parallel, HPC, rendering, simulation, engineering, and scheduled compute. Decide:

- Pool allocation mode, VM size/image, dedicated versus interruptible capacity
- Task independence/tight coupling, inter-node communication, placement
- Autoscale and warm-pool trade-off
- Checkpoint/retry/idempotency because nodes can disappear
- Data staging/locality and output durability
- Quota and regional capacity

Do not create a pool per very short task if startup dominates. Do not rely on local node storage for final output.

## Automated deployment

| Requirement | Direction |
|---|---|
| Repeatable platform resources | Bicep/Azure Resource Manager (ARM)/Terraform or approved infrastructure as code (IaC) in source control |
| App build/test/release | Azure Pipelines, GitHub Actions, or equivalent continuous integration and continuous delivery (CI/CD) |
| Safe App Service release | Deployment slots and swap with health validation |
| Safe container release | Immutable image, registry scanning, revisions/rolling/canary strategy |
| VM fleet rollout | Immutable image via Azure Compute Gallery plus scale-set upgrade policy |

Separate build artifact creation from environment promotion. Use workload identity federation/managed identity instead of long-lived deployment secrets. Include rollback and database-change compatibility; infrastructure deployment success does not guarantee application health.

## Cost and availability

- VM/AKS maximize control but transfer the most operations to the customer.
- Consumption/serverless reduces idle cost for bursty workloads but can add cold starts, quotas, and per-execution cost.
- Dedicated plans can host multiple apps but create shared capacity/blast radius.
- Spot/interruptible capacity is appropriate only for restartable workloads.
- Minimum two instances/zones for HA costs more than autoscaling from one after failure.
- Licensing, data egress, managed disks, load balancers, NAT, observability, and standby region are part of compute cost.

## Common Trap

- Containers do not automatically mean Kubernetes.
- AKS is managed Kubernetes, not zero-operations Kubernetes.
- ACI is not a full orchestrator.
- Scale-to-zero conflicts with always-warm latency unless plan/features compensate.
- Deployment slots are not backups or cross-region DR.
- Autoscale is capacity management, not HA.
- Rehosting to a VM is often fastest, not necessarily the best target state.

Official references: [Azure compute decision guide](https://learn.microsoft.com/en-us/azure/architecture/guide/technology-choices/compute-decision-tree), [App Service overview](https://learn.microsoft.com/en-us/azure/app-service/overview), [Functions hosting options](https://learn.microsoft.com/en-us/azure/azure-functions/functions-scale), [Container Apps overview](https://learn.microsoft.com/en-us/azure/container-apps/overview), [AKS core concepts](https://learn.microsoft.com/en-us/azure/aks/core-aks-concepts), [Azure Batch overview](https://learn.microsoft.com/en-us/azure/batch/batch-technical-overview).

---

## Navigation

| Previous | Home | Next |
|---|---|---|
| [← Infrastructure solutions](README.md) | [Domain home](README.md) | [Application architecture →](application_architecture.md) |
