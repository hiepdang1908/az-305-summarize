# Networking

## Start with traffic flows

For every flow identify source, destination, protocol/port, direction, trust boundary, expected throughput/latency, Domain Name System (DNS) name, encryption, inspection, availability, and ownership.

```text
Internet / branch / remote user
        ↓
Global or regional entry point
        ↓
Web Application Firewall (WAF) / firewall / network security group (NSG) segmentation
        ↓
Application subnet/service
        ↓
Private Endpoint + private DNS for platform as a service (PaaS)
        ↓
Data service
```

## Application delivery matrix

| Dimension | Azure Front Door | Application Gateway | Azure Load Balancer | Traffic Manager |
|---|---|---|---|---|
| Layer | Layer 7 (L7) HTTP(S) reverse proxy at edge | L7 HTTP(S) reverse proxy | Layer 4 (L4) Transmission Control Protocol (TCP)/User Datagram Protocol (UDP) | DNS-based routing; not a proxy |
| Scope | Global | Regional | Regional; cross-region tier/features exist for specific designs | Global |
| Frontend | Public edge | Public or private regional frontend | Public or internal | DNS response |
| Backend scope | Publicly reachable or private-link-supported origins depending on tier/configuration | VNet/reachable regional or cross-network HTTP backends | VNet/regional IP resources | Any supported public endpoint type |
| WAF | Yes | Yes | No | No |
| Transport Layer Security (TLS) termination | Yes | Yes | No application TLS termination | No |
| Path/host routing | Yes | Yes | No | No; DNS policies only |
| Acceleration/cache | Anycast edge acceleration; content delivery network (CDN) capabilities | No global edge acceleration | No | No proxy/cache |
| Non-HTTP TCP/UDP | No | No | Yes | Can direct DNS clients to endpoints, but does not proxy protocol |
| Private/internal app ingress | Private Link to supported origin patterns; edge remains global | Strong regional private-frontend option | Internal L4 | Private endpoints are not directly reached by public DNS clients without network path |
| Best fit | Global web app, edge acceleration, WAF, multi-region HTTP failover | Regional/private L7 routing and WAF | Regional L4 or internal load balancing | DNS-level global routing for varied endpoint/protocol scenarios |

```text
Global HTTP(S) + edge acceleration + WAF → Front Door
Regional HTTP(S) + private/internal frontend + WAF → Application Gateway
Regional TCP/UDP or internal Layer 4 → Load Balancer
DNS-based global endpoint selection → Traffic Manager
```

These services can compose. Example: Front Door for global routing, Application Gateway for regional private web ingress, and Load Balancer for an internal non-HTTP tier. Avoid stacking them without a requirement because each adds latency, cost, certificates, probes, and failure modes.

### Health and routing

- Proxy health probes determine eligible backends; DNS health routing changes future DNS answers and is affected by client/recursive resolver caching.
- Front Door can fail traffic rapidly at the edge, while Traffic Manager recovery depends partly on DNS time-to-live (TTL)/cache behavior.
- Preserving client IP, TLS end-to-end, hostname/server name indication (SNI), and probe paths requires explicit configuration.
- WAF protects HTTP(S) patterns. It does not replace network segmentation, distributed denial-of-service (DDoS) protection, identity, or secure application code.

## Hybrid connectivity matrix

A virtual private network (VPN) provides an encrypted tunnel over a shared network.

| Dimension | VPN Gateway | ExpressRoute | Virtual WAN |
|---|---|---|---|
| Transport | Encrypted VPN tunnel over public internet | Private provider circuit into Microsoft network | Microsoft-managed hubs combining VPN, ExpressRoute, user VPN, and virtual network (VNet) connectivity |
| Time/cost | Fast deployment, lower entry cost | Provider lead time and higher fixed cost | Cost for hubs/connections/services; lowers operational complexity at scale |
| Predictability | Internet-dependent | More predictable private connectivity; bandwidth/service-level agreement (SLA) by circuit/provider design | Depends on underlying VPN/ExpressRoute and hub design |
| Encryption | Internet Protocol security (IPsec)/Internet Key Exchange (IKE) for VPN | Private path is not synonymous with encrypted traffic; add encryption if required | Depends on connection type/features |
| Best fit | Small/medium sites, encrypted connection, backup path | High-throughput/predictable enterprise hybrid connectivity | Many branches/regions, transitive managed connectivity, software-defined wide area network (SD-WAN) integration |
| High availability (HA) | Active-active/redundant gateway and on-premises devices | Redundant circuit connections/providers; VPN backup optional | Redundant managed hubs and connections according to design |

