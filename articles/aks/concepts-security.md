---
title: Concepts - Security in Azure Kubernetes Services (AKS)
description: Learn about security in Azure Kubernetes Service (AKS), including master and node communication, network policies, and Kubernetes secrets.
services: container-service
author: mlearned
ms.topic: conceptual
ms.date: 07/01/2020
ms.author: mlearned
---

# Security concepts for applications and clusters in Azure Kubernetes Service (AKS)

To protect your customer data as you run application workloads in Azure Kubernetes Service (AKS), the security of your cluster is a key consideration. Kubernetes includes security components such as *network policies* and *Secrets*. Azure then adds in components such as network security groups and orchestrated cluster upgrades. These security components are combined to keep your AKS cluster running the latest OS security updates and Kubernetes releases, and with secure pod traffic and access to sensitive credentials.

This article introduces the core concepts that secure your applications in AKS:

- [Security concepts for applications and clusters in Azure Kubernetes Service (AKS)](#security-concepts-for-applications-and-clusters-in-azure-kubernetes-service-aks)
  - [Master security](#master-security)
  - [Node security](#node-security)
    - [Compute isolation](#compute-isolation)
  - [Cluster upgrades](#cluster-upgrades)
    - [Cordon and drain](#cordon-and-drain)
  - [Network security](#network-security)
    - [Azure network security groups](#azure-network-security-groups)
  - [Kubernetes Secrets](#kubernetes-secrets)
  - [Next steps](#next-steps)

## Master security

In AKS, the Kubernetes master components are part of the managed service provided by Microsoft. Each AKS cluster has its own single-tenanted, dedicated Kubernetes master to provide the API Server, Scheduler, etc. This master is managed and maintained by Microsoft.

By default, the Kubernetes API server uses a public IP address and a fully qualified domain name (FQDN). You can limit access to the API server endpoint using [authorized IP ranges][authorized-ip-ranges]. You can also create a fully [private cluster][private-clusters] to limit API server access to your virtual network.

You can control access to the API server using Kubernetes role-based access control (RBAC) and Azure Active Directory. For more information, see [Azure AD integration with AKS][aks-aad].

## Node security

AKS nodes are Azure virtual machines that you manage and maintain. Linux nodes run an optimized Ubuntu distribution using the Moby container runtime. Windows Server nodes run an optimized Windows Server 2019 release and also use the Moby container runtime. When an AKS cluster is created or scaled up, the nodes are automatically deployed with the latest OS security updates and configurations.

The Azure platform automatically applies OS security patches to Linux nodes on a nightly basis. If a Linux OS security update requires a host reboot, that reboot is not automatically performed. You can manually reboot the Linux nodes, or a common approach is to use [Kured][kured], an open-source reboot daemon for Kubernetes. Kured runs as a [DaemonSet][aks-daemonsets] and monitors each node for the presence of a file indicating that a reboot is required. Reboots are managed across the cluster using the same [cordon and drain process](#cordon-and-drain) as a cluster upgrade.

For Windows Server nodes, Windows Update does not automatically run and apply the latest updates. On a regular schedule around the Windows Update release cycle and your own validation process, you should perform an upgrade on the Windows Server node pool(s) in your AKS cluster. This upgrade process creates nodes that run the latest Windows Server image and patches, then removes the older nodes. For more information on this process, see [Upgrade a node pool in AKS][nodepool-upgrade].

Nodes are deployed into a private virtual network subnet, with no public IP addresses assigned. For troubleshooting and management purposes, SSH is enabled by default. This SSH access is only available using the internal IP address.

To provide storage, the nodes use Azure Managed Disks. For most VM node sizes, these are Premium disks backed by high-performance SSDs. The data stored on managed disks is automatically encrypted at rest within the Azure platform. To improve redundancy, these disks are also securely replicated within the Azure datacenter.

Kubernetes environments, in AKS or elsewhere, currently aren't completely safe for hostile multi-tenant usage. Additional security features like *Pod Security Policies*, or more fine-grained role-based access control (RBAC) for nodes, make exploits more difficult. However, for true security when running hostile multi-tenant workloads, a hypervisor is the only level of security that you should trust. The security domain for Kubernetes becomes the entire cluster, not an individual node. For these types of hostile multi-tenant workloads, you should use physically isolated clusters. For more information on ways to isolate workloads, see [Best practices for cluster isolation in AKS][cluster-isolation].

### Compute isolation

 Certain workloads may require a high degree of isolation from other customer workloads due to compliance or regulatory requirements. For these workloads, Azure provides [isolated virtual machines](../virtual-machines/isolation.md), which can be used as the agent nodes in an AKS cluster. These isolated virtual machines are isolated to a specific hardware type and dedicated to a single customer. 

 To use these isolated virtual machines with an AKS cluster, select one of the isolated virtual machines sizes listed [here](../virtual-machines/isolation.md) as the **Node size** when creating an AKS cluster or adding a node pool.


## Cluster upgrades

For security and compliance, or to use the latest features, Azure provides tools to orchestrate the upgrade of an AKS cluster and components. This upgrade orchestration includes both the Kubernetes master and agent components. You can view a [list of available Kubernetes versions](supported-kubernetes-versions.md) for your AKS cluster. To start the upgrade process, you specify one of these available versions. Azure then safely cordons and drains each AKS node and performs the upgrade.

### Cordon and drain

During the upgrade process, AKS nodes are individually cordoned from the cluster so new pods aren't scheduled on them. The nodes are then drained and upgraded as follows:

- A new node is deployed into the node pool. This node runs the latest OS image and patches.
- One of the existing nodes is identified for upgrade. Pods on this node are gracefully terminated and scheduled on the other nodes in the node pool.
- This existing node is deleted from the AKS cluster.
- The next node in the cluster is cordoned and drained using the same process until all nodes are successfully replaced as part of the upgrade process.

For more information, see [Upgrade an AKS cluster][aks-upgrade-cluster].

## Network security

For connectivity and security with on-premises networks, you can deploy your AKS cluster into existing Azure virtual network subnets. These virtual networks may have an Azure Site-to-Site VPN or Express Route connection back to your on-premises network. Kubernetes ingress controllers can be defined with private, internal IP addresses so services are only accessible over this internal network connection.

### Azure network security groups

To filter the flow of traffic in virtual networks, Azure uses network security group rules. These rules define the source and destination IP ranges, ports, and protocols that are allowed or denied access to resources. Default rules are created to allow TLS traffic to the Kubernetes API server. As you create services with load balancers, port mappings, or ingress routes, AKS automatically modifies the network security group for traffic to flow appropriately.

In cases where you provide your own subnet for your AKS cluster and you wish to modify the flow of traffic, do not modify the subnet-level network security group managed by AKS. You may create additional subnet-level network security groups to modify the flow of traffic as long as they do not interfere with traffic needed for managing the cluster, such as load balancer access, communication with the control plane, and [egress][aks-limit-egress-traffic].

### Kubernetes network policy

To limit network traffic between pods in your cluster, AKS offers support for [Kubernetes network policies][network-policy]. With network policies, you can choose to allow or deny specific network paths within the cluster based on namespaces and label selectors.

## Kubernetes Secrets

A Kubernetes *Secret* is used to inject sensitive data into pods, such as access credentials or keys. You first create a Secret using the Kubernetes API. When you define your pod or deployment, a specific Secret can be requested. Secrets are only provided to nodes that have a scheduled pod that requires it, and the Secret is stored in *tmpfs*, not written to disk. When the last pod on a node that requires a Secret is deleted, the Secret is deleted from the node's tmpfs. Secrets are stored within a given namespace and can only be accessed by pods within the same namespace.

The use of Secrets reduces the sensitive information that is defined in the pod or service YAML manifest. Instead, you request the Secret stored in Kubernetes API Server as part of your YAML manifest. This approach only provides the specific pod access to the Secret. Please note: the raw secret manifest files contains the secret data in base64 format (see the [official documentation][secret-risks] for more details). Therefore, this file should be treated as sensitive information, and never committed to source control.

Kubernetes secrets are stored in etcd, a distributed key-value store. Etcd store is fully managed by AKS and [data is encrypted at rest within the Azure platform][encryption-atrest]. 

## Next steps

To get started with securing your AKS clusters, see [Upgrade an AKS cluster][aks-upgrade-cluster].

For associated best practices, see [Best practices for cluster security and upgrades in AKS][operator-best-practices-cluster-security] and [Best practices for pod security in AKS][developer-best-practices-pod-security].

For additional information on core Kubernetes and AKS concepts, see the following articles:

- [Kubernetes / AKS clusters and workloads][aks-concepts-clusters-workloads]
- [Kubernetes / AKS identity][aks-concepts-identity]
- [Kubernetes / AKS virtual networks][aks-concepts-network]
- [Kubernetes / AKS storage][aks-concepts-storage]
- [Kubernetes / AKS scale][aks-concepts-scale]

<!-- LINKS - External -->
[kured]: https://github.com/weaveworks/kured
[kubernetes-network-policies]: https://kubernetes.io/docs/concepts/services-networking/network-policies/
[secret-risks]: https://kubernetes.io/docs/concepts/configuration/secret/#risks
[encryption-atrest]: ../security/fundamentals/encryption-atrest.md

<!-- LINKS - Internal -->
[aks-daemonsets]: concepts-clusters-workloads.md#daemonsets
[aks-upgrade-cluster]: upgrade-cluster.md
[aks-aad]: ./managed-aad.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
[aks-limit-egress-traffic]: limit-egress-traffic.md
[cluster-isolation]: operator-best-practices-cluster-isolation.md
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[developer-best-practices-pod-security]:developer-best-practices-pod-security.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[authorized-ip-ranges]: api-server-authorized-ip-ranges.md
[private-clusters]: private-clusters.md
[network-policy]: use-network-policies.md
