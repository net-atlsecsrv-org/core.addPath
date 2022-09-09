---
 title: include file
 description: include file
 services: firewall
 author: vhorne
 ms.service: 
 ms.topic: include
 ms.date: 3/26/2019
 ms.author: victorh
 ms.custom: include file
---

### What is Azure Firewall?

Azure Firewall is a managed, cloud-based network security service that protects your Azure Virtual Network resources. It is a fully stateful firewall-as-a-service with built-in high availability and unrestricted cloud scalability. You can centrally create, enforce, and log application and network connectivity policies across subscriptions and virtual networks.

### What capabilities are supported in Azure Firewall?

* Stateful firewall as a service
* Built-in high availability with unrestricted cloud scalability
* FQDN filtering
* FQDN tags
* Network traffic filtering rules
* Outbound SNAT support
* Inbound DNAT support
* Centrally create, enforce, and log application and network connectivity policies across Azure subscriptions and VNETs
* Fully integrated with Azure Monitor for logging and analytics

### What is the typical deployment model for Azure Firewall?

You can deploy Azure Firewall on any virtual network, but customers typically deploy it on a central virtual network and peer other virtual networks to it in a hub-and-spoke model. You can then set the default route from the peered virtual networks to point to this central firewall virtual network. Global VNet peering is supported, but it is not recommended due to potential performance and latency issues across regions. For best performance, deploy one firewall per region.

The advantage of this model is the ability to centrally exert control on multiple spoke VNETs across different subscriptions. There are also cost savings as you don't need to deploy a firewall in each VNet separately. The cost savings should be measured versus the associate peering cost based on the customer traffic patterns.

### How can I install the Azure Firewall?

You can set up Azure Firewall by using the Azure portal, PowerShell, REST API, or by using templates. See [Tutorial: Deploy and configure Azure Firewall using the Azure portal](../articles/firewall/tutorial-firewall-deploy-portal.md) for step-by-step instructions.

### What are some Azure Firewall concepts?

Azure Firewall supports rules and rule collections. A rule collection is a set of rules that share the same order and priority. Rule collections are executed in order of their priority. Network rule collections are higher priority than application rule collections, and all rules are terminating.

There are three types of rule collections:

* *Application rules*: Configure fully qualified domain names (FQDNs) that can be accessed from a subnet.
* *Network rules*: Configure rules that contain source addresses, protocols, destination ports, and destination addresses.
* *NAT rules*: Configure DNAT rules to allow incoming connections.

### Does Azure Firewall support inbound traffic filtering?

Azure Firewall supports inbound and outbound filtering. Inbound protection is for non-HTTP/S protocols. For example RDP, SSH, and FTP protocols.

### Which logging and analytics services are supported by the Azure Firewall?

Azure Firewall is integrated with Azure Monitor for viewing and analyzing firewall logs. Logs can be sent to Log Analytics, Azure Storage, or Event Hubs. They can be analyzed in Log Analytics or by different tools such as Excel and Power BI. For more information, see [Tutorial: Monitor Azure Firewall logs](../articles/firewall/tutorial-diagnostics.md).

### How does Azure Firewall work differently from existing services such as NVAs in the marketplace?

Azure Firewall is a basic firewall service that can address certain customer scenarios. It's expected that you will have a mix of third-party NVAs and Azure Firewall. Working better together is a core priority.

### What is the difference between Application Gateway WAF and Azure Firewall?

The Web Application Firewall (WAF) is a feature of Application Gateway that provides centralized inbound protection of your web applications from common exploits and vulnerabilities. Azure Firewall provides inbound protection for non-HTTP/S protocols (for example, RDP, SSH, FTP), outbound network-level protection for all ports and protocols, and application-level protection for outbound HTTP/S.

### What is the difference between Network Security Groups (NSGs) and Azure Firewall?

The Azure Firewall service complements network security group functionality. Together, they provide better "defense-in-depth" network security. Network security groups provide distributed network layer traffic filtering to limit traffic to resources within virtual networks in each subscription. Azure Firewall is a fully stateful, centralized network firewall as-a-service, which provides network- and application-level protection across different subscriptions and virtual networks.