### VPN choices

- Site-to-site: network-to-network persistent tunnel.
- Point-to-site: individual client access.
- VNet-to-VNet: IPsec between Azure VNets; peering is usually lower-latency/private-backbone for VNet connectivity.
- Route-based VPN is the normal choice for modern dynamic/routed topologies; validate policy-based interoperability needs.
- Border Gateway Protocol (BGP) supports dynamic route exchange and failover but requires unique addressing/autonomous system numbers (ASNs) and controlled advertisements.

### ExpressRoute choices

- Private peering accesses Azure VNets and supported private IP resources.
- Microsoft peering is for supported Microsoft public services with routing/authorization requirements; it does not turn all software as a service (SaaS) into private endpoints.
- ExpressRoute Global Reach connects on-premises sites through Microsoft backbone in supported locations.
- A circuit alone is not end-to-end HA: use redundant connections, diverse provider/peering locations where business requirements justify, and resilient gateways.
- VPN can provide backup, but route preference and capacity must be tested.

### Virtual WAN

Choose Virtual WAN when branch, user, VNet, VPN, and ExpressRoute connectivity at global scale benefits from Microsoft-managed hubs and automated routing. A secured virtual hub integrates Azure Firewall and routing intent/policies for controlled traffic paths. Do not choose it for a few simple peered VNets unless managed transit benefit justifies cost and model constraints.

## Topology and routing

| Pattern | Choose when | Constraint |
|---|---|---|
| Single VNet | Small regional workload with simple ownership | Limited scale/isolation; VNet is regional |
| Peered VNets | Direct low-latency private connectivity between a manageable number of VNets | Peering is not transitive; mesh complexity grows |
| Customer-managed hub-spoke | Central firewall/gateways/DNS with explicit control | Customer owns routes, scale, appliances, HA |
| Virtual WAN hub-spoke | Many branches/regions and managed transit | Different routing model/cost/features; validate requirements |

Plan nonoverlapping IP space before migration. VNet peering and hybrid routing become difficult when address ranges overlap. Network address translation (NAT) can mitigate specific overlaps but adds complexity and is not a substitute for address governance.

### Route selection

Azure selects the longest prefix. For equal prefixes, user-defined routes (UDRs) are generally preferred over BGP routes, which are preferred over system routes, subject to documented exceptions. Use effective routes for diagnosis rather than reasoning only from configured tables.

Use UDRs to steer traffic to Azure Firewall/network virtual appliance (NVA), force tunneling, or override system routes. Prevent asymmetric routing through stateful appliances by designing both directions. Gateway transit allows a spoke to use a hub gateway under peering configuration; it does not make all peering transitive.

## Internet ingress and egress

### Ingress

- Public IP directly on a virtual machine (VM) maximizes exposure and should be exceptional.
- Front Door provides global HTTP(S) edge ingress.
- Application Gateway provides regional HTTP(S) ingress and private frontend.
- Public Load Balancer provides regional L4 ingress.
- NAT Gateway is outbound only; it does not accept unsolicited inbound connections.

### Egress

For the Azure network API behavior released after March 31, 2026, subnets in new virtual networks default to private (`defaultOutboundAccess=false`); the Azure portal also defaults new subnets to private. Earlier API versions and existing virtual networks are not changed automatically. Explicitly design egress instead of relying on implicit default outbound access.

| Requirement | Direction |
|---|---|
| Scalable source network address translation (SNAT) with stable public IPs, no inspection | NAT Gateway |
| Central filtering/fully qualified domain name (FQDN) rules/threat intelligence | Azure Firewall |
| L4 load balancer already fronts instances and outbound rules meet needs | Load Balancer outbound rules, after validating SNAT scale |
| No internet egress | Private endpoints/service paths, deny default route/NSG/firewall as appropriate |

NAT Gateway attaches to subnets and takes precedence for new outbound flows over several other outbound methods. It does not filter traffic; pair with NSGs/firewall as required. Plan SNAT port capacity for high fan-out connections.

## Private Endpoint versus service endpoint

