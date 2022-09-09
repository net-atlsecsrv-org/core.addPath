---
title: Plan a virtual network for Azure HDInsight
description: Learn how to plan an Azure Virtual Network deployment to connect HDInsight to other cloud resources, or resources in your datacenter.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/25/2020
---

# Plan a virtual network for Azure HDInsight

This article provides background information on using [Azure Virtual Networks](../virtual-network/virtual-networks-overview.md) (VNets) with Azure HDInsight. It also discusses design and implementation decisions that must be made before you can implement a virtual network for your HDInsight cluster. Once the planning phase is finished, you can proceed to [Create virtual networks for Azure HDInsight clusters](hdinsight-create-virtual-network.md). For more information on HDInsight management IP addresses that are needed to properly configure network security groups (NSGs) and user-defined routes, see [HDInsight management IP addresses](hdinsight-management-ip-addresses.md).

Using an Azure Virtual Network enables the following scenarios:

* Connecting to HDInsight directly from an on-premises network.
* Connecting HDInsight to data stores in an Azure Virtual network.
* Directly accessing Apache Hadoop services that aren't available publicly over the internet. For example, Apache Kafka APIs or the Apache HBase Java API.

> [!IMPORTANT]
> Creating an HDInsight cluster in a VNET will create several networking resources, such as NICs and load balancers. Do **not** delete these networking resources, as they are needed for your cluster to function correctly with the VNET.
>
> After Feb 28, 2019, the networking resources (such as NICs, LBs, etc) for NEW HDInsight clusters created in a VNET will be provisioned in the same HDInsight cluster resource group. Previously, these resources were provisioned in the VNET resource group. There is no change to the current running clusters and those clusters created without a VNET.

## Planning

The following are the questions that you must answer when planning to install HDInsight in a virtual network:

* Do you need to install HDInsight into an existing virtual network? Or are you creating a new network?

    If you're using an existing virtual network, you may need to modify the network configuration before you can install HDInsight. For more information, see the [add HDInsight to an existing virtual network](#existingvnet) section.

