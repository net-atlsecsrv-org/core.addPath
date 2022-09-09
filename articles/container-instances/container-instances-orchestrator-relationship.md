---
title: Container instances and container orchestration
description: Understand how Azure container instances interact with container orchestrators.
ms.topic: article
ms.date: 04/15/2019
ms.custom: mvc
---

# Azure Container Instances and container orchestrators

Because of their small size and application orientation, containers are well suited for agile delivery environments and microservice-based architectures. The task of automating and managing a large number of containers and how they interact is known as *orchestration*. Popular container orchestrators include Kubernetes, DC/OS, and Docker Swarm.

Azure Container Instances provides some of the basic scheduling capabilities of orchestration platforms. And while it does not cover the higher-value services that those platforms provide, Azure Container Instances can be complementary to them. This article describes the scope of what Azure Container Instances handles, and how full container orchestrators might interact with it.

## Traditional orchestration

The standard definition of orchestration includes the following tasks:

- **Scheduling**: Given a container image and a resource request, find a suitable machine on which to run the container.
- **Affinity/Anti-affinity**: Specify that a set of containers should run nearby each other (for performance) or sufficiently far apart (for availability).
- **Health monitoring**: Watch for container failures and automatically reschedule them.
- **Failover**: Keep track of what is running on each machine, and reschedule containers from failed machines to healthy nodes.
- **Scaling**: Add or remove container instances to match demand, either manually or automatically.
- **Networking**: Provide an overlay network for coordinating containers to communicate across multiple host machines.
- **Service discovery**: Enable containers to locate each other automatically, even as they move between host machines and change IP addresses.
- **Coordinated application upgrades**: Manage container upgrades to avoid application downtime, and enable rollback if something goes wrong.

## Orchestration with Azure Container Instances: A layered approach

Azure Container Instances enables a layered approach to orchestration, providing all of the scheduling and management capabilities required to run a single container, while allowing orchestrator platforms to manage multi-container tasks on top of it.

Because the underlying infrastructure for container instances is managed by Azure, an orchestrator platform does not need to concern itself with finding an appropriate host machine on which to run a single container. The elasticity of the cloud ensures that one is always available. Instead, the orchestrator can focus on the tasks that simplify the development of multi-container architectures, including scaling and coordinated upgrades.

## Scenarios

While orchestrator integration with Azure Container Instances is still nascent, we anticipate that a few different environments will emerge:

### Orchestration of container instances exclusively

Because they start quickly and bill by the second, an environment based exclusively on Azure Container Instances offers the fastest way to get started and to deal with highly variable workloads.

### Combination of container instances and containers in Virtual Machines

For long-running, stable workloads, orchestrating containers in a cluster of dedicated virtual machines is typically cheaper than running the same containers with Azure Container Instances. However, container instances offer a great solution for quickly expanding and contracting your overall capacity to deal with unexpected or short-lived spikes in usage.

Rather than scaling out the number of virtual machines in your cluster, then deploying additional containers onto those machines, the orchestrator can simply schedule the additional containers in Azure Container Instances, and delete them when they're no longer needed.

## Sample implementation: virtual nodes for Azure Kubernetes Service (AKS)

To rapidly scale application workloads in an [Azure Kubernetes Service](../aks/intro-kubernetes.md) (AKS) cluster, you can use *virtual nodes* created dynamically in Azure Container Instances. Virtual nodes enable network communication between pods that run in ACI and the AKS cluster. 

Virtual nodes currently support Linux container instances. Get started with virtual nodes using the [Azure CLI](https://go.microsoft.com/fwlink/?linkid=2047538) or [Azure portal](https://go.microsoft.com/fwlink/?linkid=2047545).

Virtual nodes use the open source [Virtual Kubelet][aci-connector-k8s] to mimic the Kubernetes [kubelet][kubelet-doc] by registering as a node with unlimited capacity. The Virtual Kubelet dispatches the creation of [pods][pod-doc] as container groups in Azure Container Instances.

See the [Virtual Kubelet](https://github.com/virtual-kubelet/virtual-kubelet) project for additional examples of extending the Kubernetes API into serverless container platforms.

## Next steps

Create your first container with Azure Container Instances using the [quickstart guide](container-instances-quickstart.md).

<!-- IMAGES -->

<!-- LINKS -->
[aci-connector-k8s]: https://github.com/virtual-kubelet/azure-aci
[kubelet-doc]: https://kubernetes.io/docs/admin/kubelet/
[pod-doc]: https://kubernetes.io/docs/concepts/workloads/pods/pod/
