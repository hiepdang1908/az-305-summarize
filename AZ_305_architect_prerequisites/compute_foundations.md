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

## Virtual Machine (VM) fundamentals

A VM is an infrastructure as a service (IaaS) server hosted on Azure's virtualized infrastructure. Azure supplies the physical host and virtualizes central processing unit (CPU), memory, networking, and storage; the VM runs an operating system (OS) that the customer selects and manages. The OS and software control make VMs useful for legacy or custom environments, but patching and guest configuration remain customer responsibilities.

Consider a VM when OS-level control, custom software/runtime, specialized hardware, or a low-change lift-and-shift path is important. Prefer a managed platform or serverless option when it meets the workload needs and reducing guest OS operations is more valuable than VM-level control.

## VM resources

A VM deployment consists of multiple Azure resources. A NIC connects the VM to a subnet in a virtual network; an OS disk provides boot storage. Other resources depend on the workload and exposure design.

```text
Virtual Machine → Network Interface (NIC) → subnet → Virtual Network (VNet)
	├────────→ OS disk (normally a managed disk)
	└────────→ optional data disks

NIC address configuration → private Internet Protocol (IP) address; optional public IP
Network Security Group (NSG) → optional at NIC or subnet scope
Boot diagnostics / Azure Monitor → optional troubleshooting and observability capabilities
```

| Resource/capability | Role | Requirement |
|---|---|---|
| VM resource | Defines the VM's size, image/OS, and references to its attached resources | Required |
| OS disk | Holds the operating system and boot volume; normally backed by a managed disk | Required |
| NIC and IP configuration | Attaches the VM to a network and provides a private IP configuration | Required for a networked Azure VM |
| VNet and subnet | Provide the private network/address space where the NIC is placed | Required for the standard VNet-connected VM design |
| Data disk(s) | Adds persistent data volumes when the workload needs separate capacity/performance | Optional |
| NSG | Filters network traffic at a subnet or NIC | Optional; apply where the network security design requires it |
| Public IP | Enables direct public addressing for supported inbound/outbound patterns | Optional; use a controlled ingress/egress design instead when appropriate |
| Boot diagnostics | Captures console/boot information to help investigate startup failures | Optional troubleshooting capability |
| Azure Monitor / guest agent | Collects platform or guest telemetry for operations | Optional monitoring design; not a prerequisite for VM existence |

The NSG can be associated with a subnet or NIC; it is not required to be attached directly to every VM. A VM can use a private address without a public IP. Data disks are added only when required, and reachability is distinct from authorization.

## VM sizing

VM size is a capacity and capability profile, not just a label for "how big" a server is. The selected size sets limits for virtual CPUs (vCPUs), memory, network throughput, supported NIC/data-disk counts, and storage performance ceilings; some sizes also provide graphics processing units (GPUs). Disk choice and VM-level limits both affect achievable disk input/output operations per second (IOPS) and throughput.

Choose from measured workload needs:

| Dimension | Sizing question |
|---|---|
| CPU | How much parallel or CPU-bound work is required at typical and peak load? |
| Memory | How large is the working set, and does it need to stay in memory? |
| Disk I/O | What capacity, latency, IOPS, throughput, and persistence does the workload need? |
| Network | What bandwidth, flow/connections, and NIC capability are required? |
| Accelerator | Does software benefit from a supported GPU or other specialized hardware? |
| Workload pattern | Is demand steady, bursty, seasonal, or scale-out? |
| Constraints and cost | Is the size available in the target region/zone, supported by quota and required features, and cost-effective at expected utilization? |

Validate the exact VM size and disk combination: per-VM limits, disk limits, network performance, regional/zone availability, quota, OS/software compatibility, and price vary by series and deployment. Benchmark representative load rather than choosing from vCPU count alone.

### VM size families

| Family | Workload characteristic | Typical use | Key idea |
|---|---|---|---|
| B | Burstable CPU using credits above a baseline | Intermittent development/test, light web, or small services | Cost-efficient when high CPU is occasional; sustained CPU can exhaust credits and throttle to baseline |
| D | General purpose, balanced CPU and memory | Web/app servers and broad enterprise workloads | A reasonable starting family when requirements are balanced |
| E | Memory optimized, higher memory-to-vCPU ratio | Databases, caches, and in-memory analytics | Choose when memory pressure is the bottleneck |
| F | Compute optimized, higher CPU-to-memory ratio | CPU-heavy application servers, batch, and processing | Choose when CPU dominates and a large memory ratio is unnecessary |
| M | Very large memory footprint | Large enterprise databases, SAP, or extremely memory-intensive workloads | For memory scale beyond typical E-family choices; validate workload certification and cost |
| L | Storage optimized, high disk I/O/throughput capability | Data-intensive processing and I/O-heavy databases | Compare local/remote storage behavior and persistence needs for the specific series |
| N | GPU-accelerated subfamilies | Graphics, visualization, artificial intelligence/machine learning (AI/ML), and accelerated computing | Select the GPU subfamily from the actual accelerator/software requirement |