### How do I set up Azure Firewall with my service endpoints?

For secure access to PaaS services, we recommend service endpoints. You can choose to enable service endpoints in the Azure Firewall subnet and disable them on the connected spoke virtual networks. This way you benefit from both features-- service endpoint security and central logging for all traffic.

### What is the pricing for Azure Firewall?

Azure Firewall has a fixed cost + variable cost:

* Fixed fee: $1.25/firewall/hour
* Variable fee: $0.03/GB processed by the firewall (ingress or egress)

There are no costs for a deallocated firewall.

For more information, see [Azure Firewall Pricing](https://azure.microsoft.com/pricing/details/azure-firewall/).

### How can I stop and start Azure Firewall?

You can use Azure PowerShell *deallocate* and *allocate* methods.

For example:

```azurepowershell
# Stop an exisitng firewall

$azfw = Get-AzFirewall -Name "FW Name" -ResourceGroupName "RG Name"
$azfw.Deallocate()
Set-AzFirewall -AzureFirewall $azfw
```

```azurepowershell
#Start a firewall

$azfw = Get-AzFirewall -Name "FW Name" -ResourceGroupName "RG Name"
$vnet = Get-AzVirtualNetwork -ResourceGroupName "RG Name" -Name "VNet Name"
$publicip = Get-AzPublicIpAddress -Name "Public IP Name" -ResourceGroupName " RG Name"
$azfw.Allocate($vnet,$publicip)
Set-AzFirewall -AzureFirewall $azfw
```

> [!NOTE]
> You must reallocate a firewall and public IP to the original resource group and subscription.

### What are the known service limits?

For Azure Firewall service limits, see [Azure subscription and service limits, quotas, and constraints](../articles/azure-subscription-service-limits.md#azure-firewall-limits)

### Can Azure Firewall in a hub virtual network forward and filter network traffic between two spoke virtual networks?

Yes, you can use Azure Firewall in a hub virtual network to route and filter traffic between two spoke virtual network. Subnets in each of the spoke virtual networks must have UDR pointing to the Azure Firewall as a default gateway for this scenario to work properly.

### Can Azure Firewall forward and filter network traffic between subnets in the same virtual network or peered virtual networks?

Yes. However, configuring the UDRs to redirect traffic between subnets in the same VNET requires additional attention. While using the VNET address range as a target prefix for the UDR is sufficient, this also routes all traffic from one machine to another machine in the same subnet through the Azure Firewall instance. To avoid this, include a route for the subnet in the UDR with a next hop type of **VNET**. Managing these routes might be cumbersome and prone to error. The recommended method for internal network segmentation is to use Network Security Groups, which don’t require UDRs.

### Is forced tunneling/chaining to a Network Virtual Appliance supported?

Yes.

Azure Firewall must have direct Internet connectivity. By default, AzureFirewallSubnet has a 0.0.0.0/0 route with the NextHopType value set to **Internet**.

If you enable forced tunneling to on-premises via ExpressRoute or VPN Gateway, you may need to explicitly configure a 0.0.0.0/0 user defined route (UDR) with the NextHopType value set as Internet and associate it with your AzureFirewallSubnet. This overrides a potential default gateway BGP advertisement back to your on-premises network. If your organization requires forced tunneling for Azure Firewall to direct default gateway traffic back through your on-premises network, contact Support. We can whitelist your subscription to ensure the required firewall Internet connectivity is maintained.

### Are there any firewall resource group restrictions?

Yes. The firewall, subnet, VNet, and the public IP address all must be in the same resource group.

### When configuring DNAT for inbound network traffic, do I also need to configure a corresponding network rule to allow that traffic?

No. NAT rules implicitly add a corresponding network rule to allow the translated traffic. You can override this behavior by explicitly adding a network rule collection with deny rules that match the translated traffic. To learn more about Azure Firewall rule processing logic, see [Azure Firewall rule processing logic](../articles/firewall/rule-processing.md).

### How to wildcards work in an application rule target FQDN?

If you configure ***.contoso.com**, it allows *anyvalue*.contoso.com, but not contoso.com (the domain apex). If you want to allow the domain apex, you must explicitly configure it as a target FQDN.