| Dimension | Private Endpoint / Private Link | Service Endpoint |
|---|---|---|
| Service address | Private IP network interface card (NIC) in consumer VNet | Service retains public endpoint/IP |
| Traffic path | Private Link over Microsoft network | Optimized route from enabled subnet to public service endpoint |
| On-prem access | Possible through VPN/ExpressRoute with correct routing and DNS | Service endpoint identity applies to Azure VNet subnet; not equivalent for on-prem clients |
| DNS | Private DNS design is essential | Usually public service DNS remains |
| Isolation | Can disable public network access for supported service | Firewall permits selected VNets/subnets but public endpoint still exists |
| Cross-region/VNet consumer model | Endpoint can be placed where consumer connects, subject to service support | Configured on subnet and service firewall |
| Cost/operations | Endpoint/hour/data and DNS lifecycle | Generally simpler/lower direct cost |
| Choose when | Private IP, on-prem private access, exfiltration control, public access disabled | Simple VNet-to-PaaS restriction with public endpoint acceptable |

Private Endpoint approval and DNS are separate from data authorization. A successful TCP path does not grant access. Multiple endpoints and split-horizon DNS must be planned for hub-spoke/on-premises resolution.

### Private-connectivity choice

| Mechanism | What receives/connects | Traffic and public-endpoint effect | DNS and cross-network consequence | Typical use |
|---|---|---|---|---|
| Public endpoint + firewall | Client reaches the service's public FQDN/IP | Internet/public service endpoint remains; firewall restricts allowed sources | Public DNS; on-premises clients can use allowlisted public/NAT IPs | Publicly reachable service with controlled sources and no private-IP requirement |
| Service Endpoint | Source subnet identity is extended to a supported PaaS service | Microsoft backbone path, but destination remains the service's public endpoint | Public DNS normally remains; primarily an Azure-subnet-to-service control | Simple subnet restriction when public endpoint semantics are acceptable |
| Private Endpoint | A private NIC/IP in a consumer VNet maps to one service instance/subresource | Clients use the private IP; supported services can disable public access | Private DNS/split-horizon design is required; reachable from peered/hybrid networks when routing and DNS work | Resource-scoped private PaaS access and exfiltration control |
| App Service/Functions VNet Integration | The managed app gets an outbound path into/through an integration subnet | Outbound feature only; it does not make inbound access to the app private | App uses VNet routes/DNS for routed traffic and can reach private endpoints, peered networks, or on-premises | Managed app must call VNet-private dependencies or route outbound through a firewall/NAT |
| VPN Gateway / ExpressRoute | Connects networks, not an individual PaaS resource | Supplies a hybrid path; PaaS is private only when combined with an appropriate private-access feature | Requires routing plus hybrid DNS; VPN is encrypted over internet, ExpressRoute is private provider connectivity | On-premises/branch-to-VNet connectivity, including reaching private endpoints |

```text
Private inbound access to App Service → Private Endpoint
App Service outbound access to VNet/private dependency → VNet Integration
On-premises network path to Azure → VPN Gateway or ExpressRoute
On-premises private access to PaaS → hybrid path + Private Endpoint + private DNS
```

## DNS architecture

| Need | Direction |
|---|---|
| Public authoritative zones | Azure DNS public zones |
| Private names within linked VNets | Azure Private DNS zones |
| Resolve Azure private zones from on-premises and vice versa | Azure DNS Private Resolver with inbound/outbound endpoints and forwarding rulesets |
| Hybrid custom DNS/AD-integrated zones | Conditional forwarding and resilient DNS servers/resolver architecture |

Private Endpoint normally requires the documented `privatelink` zone and VNet links. Do not overwrite public service zones with incomplete private zones. Centralize private DNS carefully: record lifecycle, auto-registration, cross-region resolver availability, and forwarding loops are operational risks.

## Network security layers

| Control | Layer/scope | Primary role | Does not replace |
|---|---|---|---|
| NSG | Stateful Layer 3 (L3)/L4 on subnet/NIC | Distributed allow/deny segmentation | Central advanced firewall or WAF |
| Azure Firewall | Central stateful L3–L7 network security | Egress/intersite filtering, destination/source network address translation (DNAT/SNAT), FQDN/application rules, threat intelligence | Web-specific WAF or identity authorization |
| WAF | HTTP(S) application layer | Open Worldwide Application Security Project (OWASP)-style attack filtering on Front Door/Application Gateway | General TCP/UDP firewall |
| DDoS Protection | Network-layer volumetric attack protection for public IP/VNet resources | Enhanced mitigation/telemetry/cost protection by plan | WAF, application scaling, identity |
| Private Endpoint | Private service exposure | Remove public path for supported PaaS | Authorization/firewall inspection |
| Service Endpoint | Subnet identity to public PaaS endpoint | Restrict service firewall to selected subnet | Private IP or on-prem private service access |

