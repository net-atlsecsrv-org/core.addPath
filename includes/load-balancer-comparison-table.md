---
 title: include file
 description: include file
 services: load balancer
 author: KumudD
 ms.service: load-balancer
 ms.topic: include
 ms.date: 02/08/2018
 ms.author: kumud
 ms.custom: include file
---

| | Standard SKU | Basic SKU |
| --- | --- | --- |
| Backend pool size | Supports up to 1000 instances. | Supports up to 100 instances. |
| Backend pool endpoints | Any virtual machine in a single virtual network, including blend of virtual machines, availability sets, virtual machine scale sets. | Virtual machines in a single availability set or virtual machine scale set. |
| [Health probes](../articles/load-balancer/load-balancer-custom-probe-overview.md#types) | TCP, HTTP, HTTPS | TCP, HTTP |
| [Health probe down behavior](../articles/load-balancer/load-balancer-custom-probe-overview.md#probedown) | TCP connections stay alive on instance probe down __and__ on all probes down. | TCP connections stay alive on instance probe down. All TCP connections terminate on all probes are down. |
| Availability Zones | In Standard SKU, zone-redundant and zonal frontends for inbound and outbound, outbound flows mappings survive zone failure, cross-zone load balancing. | Not available. |
| Diagnostics | Azure Monitor, multi-dimensional metrics including byte and packet counters, health probe status, connection attempts (TCP SYN), outbound connection health (SNAT successful and failed flows), active data plane measurements | Azure Log Analytics for public Load Balancer only, SNAT exhaustion alert, backend pool health count. |
| HA Ports | Internal Load Balancer | Not available. |
| Secure by default | Public IP, public Load Balancer endpoints, internal Load Balancer endpoints are closed to inbound flows unless whitelisted by a network security group. | Open by default, network security group optional. |
| [Outbound connections](../articles/load-balancer/load-balancer-outbound-connections.md) | You can explicitly define pool-based outbound NAT with [outbound rules](../articles/load-balancer/load-balancer-outbound-rules-overview.md). You can use multiple frontends with per load balancing rule opt-out. An outbound scenario _must_ be explicitly created for the virtual machine, availability set, virtual machine scale set to use outbound connectivity.  Virtual Network Service Endpoints can be reached without defining outbound connectivity and don't count towards data processed.  Any public IP addresses, including Azure PaaS services not available as VNet Service Endpoints, must be reached via outbound connectivity and count towards data processed. When only an internal Load Balancer is serving a virtual machine, availability set, or virtual machine scale set, outbound connections via default SNAT aren't available; use [outbound rules](../articles/load-balancer/load-balancer-outbound-rules-overview.md) instead. Outbound SNAT programming is transport protocol specific based on protocol of the inbound load balancing rule. | Single frontend, selected at random when multiple frontends are present.  When only internal Load Balancer is serving a virtual machine, availability set, or virtual machine scale set, default SNAT is used. |
| [Outbound Rules](../articles/load-balancer/load-balancer-outbound-rules-overview.md) | Declarative outbound NAT configuration, using public IP addresses or public IP prefixes or both, configurable outbound idle timeout (4-120 minutes), custom SNAT port allocation | Not available. |
|  [TCP Reset on Idle](../articles/load-balancer/load-balancer-tcp-reset.md) | Enable TCP Reset (TCP RST) on Idle Timeout on any rule | Not available |
| [Multiple frontends](../articles/load-balancer/load-balancer-multivip-overview.md) | Inbound and [outbound](../articles/load-balancer/load-balancer-outbound-connections.md) | Inbound only |
| Management Operations | Most operations < 30 seconds | 60-90+ seconds typical. |
| SLA | 99.99% for data path with two healthy virtual machines. | Not applicable. | 
| Pricing | Charged based on number of rules, data processed inbound and outbound associated with resource.  | No charge. |
|  |  |  |