Conceptual selection examples: occasional development server → consider B; a general line-of-business server → consider D; memory-heavy database → consider E or M based on scale; CPU-heavy processing → consider F; high disk-I/O → consider L; GPU rendering or supported AI acceleration → consider an appropriate N subfamily. These are starting points, not rules; Azure has many series and subfamilies beyond these seven.

### VM size names

Names commonly encode family, vCPU count, optional feature letters, and generation/version. For example, `Standard_D2s_v5` indicates D family, 2 vCPUs, `s` for Premium solid-state drive (SSD) compatibility, and version 5. In applicable names, `a` indicates AMD-based processor, `p` indicates ARM-based processor, and `d` indicates a local temporary disk. Not every series uses every field or letter, and some families use subfamily/accelerator identifiers. Treat names as clues; confirm the exact capabilities in the current size documentation rather than memorizing suffixes.

## VM hosting and container decisions

| Requirement | Likely option | Why / boundary |
|---|---|---|
| OS control, custom runtime/agent, legacy compatibility, or specialized VM hardware | VM | Broadest guest control; highest OS and patching responsibility |
| Managed HTTP web app/API on a supported runtime | App Service | Azure operates the web-hosting platform; less OS control than a VM |
| Package an application and its dependencies for portability | Container service | Shares the host OS kernel rather than providing a separate customer-managed guest OS; choose ACI, Container Apps, or AKS by orchestration needs |
| Event-driven code with variable demand and minimal infrastructure management | Azure Functions | Triggered serverless execution; scaling, duration, networking, and cold-start behavior depend on the hosting plan |

VMs virtualize a hardware/guest-OS environment; containers package application code and dependencies while sharing the host OS kernel. Containers can start and scale more lightly, but they are not a VM substitute when a distinct kernel or full OS control is required. ACI runs container groups without a full orchestrator; Container Apps adds managed app deployment/scaling; Azure Kubernetes Service (AKS) is appropriate when Kubernetes orchestration is a requirement.

Azure Functions is an event-driven serverless compute option for triggered processing, commonly short-running units. It can scale with demand under the selected plan, but is not unlimited: duration, scale, networking, and always-ready behavior are plan-dependent. Use App Service for a continuously hosted managed web/API when that model fits; use Functions for discrete trigger-driven work; use VMs when host/OS control is mandatory.

### VM availability choices

| Choice | What it provides | Choose when | Boundary |
|---|---|---|---|
| Availability set | Distributes VMs across fault and update domains within a datacenter-scale deployment | Existing design or region without availability zones | Does not span zones or protect against regional failure |
| Availability zones | Places instances in physically separate datacenter locations within one region | Zonal failure protection is required and the service/SKU supports it | Does not by itself provide regional disaster recovery |
| Virtual Machine Scale Sets (VMSS) | Manages a fleet of VM instances, with scaling and lifecycle capabilities | A repeatable, elastic VM fleet is needed | A scale set is not itself a failure-isolation level; configure instance count and zone/placement policy |

An availability set is a VM placement option; a scale set is a fleet-management and scaling option. Scale sets can centralize instance configuration, integrate with load balancing, and support autoscale rules/schedules. A single VM is simpler but remains one instance; a scale set suits repeatable fleet deployment and horizontal scaling, especially for stateless or externally stateful workloads. Neither a scale set nor autoscaling alone guarantees high availability: configure multiple instances, appropriate fault distribution, healthy traffic routing, and sufficient surviving capacity.

**Scale up** means selecting a larger VM size; **scale out** means adding VM instances, often managed as a scale set. Scale-out requires the application to tolerate multiple instances and usually externalized shared state. Autoscale reacts to configured signals/schedules but may take time to add capacity, so set a viable minimum and plan for quota/startup latency.

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

Official references: [Describe Azure virtual machines](https://learn.microsoft.com/en-us/training/modules/describe-azure-compute-networking-services/2-virtual-machines), [Azure VM size overview](https://learn.microsoft.com/en-us/azure/virtual-machines/sizes/overview), [Azure VM naming conventions](https://learn.microsoft.com/en-us/azure/virtual-machines/vm-naming-conventions), [Azure Virtual Desktop](https://learn.microsoft.com/en-us/training/modules/describe-azure-compute-networking-services/4-virtual-desktop), [Azure containers](https://learn.microsoft.com/en-us/training/modules/describe-azure-compute-networking-services/5-containers), [Azure Functions](https://learn.microsoft.com/en-us/training/modules/describe-azure-compute-networking-services/6-functions), [Application hosting options](https://learn.microsoft.com/en-us/training/modules/describe-azure-compute-networking-services/7-describe-application-hosting-options), [Azure compute technology choices](https://learn.microsoft.com/en-us/azure/architecture/guide/technology-choices/compute-decision-tree).

---

## Navigation

| Previous | Home | Next |
|---|---|---|
| [← Azure architecture fundamentals](azure_architecture_fundamentals.md) | [Prerequisites home](README.md) | [Storage foundations →](storage_foundations.md) |
