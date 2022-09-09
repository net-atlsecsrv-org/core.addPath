---
title: Concepts - Networking in Azure Kubernetes Services (AKS)
description: Learn about networking in Azure Kubernetes Service (AKS), including kubenet and Azure CNI networking, ingress controllers, load balancers, and static IP addresses.
ms.topic: conceptual
ms.date: 03/11/2021
ms.custom: fasttrack-edit

---

# Network concepts for applications in Azure Kubernetes Service (AKS)

In a container-based, microservices approach to application development, application components work together to process their tasks. Kubernetes provides various resources enabling this cooperation:
* You can connect to and expose applications internally or externally. 
* You can build highly available applications by load balancing your applications. 
* For your more complex applications, you can configure ingress traffic for SSL/TLS termination or routing of multiple components. 
* For security reasons, you can restrict the flow of network traffic into or between pods and nodes.

This article introduces the core concepts that provide networking to your applications in AKS:

- [Services](#services)
- [Azure virtual networks](#azure-virtual-networks)
- [Ingress controllers](#ingress-controllers)
- [Network policies](#network-policies)

## Kubernetes basics

To allow access to your applications or between application components, Kubernetes provides an abstraction layer to virtual networking. Kubernetes nodes connect to a virtual network, providing inbound and outbound connectivity for pods. The *kube-proxy* component runs on each node to provide these network features.

In Kubernetes:
* *Services* logically group pods to allow for direct access on a specific port via an IP address or DNS name. 
* You can distribute traffic using a *load balancer*. 
* More complex routing of application traffic can also be achieved with *Ingress Controllers*. 
* Security and filtering of the network traffic for pods is possible with Kubernetes *network policies*.

The Azure platform also simplifies virtual networking for AKS clusters. When you create a Kubernetes load balancer, you also create and configure the underlying Azure load balancer resource. As you open network ports to pods, the corresponding Azure network security group rules are configured. For HTTP application routing, Azure can also configure *external DNS* as new ingress routes are configured.

## Services

To simplify the network configuration for application workloads, Kubernetes uses *Services* to logically group a set of pods together and provide network connectivity. The following Service types are available:

- **Cluster IP** 
  
  Creates an internal IP address for use within the AKS cluster. Good for internal-only applications that support other workloads within the cluster.

    ![Diagram showing Cluster IP traffic flow in an AKS cluster][aks-clusterip]

- **NodePort** 

  Creates a port mapping on the underlying node that allows the application to be accessed directly with the node IP address and port.

    ![Diagram showing NodePort traffic flow in an AKS cluster][aks-nodeport]

- **LoadBalancer** 

  Creates an Azure load balancer resource, configures an external IP address, and connects the requested pods to the load balancer backend pool. To allow customers' traffic to reach the application, load balancing rules are created on the desired ports. 

    ![Diagram showing Load Balancer traffic flow in an AKS cluster][aks-loadbalancer]

    For extra control and routing of the inbound traffic, you may instead use an [Ingress controller](#ingress-controllers).

- **ExternalName** 

  Creates a specific DNS entry for easier application access.

Either the load balancers and services IP address can be dynamically assigned, or you can specify an existing static IP address. You can assign both internal and external static IP addresses. Existing static IP addresses are often tied to a DNS entry.

You can create both *internal* and *external* load balancers. Internal load balancers are only assigned a private IP address, so they can't be accessed from the Internet.

## Azure virtual networks

In AKS, you can deploy a cluster that uses one of the following two network models:

- *Kubenet* networking

  The network resources are typically created and configured as the AKS cluster is deployed.

- *Azure Container Networking Interface (CNI)* networking
 
  The AKS cluster is connected to existing virtual network resources and configurations.

### Kubenet (basic) networking

The *kubenet* networking option is the default configuration for AKS cluster creation. With *kubenet*:
1. Nodes receive an IP address from the Azure virtual network subnet. 
1. Pods receive an IP address from a logically different address space than the nodes' Azure virtual network subnet. 
1. Network address translation (NAT) is then configured so that the pods can reach resources on the Azure virtual network. 
1. The source IP address of the traffic is translated to the node's primary IP address.

Nodes use the [kubenet][kubenet] Kubernetes plugin. You can:
* Let the Azure platform create and configure the virtual networks for you, or 
* Choose to deploy your AKS cluster into an existing virtual network subnet. 

Remember, only the nodes receive a routable IP address. The pods use NAT to communicate with other resources outside the AKS cluster. This approach reduces the number of IP addresses you need to reserve in your network space for pods to use.

For more information, see [Configure kubenet networking for an AKS cluster][aks-configure-kubenet-networking].

### Azure CNI (advanced) networking

With Azure CNI, every pod gets an IP address from the subnet and can be accessed directly. These IP addresses must be planned in advance and unique across your network space. Each node has a configuration parameter for the maximum number of pods it supports. The equivalent number of IP addresses per node are then reserved up front. Without planning, this approach can lead to IP address exhaustion or the need to rebuild clusters in a larger subnet as your application demands grow.

Unlike kubenet, traffic to endpoints in the same virtual network isn't NAT'd to the node's primary IP. The source address for traffic inside the virtual network is the pod IP. Traffic that's external to the virtual network still NATs to the node's primary IP.

Nodes use the [Azure CNI][cni-networking] Kubernetes plugin.

![Diagram showing two nodes with bridges connecting each to a single Azure VNet][advanced-networking-diagram]

For more information, see [Configure Azure CNI for an AKS cluster][aks-configure-advanced-networking].

### Compare network models

Both kubenet and Azure CNI provide network connectivity for your AKS clusters. However, there are advantages and disadvantages to each. At a high level, the following considerations apply:

* **kubenet**
    * Conserves IP address space.
    * Uses Kubernetes internal or external load balancer to reach pods from outside of the cluster.
    * You manually manage and maintain user-defined routes (UDRs).
    * Maximum of 400 nodes per cluster.
* **Azure CNI**
    * Pods get full virtual network connectivity and can be directly reached via their private IP address from connected networks.
    * Requires more IP address space.

The following behavior differences exist between kubenet and Azure CNI:

| Capability                                                                                   | Kubenet   | Azure CNI |
|----------------------------------------------------------------------------------------------|-----------|-----------|
| Deploy cluster in existing or new virtual network                                            | Supported - UDRs manually applied | Supported |
| Pod-pod connectivity                                                                         | Supported | Supported |
| Pod-VM connectivity; VM in the same virtual network                                          | Works when initiated by pod | Works both ways |
| Pod-VM connectivity; VM in peered virtual network                                            | Works when initiated by pod | Works both ways |
| On-premises access using VPN or Express Route                                                | Works when initiated by pod | Works both ways |
| Access to resources secured by service endpoints                                             | Supported | Supported |
| Expose Kubernetes services using a load balancer service, App Gateway, or ingress controller | Supported | Supported |
| Default Azure DNS and Private Zones                                                          | Supported | Supported |

Regarding DNS, with both kubenet and Azure CNI plugins DNS are offered by CoreDNS, a deployment running in AKS with its own autoscaler. For more information on CoreDNS on Kubernetes, see [Customizing DNS Service](https://kubernetes.io/docs/tasks/administer-cluster/dns-custom-nameservers/). CoreDNS by default is configured to forward unknown domains to the DNS functionality of the Azure Virtual Network where the AKS cluster is deployed. Hence, Azure DNS and Private Zones will work for pods running in AKS.

### Support scope between network models

Whatever network model you use, both kubenet and Azure CNI can be deployed in one of the following ways:

* The Azure platform can automatically create and configure the virtual network resources when you create an AKS cluster.
* You can manually create and configure the virtual network resources and attach to those resources when you create your AKS cluster.

Although capabilities like service endpoints or UDRs are supported with both kubenet and Azure CNI, the [support policies for AKS][support-policies] define what changes you can make. For example:

* If you manually create the virtual network resources for an AKS cluster, you're supported when configuring your own UDRs or service endpoints.
* If the Azure platform automatically creates the virtual network resources for your AKS cluster, you can't manually change those AKS-managed resources to configure your own UDRs or service endpoints.

## Ingress controllers

When you create a LoadBalancer-type Service, you also create an underlying Azure load balancer resource. The load balancer is configured to distribute traffic to the pods in your Service on a given port. 

The LoadBalancer only works at layer 4. At layer 4, the Service is unaware of the actual applications, and can't make any more routing considerations.

*Ingress controllers* work at layer 7, and can use more intelligent rules to distribute application traffic. Ingress controllers typically route HTTP traffic to different applications based on the inbound URL.

![Diagram showing Ingress traffic flow in an AKS cluster][aks-ingress]

### Create an ingress resource
In AKS, you can create an Ingress resource using NGINX, a similar tool, or the AKS HTTP application routing feature. When you enable HTTP application routing for an AKS cluster, the Azure platform creates the Ingress controller and an *External-DNS* controller. As new Ingress resources are created in Kubernetes, the required DNS A records are created in a cluster-specific DNS zone. 

For more information, see [Deploy HTTP application routing][aks-http-routing].

### Application Gateway Ingress Controller (AGIC)

With the Application Gateway Ingress Controller (AGIC) add-on, AKS customers leverage Azure's native Application Gateway level 7 load-balancer to expose cloud software to the Internet. AGIC monitors the host Kubernetes cluster and continuously updates an Application Gateway, exposing selected services to the Internet. 

To learn more about the AGIC add-on for AKS, see [What is Application Gateway Ingress Controller?][agic-overview].

### SSL/TLS termination

SSL/TLS termination is another common feature of Ingress. On large web applications accessed via HTTPS, the Ingress resource handles the TLS termination rather than within the application itself. To provide automatic TLS certification generation and configuration, you can configure the Ingress resource to use providers such as "Let's Encrypt". 

For more information on configuring an NGINX Ingress controller with Let's Encrypt, see [Ingress and TLS][aks-ingress-tls].

### Client source IP preservation

Configure your ingress controller to preserve the client source IP on requests to containers in your AKS cluster. When your ingress controller routes a client's request to a container in your AKS cluster, the original source IP of that request is unavailable to the target container. When you enable *client source IP preservation*, the source IP for the client is available in the request header under *X-Forwarded-For*. 

If you're using client source IP preservation on your ingress controller, you can't use TLS pass-through. Client source IP preservation and TLS pass-through can be used with other services, such as the *LoadBalancer* type.

## Network security groups

A network security group filters traffic for VMs like the AKS nodes. As you create Services, such as a LoadBalancer, the Azure platform automatically configures any necessary network security group rules. 

You don't need to manually configure network security group rules to filter traffic for pods in an AKS cluster. Simply define any required ports and forwarding as part of your Kubernetes Service manifests. Let the Azure platform create or update the appropriate rules. 

You can also use network policies to automatically apply traffic filter rules to pods.

## Network policies

By default, all pods in an AKS cluster can send and receive traffic without limitations. For improved security, define rules that control the flow of traffic, like:
* Backend applications are only exposed to required frontend services. 
* Database components are only accessible to the application tiers that connect to them.

Network policy is a Kubernetes feature available in AKS that lets you control the traffic flow between pods. You allow or deny traffic to the pod based on settings such as assigned labels, namespace, or traffic port. While network security groups are better for AKS nodes, network policies are a more suited, cloud-native way to control the flow of traffic for pods. As pods are dynamically created in an AKS cluster, required network policies can be automatically applied.

For more information, see [Secure traffic between pods using network policies in Azure Kubernetes Service (AKS)][use-network-policies].

## Next steps

To get started with AKS networking, create and configure an AKS cluster with your own IP address ranges using [kubenet][aks-configure-kubenet-networking] or [Azure CNI][aks-configure-advanced-networking].

For associated best practices, see [Best practices for network connectivity and security in AKS][operator-best-practices-network].

For more information on core Kubernetes and AKS concepts, see the following articles:

- [Kubernetes / AKS clusters and workloads][aks-concepts-clusters-workloads]
- [Kubernetes / AKS access and identity][aks-concepts-identity]
- [Kubernetes / AKS security][aks-concepts-security]
- [Kubernetes / AKS storage][aks-concepts-storage]
- [Kubernetes / AKS scale][aks-concepts-scale]

<!-- IMAGES -->
[aks-clusterip]: ./media/concepts-network/aks-clusterip.png
[aks-nodeport]: ./media/concepts-network/aks-nodeport.png
[aks-loadbalancer]: ./media/concepts-network/aks-loadbalancer.png
[advanced-networking-diagram]: ./media/concepts-network/advanced-networking-diagram.png
[aks-ingress]: ./media/concepts-network/aks-ingress.png

<!-- LINKS - External -->
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet

<!-- LINKS - Internal -->
[aks-http-routing]: http-application-routing.md
[aks-ingress-tls]: ./ingress-tls.md
[aks-configure-kubenet-networking]: configure-kubenet.md
[aks-configure-advanced-networking]: configure-azure-cni.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-identity]: concepts-identity.md
[agic-overview]: ../application-gateway/ingress-controller-overview.md
[use-network-policies]: use-network-policies.md
[operator-best-practices-network]: operator-best-practices-network.md
[support-policies]: support-policies.md
