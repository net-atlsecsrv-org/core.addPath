---
title: Introduction to container groups
description: Learn about container groups in Azure Container Instances, a collection of instances that share a lifecycle and resources such as storage and network
ms.topic: article
ms.date: 11/01/2019
ms.custom: mvc

---
# Container groups in Azure Container Instances

The top-level resource in Azure Container Instances is the *container group*. This article describes what container groups are and the types of scenarios they enable.

## What is a container group?

A container group is a collection of containers that get scheduled on the same host machine. The containers in a container group share a lifecycle, resources, local network, and storage volumes. It's similar in concept to a *pod* in [Kubernetes][kubernetes-pod].

The following diagram shows an example of a container group that includes multiple containers:

![Container groups diagram][container-groups-example]

This example container group:

* Is scheduled on a single host machine.
* Is assigned a DNS name label.
* Exposes a single public IP address, with one exposed port.
* Consists of two containers. One container listens on port 80, while the other listens on port 5000.
* Includes two Azure file shares as volume mounts, and each container mounts one of the shares locally.

> [!NOTE]
> Multi-container groups currently support only Linux containers. For Windows containers, Azure Container Instances only supports deployment of a single instance. While we are working to bring all features to Windows containers, you can find current platform differences in the service [Overview](container-instances-overview.md#linux-and-windows-containers).

## Deployment

Here are two common ways to deploy a multi-container group: use a [Resource Manager template][resource-manager template] or a [YAML file][yaml-file]. A Resource Manager template is recommended when you need to deploy additional Azure service resources (for example, an [Azure Files share][azure-files]) when you deploy the container instances. Due to the YAML format's more concise nature, a YAML file is recommended when your deployment includes only container instances. For details on properties you can set, see the [Resource Manager template reference](/azure/templates/microsoft.containerinstance/containergroups) or [YAML reference](container-instances-reference-yaml.md) documentation.

To preserve a container group's configuration, you can export the configuration to a YAML file by using the Azure CLI command [az container export][az-container-export]. Export allows you to store your container group configurations in version control for "configuration as code." Or, use the exported file as a starting point when developing a new configuration in YAML.



## Resource allocation

Azure Container Instances allocates resources such as CPUs, memory, and optionally [GPUs][gpus] (preview) to a multi-container group by adding the [resource requests][resource-requests] of the instances in the group. Taking CPU resources as an example, if you create a container group with two instances, each requesting 1 CPU, then the container group is allocated 2 CPUs.

### Resource usage by instances

Each container instance in a group is allocated the resources specified in its resource request. However, the maximum resources used by an instance in a group could be different if you configure its optional [resource limit][resource-limits] property. The resource limit of an instance must be greater than or equal to the mandatory [resource request][resource-requests] property.

* If you don't specify a resource limit, the instance's maximum resource usage is the same as its resource request.

* If you specify a limit for an instance, the instance's maximum usage could be greater than the request, up to the limit you set. Correspondingly, resource usage by other instances in the group could decrease. The maximum resource limit you can set for an instance is the total resources allocated to the group.
    
For example, in a group with two instances each requesting 1 CPU, one of your containers might run a workload that requires more CPUs to run than the other.

In this scenario, you could set a resource limit of 2 CPUs for the instance. This configuration allows the container to use up to the full 2 CPUs if available.

### Minimum and maximum allocation

* Allocate a **minimum** of 1 CPU and 1 GB of memory to a container group. Individual container instances within a group can be provisioned with less than 1 CPU and 1 GB of memory. 

* For the **maximum** resources in a container group, see the [resource availability][region-availability] for Azure Container Instances in the deployment region.

## Networking

Container groups can share an external-facing IP address and a port namespace on that IP address. To enable external clients to reach a container within the group, you must expose the port on the IP address and from the container. Because containers within the group share a port namespace, port mapping isn't supported. 

Within a container group, containers instances can reach each other via localhost on any port, even if those ports aren't exposed externally on the group's IP address or from the container.

Optionally deploy container groups into an [Azure virtual network][virtual-network] (preview) to allow containers to communicate securely with other resources in the virtual network.

## Storage

You can specify external volumes to mount within a container group. You can map those volumes into specific paths within the individual containers in a group.

## Common scenarios

Multi-container groups are useful in cases where you want to divide a single functional task into a small number of container images. These images can then be delivered by different teams and have separate resource requirements.

Example usage could include:

* A container serving a web application and a container pulling the latest content from source control.
* An application container and a logging container. The logging container collects the logs and metrics output by the main application and writes them to long-term storage.
* An application container and a monitoring container. The monitoring container periodically makes a request to the application to ensure that it's running and responding correctly, and raises an alert if it's not.
* A front-end container and a back-end container. The front end might serve a web application, with the back end running a service to retrieve data. 

## Next steps

Learn how to deploy a multi-container container group with an Azure Resource Manager template:

> [!div class="nextstepaction"]
> [Deploy a container group][resource-manager template]

<!-- IMAGES -->
[container-groups-example]: ./media/container-instances-container-groups/container-groups-example.png

<!-- LINKS - External -->
[dcos-pod]: https://dcos.io/docs/1.10/deploying-services/pods/
[kubernetes-pod]: https://kubernetes.io/docs/concepts/workloads/pods/pod/

<!-- LINKS - Internal -->
[resource-manager template]: container-instances-multi-container-group.md
[yaml-file]: container-instances-multi-container-yaml.md
[region-availability]: container-instances-region-availability.md
[resource-requests]: /rest/api/container-instances/containergroups/createorupdate#resourcerequests
[resource-limits]: /rest/api/container-instances/containergroups/createorupdate#resourcelimits
[resource-requirements]: /rest/api/container-instances/containergroups/createorupdate#resourcerequirements
[azure-files]: container-instances-volume-azure-files.md
[virtual-network]: container-instances-vnet.md
[gpus]: container-instances-gpu.md
[az-container-export]: /cli/azure/container#az-container-export