* Do you want to connect the virtual network containing HDInsight to another virtual network or your on-premises network?

    To easily work with resources across networks, you may need to create a custom DNS and configure DNS forwarding. For more information, see the [connecting multiple networks](#multinet) section.

* Do you want to restrict/redirect inbound or outbound traffic to HDInsight?

    HDInsight must have unrestricted communication with specific IP addresses in the Azure data center. There are also several ports that must be allowed through firewalls for client communication. For more information, see the [controlling network traffic](#networktraffic) section.

## <a id="existingvnet"></a>Add HDInsight to an existing virtual network

Use the steps in this section to discover how to add a new HDInsight to an existing Azure Virtual Network.

> [!NOTE]  
> You cannot add an existing HDInsight cluster into a virtual network.

1. Are you using a classic or Resource Manager deployment model for the virtual network?

    HDInsight 3.4 and greater requires a Resource Manager virtual network. Earlier versions of HDInsight required a classic virtual network.

    If your existing network is a classic virtual network, then you must create a Resource Manager virtual network and then connect the two. [Connecting classic VNets to new VNets](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

    Once joined, HDInsight installed in the Resource Manager network can interact with resources in the classic network.

2. Do you use network security groups, user-defined routes, or Virtual Network Appliances to restrict traffic into or out of the virtual network?

    As a managed service, HDInsight requires unrestricted access to several IP addresses in the Azure data center. To allow communication with these IP addresses, update any existing network security groups or user-defined routes.

    HDInsight  hosts multiple services, which use a variety of ports. Don't block traffic to these ports. For a list of ports to allow through virtual appliance firewalls, see the Security section.

    To find your existing security configuration, use the following Azure PowerShell or Azure CLI commands:

    * Network security groups

        Replace `RESOURCEGROUP` with the name of the resource group that contains the virtual network, and then enter the command:

        ```powershell
        Get-AzNetworkSecurityGroup -ResourceGroupName  "RESOURCEGROUP"
        ```

        ```azurecli
        az network nsg list --resource-group RESOURCEGROUP
        ```

        For more information, see the [Troubleshoot network security groups](../virtual-network/diagnose-network-traffic-filter-problem.md) document.

        > [!IMPORTANT]  
        > Network security group rules are applied in order based on rule priority. The first rule that matches the traffic pattern is applied, and no others are applied for that traffic. Order rules from most permissive to least permissive. For more information, see the [Filter network traffic with network security groups](../virtual-network/security-overview.md) document.

    * User-defined routes

        Replace `RESOURCEGROUP` with the name of the resource group that contains the virtual network, and then enter the command:

        ```powershell
        Get-AzRouteTable -ResourceGroupName "RESOURCEGROUP"
        ```

        ```azurecli
        az network route-table list --resource-group RESOURCEGROUP
        ```

        For more information, see the [Troubleshoot routes](../virtual-network/diagnose-network-routing-problem.md) document.

3. Create an HDInsight cluster and select the Azure Virtual Network during configuration. Use the steps in the following documents to understand the cluster creation process:

    * [Create HDInsight using the Azure portal](hdinsight-hadoop-create-linux-clusters-portal.md)
    * [Create HDInsight using Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)
    * [Create HDInsight using Azure Classic CLI](hdinsight-hadoop-create-linux-clusters-azure-cli.md)
    * [Create HDInsight using an Azure Resource Manager template](hdinsight-hadoop-create-linux-clusters-arm-templates.md)

   > [!IMPORTANT]  
   > Adding HDInsight to a virtual network is an optional configuration step. Be sure to select the virtual network when configuring the cluster.

## <a id="multinet"></a>Connecting multiple networks

The biggest challenge with a multi-network configuration is name resolution between the networks.

Azure provides name resolution for Azure services that are installed in a virtual network. This built-in name resolution allows HDInsight to connect to the following resources by using a fully qualified domain name (FQDN):

* Any resource that is available on the internet. For example, microsoft.com, windowsupdate.com.

* Any resource that is in the same Azure Virtual Network, by using the __internal DNS name__ of the resource. For example, when using the default name resolution, the following are examples of internal DNS names assigned to HDInsight worker nodes:

  * wn0-hdinsi.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net
  * wn2-hdinsi.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net

    Both these nodes can communicate directly with each other, and other nodes in HDInsight, by using internal DNS names.

The default name resolution does __not__ allow HDInsight to resolve the names of resources in networks that are joined to the virtual network. For example, it's common to join your on-premises network to the virtual network. With only the default name resolution, HDInsight can't access resources in the on-premises network by name. The opposite is also true, resources in your on-premises network can't access resources in the virtual network by name.

> [!WARNING]  
> You must create the custom DNS server and configure the virtual network to use it before creating the HDInsight cluster.

To enable name resolution between the virtual network and resources in joined networks, you must perform the following actions:

1. Create a custom DNS server in the Azure Virtual Network where you plan to install HDInsight.

2. Configure the virtual network to use the custom DNS server.

3. Find the Azure assigned DNS suffix for your virtual network. This value is similar to `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net`. For information on finding the DNS suffix, see the [Example: Custom DNS](hdinsight-create-virtual-network.md#example-dns) section.

4. Configure forwarding between the DNS servers. The configuration depends on the type of remote network.

   * If the remote network is an on-premises network, configure DNS as follows:

     * __Custom DNS__ (in the virtual network):

         * Forward requests for the DNS suffix of the virtual network to the Azure recursive resolver (168.63.129.16). Azure handles requests for resources in the virtual network

         * Forward all other requests to the on-premises DNS server. The on-premises DNS handles all other name resolution requests, even requests for internet resources such as Microsoft.com.

     * __On-premises DNS__: Forward requests for the virtual network DNS suffix to the custom DNS server. The custom DNS server then forwards to the Azure recursive resolver.

       This configuration routes requests for fully qualified domain names that contain the DNS suffix of the virtual network to the custom DNS server. All other requests (even for public internet addresses) are handled by the on-premises DNS server.

   * If the remote network is another Azure Virtual Network, configure DNS as follows:

     * __Custom DNS__ (in each virtual network):

         * Requests for the DNS suffix of the virtual networks are forwarded to the custom DNS servers. The DNS in each virtual network is responsible for resolving resources within its network.

         * Forward all other requests to the Azure recursive resolver. The recursive resolver is responsible for resolving local and internet resources.

       The DNS server for each network forwards requests to the other, based on DNS suffix. Other requests are resolved using the Azure recursive resolver.

     For an example of each configuration, see the [Example: Custom DNS](hdinsight-create-virtual-network.md#example-dns) section.

For more information, see the [Name Resolution for VMs and Role Instances](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) document.

## Directly connect to Apache Hadoop services

You can connect to the cluster at `https://CLUSTERNAME.azurehdinsight.net`. This address uses a public IP, which may not be reachable if you have used NSGs to restrict incoming traffic from the internet. Additionally, when you deploy the cluster in a VNet you can access it using the private endpoint `https://CLUSTERNAME-int.azurehdinsight.net`. This endpoint resolves to a private IP inside the VNet for cluster access.

To connect to Apache Ambari and other web pages through the virtual network, use the following steps:

1. To discover the internal fully qualified domain names (FQDN) of the HDInsight cluster nodes, use one of the following methods:

    Replace `RESOURCEGROUP` with the name of the resource group that contains the virtual network, and then enter the command:

	```powershell
	$clusterNICs = Get-AzNetworkInterface -ResourceGroupName "RESOURCEGROUP" | where-object {$_.Name -like "*node*"}

	$nodes = @()
	foreach($nic in $clusterNICs) {
		$node = new-object System.Object
		$node | add-member -MemberType NoteProperty -name "Type" -value $nic.Name.Split('-')[1]
		$node | add-member -MemberType NoteProperty -name "InternalIP" -value $nic.IpConfigurations.PrivateIpAddress
		$node | add-member -MemberType NoteProperty -name "InternalFQDN" -value $nic.DnsSettings.InternalFqdn
		$nodes += $node
	}
	$nodes | sort-object Type
    ```

	```azurecli
	az network nic list --resource-group RESOURCEGROUP --output table --query "[?contains(name,'node')].{NICname:name,InternalIP:ipConfigurations[0].privateIpAddress,InternalFQDN:dnsSettings.internalFqdn}"
    ```

    In the list of nodes returned, find the FQDN for the head nodes and use the FQDNs to connect to Ambari and other web services. For example, use `http://<headnode-fqdn>:8080` to access Ambari.

    > [!IMPORTANT]  
    > Some services hosted on the head nodes are only active on one node at a time. If you try accessing a service on one head node and it returns a 404 error, switch to the other head node.

2. To determine the node and port that a service is available on, see the [Ports used by Hadoop services on HDInsight](./hdinsight-hadoop-port-settings-for-services.md) document.

## <a id="networktraffic"></a> Controlling network traffic

### Techniques for controlling inbound and outbound traffic to HDInsight clusters

Network traffic in an Azure Virtual Networks can be controlled using the following methods:

* **Network security groups** (NSG) allow you to filter inbound and outbound traffic to the network. For more information, see the [Filter network traffic with network security groups](../virtual-network/security-overview.md) document.

* **Network virtual appliances** (NVA) can be used with outbound traffic only. NVAs replicate the functionality of devices such as firewalls and routers. For more information, see the [Network Appliances](https://azure.microsoft.com/solutions/network-appliances) document.

As a managed service, HDInsight requires unrestricted access to the HDInsight health and management services both for incoming and outgoing traffic from the VNET. When using NSGs, you must ensure that these services can still communicate with HDInsight cluster.

![Diagram of HDInsight entities created in Azure custom VNET](./media/hdinsight-plan-virtual-network-deployment/hdinsight-vnet-diagram.png)

### HDInsight with network security groups

If you plan on using **network security groups** to control network traffic, perform the following actions before installing HDInsight:

1. Identify the Azure region that you plan to use for HDInsight.

2. Identify the service tags required by HDInsight for your region. For more information, see [Network security group (NSG) service tags for Azure HDInsight](hdinsight-service-tags.md).

3. Create or modify the network security groups for the subnet that you plan to install HDInsight into.

    * __Network security groups__: allow __inbound__ traffic on port __443__ from the IP addresses. This will ensure that HDInsight management services can reach the cluster from outside the virtual network.

For more information on network security groups, see the [overview of network security groups](../virtual-network/security-overview.md).

### Controlling outbound traffic from HDInsight clusters

For more information on controlling outbound traffic from HDInsight clusters, see [Configure outbound network traffic restriction for Azure HDInsight clusters](hdinsight-restrict-outbound-traffic.md).

#### Forced tunneling to on-premises

Forced tunneling is a user-defined routing configuration where all traffic from a subnet is forced to a specific network or location, such as your on-premises network. HDInsight does __not__ support forced tunneling of traffic to on-premises networks.

## <a id="hdinsight-ip"></a> Required IP addresses

If you use network security groups or user-defined routes to control traffic, see [HDInsight management IP addresses](hdinsight-management-ip-addresses.md).

## <a id="hdinsight-ports"></a> Required ports

If you plan on using a **firewall** and access the cluster from outside on certain ports, you might need to allow traffic on those ports needed for your scenario. By default, no special whitelisting of ports is needed as long as the azure management traffic explained in the previous section is allowed to reach cluster on port 443.

For a list of ports for specific services, see the [Ports used by Apache Hadoop services on HDInsight](hdinsight-hadoop-port-settings-for-services.md) document.

For more information on firewall rules for virtual appliances, see the [virtual appliance scenario](../virtual-network/virtual-network-scenario-udr-gw-nva.md) document.

## Load balancing

When you create an HDInsight cluster, a load balancer is created as well. The type of this load balancer is at the [basic SKU level](../load-balancer/concepts-limitations.md#skus), which has certain constraints. One of these constraints is that if you have two virtual networks in different regions, you cannot connect to basic load balancers. See [virtual networks FAQ: constraints on global vnet peering](../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers), for more information.

## Transport Layer Security

Connections to the cluster via the public cluster endpoint `https://<clustername>.azurehdinsight.net` are proxied through cluster gateway nodes. These connections are secured using a protocol called TLS. Enforcing higher versions of TLS on gateways improves the security for these connections. For more information on why you should use newer versions of TLS, see [Solving the TLS 1.0 Problem](https://docs.microsoft.com/security/solving-tls1-problem).

By default, Azure HDInsight clusters accept TLS 1.2 connections on public HTTPS endpoints, as well as older versions for backward compatibility. You can control the minimum TLS version supported on the gateway nodes during cluster creation using either the Azure portal, or a resource manager template. For the portal, select the TLS version from the **Security + networking** tab during cluster creation. For a resource manager template at deployment time, use the **minSupportedTlsVersion** property. For a sample template, see [HDInsight minimum TLS 1.2 Quickstart template](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-minimum-tls). This property supports three values: "1.0", "1.1" and "1.2", which correspond to TLS 1.0+, TLS 1.1+ and TLS 1.2+ respectively.

> [!IMPORTANT]
> Starting on June 30, 2020, Azure HDInsight will enforce TLS 1.2 or later versions for all HTTPS connections. We recommend that you ensure that all your clients are ready to handle TLS 1.2 or later versions. For more information, see [Azure HDInsight TLS 1.2 Enforcement](https://azure.microsoft.com/updates/azure-hdinsight-tls-12-enforcement/).

## Next steps

* For code samples and examples of creating Azure Virtual Networks, see [Create virtual networks for Azure HDInsight clusters](hdinsight-create-virtual-network.md).
* For an end-to-end example of configuring HDInsight to connect to an on-premises network, see [Connect HDInsight to an on-premises network](./connect-on-premises-network.md).
* For configuring Apache HBase clusters in Azure virtual networks, see [Create Apache HBase clusters on HDInsight in Azure Virtual Network](hbase/apache-hbase-provision-vnet.md).
* For configuring Apache HBase geo-replication, see [Set up Apache HBase cluster replication in Azure virtual networks](hbase/apache-hbase-replication.md).
* For more information on Azure virtual networks, see the [Azure Virtual Network overview](../virtual-network/virtual-networks-overview.md).
* For more information on network security groups, see [Network security groups](../virtual-network/security-overview.md).
* For more information on user-defined routes, see [User-defined routes and IP forwarding](../virtual-network/virtual-networks-udr-overview.md).