### NSG versus Azure Firewall

- NSGs are distributed, five-tuple, stateful filters. Use service tags/application security groups to reduce IP rule sprawl.
- Azure Firewall centralizes policy, logs, FQDN/application filtering, and network/NAT rules. Premium capabilities add advanced inspection features where required.
- A common design uses NSGs for subnet/NIC segmentation and Azure Firewall for centralized inter-spoke/hybrid/egress control.
- Forced traffic inspection needs UDRs and symmetric routing. Merely deploying Firewall does not put it in the path.

## Network performance

- Place compute and data near users and each other; measure latency, not geography labels.
- Use Front Door for HTTP edge acceleration and global anycast routing.
- Use ExpressRoute for predictable private hybrid performance where provider/circuit design supports it.
- Enable accelerated networking on supported VMs and sizes.
- Choose gateway/firewall/load-balancer SKUs from throughput, connections, features, zones, and scale units.
- Use CDN/Front Door caching for cacheable web content.
- Avoid unnecessary inspection/hairpin paths and cross-zone/region chatter.
- Monitor Network Watcher/Connection Monitor, flow logs supported by current platform, gateway metrics, effective routes/security rules, latency, packet loss, and SNAT exhaustion.

## Availability and cost

- Zone-redundant gateways/firewalls/load balancers reduce zone-failure risk where supported.
- A hub is a shared failure domain; deploy and scale critical hub services accordingly.
- Cross-region hubs/gateways, diverse circuits, and active-active configurations cost more but may be required by the recovery time objective (RTO).
- Peering, egress, cross-zone, cross-region, firewall processing, NAT, private endpoints, circuits, and gateways all contribute cost.
- Centralization can lower management cost but increases blast radius if quotas/routing/policy are poorly designed.

## Common Trap

- Front Door and Application Gateway are both Layer 7, but global edge versus regional VNet role changes the answer.
- Traffic Manager returns DNS answers; it does not proxy, terminate TLS, or provide WAF.
- Load Balancer is Layer 4; it does not route by URL path.
- ExpressRoute is private connectivity, not automatic encryption.
- VNet peering is nontransitive.
- NAT Gateway provides outbound translation, not firewall inspection or inbound publishing.
- Private Endpoint needs DNS and authorization; it is not the same as service endpoint.
- VNet Integration provides supported managed services an outbound VNet path; it is not private inbound publishing.
- An availability zone design is not regional disaster recovery (DR).
- For private subnets—including the post–March 31, 2026 API default for new VNets—VM internet egress needs an explicit outbound method. Existing VNets and deployments using earlier API behavior are not changed automatically.

Official references: [Azure load-balancing options](https://learn.microsoft.com/en-us/azure/architecture/guide/technology-choices/load-balancing-overview), [VPN Gateway](https://learn.microsoft.com/en-us/azure/vpn-gateway/vpn-gateway-about-vpngateways), [ExpressRoute](https://learn.microsoft.com/en-us/azure/expressroute/expressroute-introduction), [Virtual WAN](https://learn.microsoft.com/en-us/azure/virtual-wan/virtual-wan-about), [Private PaaS access](https://learn.microsoft.com/en-us/azure/networking/design-guide/private-platform-as-a-service), [Private Link](https://learn.microsoft.com/en-us/azure/private-link/private-link-overview), [App Service VNet Integration](https://learn.microsoft.com/en-us/azure/app-service/overview-vnet-integration), [NAT Gateway](https://learn.microsoft.com/en-us/azure/nat-gateway/nat-overview), [Azure Firewall](https://learn.microsoft.com/en-us/azure/firewall/overview), [Azure DNS Private Resolver](https://learn.microsoft.com/en-us/azure/dns/dns-private-resolver-overview), [Default outbound access retirement](https://learn.microsoft.com/en-us/azure/virtual-network/ip-services/default-outbound-access).

---

## Navigation

| Previous | Home | Next |
|---|---|---|
| [← Migrations](migrations.md) | [Domain home](README.md) | [High-yield recall →](../AZ-305_HIGH_YIELD_RECALL.md) |
