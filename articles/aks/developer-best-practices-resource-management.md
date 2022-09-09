---
title: Resource management best practices
titleSuffix: Azure Kubernetes Service
description: Learn the application developer best practices for resource management in Azure Kubernetes Service (AKS)
services: container-service
author: zr-msft
ms.topic: conceptual
ms.date: 03/15/2021
ms.author: zarhoads
---

# Best practices for application developers to manage resources in Azure Kubernetes Service (AKS)

As you develop and run applications in Azure Kubernetes Service (AKS), there are a few key areas to consider. How you manage your application deployments can negatively impact the end-user experience of services that you provide. To succeed, keep in mind some best practices you can follow as you develop and run applications in AKS.

This article focuses on running your cluster and workloads from an application developer perspective. For information about administrative best practices, see [Cluster operator best practices for isolation and resource management in Azure Kubernetes Service (AKS)][operator-best-practices-isolation]. In this article, you learn:

> [!div class="checklist"]
> * Pod resource requests and limits.
> * Ways to develop and deploy applications with Bridge to Kubernetes and Visual Studio Code.
> * How to use the `kube-advisor` tool to check for issues with deployments.

## Define pod resource requests and limits

> **Best practice guidance**
> 
> Set pod requests and limits on all pods in your YAML manifests. If the AKS cluster uses *resource quotas* and you don't define these values, your deployment may be rejected.

Use pod requests and limits to manage the compute resources within an AKS cluster. Pod requests and limits inform the Kubernetes scheduler which compute resources to assign to a pod.

### Pod CPU/Memory requests
*Pod requests* define a set amount of CPU and memory that the pod needs regularly.

In your pod specifications, it's **best practice and very important** to define these requests and limits based on the above information. If you don't include these values, the Kubernetes scheduler cannot take into account the resources your applications require to aid in scheduling decisions.

Monitor the performance of your application to adjust pod requests. 
* If you underestimate pod requests, your application may receive degraded performance due to over-scheduling a node. 
* If requests are overestimated, your application may have increased difficulty getting scheduled.

### Pod CPU/Memory limits** 
*Pod limits* set the maximum amount of CPU and memory that a pod can use. 

* *Memory limits* define which pods should be killed when nodes are unstable due to insufficient resources. Without proper limits set, pods will be killed until resource pressure is lifted. 
* While a pod may exceed the *CPU limit* periodically, the pod will not be killed for exceeding the CPU limit. 

Pod limits define when a pod has lost control of resource consumption. When it exceeds the limit, the pod is marked for killing. This behavior maintains node health and minimizes impact to pods sharing the node. Not setting a pod limit defaults it to the highest available value on a given node.

Avoid setting a pod limit higher than your nodes can support. Each AKS node reserves a set amount of CPU and memory for the core Kubernetes components. Your application may try to consume too many resources on the node for other pods to successfully run.

Monitor the performance of your application at different times during the day or week. Determine peak demand times and align the pod limits to the resources required to meet maximum needs.

> [!IMPORTANT]
>
> In your pod specifications, define these requests and limits based on the above information. Failing to include these values prevents the Kubernetes scheduler from accounting for resources your applications require to aid in scheduling decisions.

If the scheduler places a pod on a node with insufficient resources, application performance will be degraded. Cluster administrators **must** set *resource quotas* on a namespace that requires you to set resource requests and limits. For more information, see [resource quotas on AKS clusters][resource-quotas].

When you define a CPU request or limit, the value is measured in CPU units. 
* *1.0* CPU equates to one underlying virtual CPU core on the node. 
    * The same measurement is used for GPUs.
* You can define fractions measured in millicores. For example, *100m* is *0.1* of an underlying vCPU core.

In the following basic example for a single NGINX pod, the pod requests *100m* of CPU time, and *128Mi* of memory. The resource limits for the pod are set to *250m* CPU and *256Mi* memory:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypod
spec:
  containers:
  - name: mypod
    image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
```

For more information about resource measurements and assignments, see [Managing compute resources for containers][k8s-resource-limits].

## Develop and debug applications against an AKS cluster

> **Best practice guidance** 
>
> Development teams should deploy and debug against an AKS cluster using Bridge to Kubernetes.

With Bridge to Kubernetes, you can develop, debug, and test applications directly against an AKS cluster. Developers within a team collaborate to build and test throughout the application lifecycle. You can continue to use existing tools such as Visual Studio or Visual Studio Code with the Bridge to Kubernetes extension. 

Using integrated development and test process with Bridge to Kubernetes reduces the need for local test environments like [minikube][minikube]. Instead, you develop and test against an AKS cluster, even secured and isolated clusters. 

> [!NOTE]
> Bridge to Kubernetes is intended for use with applications that run on Linux pods and nodes.

## Use the Visual Studio Code (VS Code) extension for Kubernetes

> **Best practice guidance** 
>
> Install and use the VS Code extension for Kubernetes when you write YAML manifests. You can also use the extension for integrated deployment solution, which may help application owners that infrequently interact with the AKS cluster.

The [Visual Studio Code extension for Kubernetes][vscode-kubernetes] helps you develop and deploy applications to AKS. The extension provides:
* Intellisense for Kubernetes resources, Helm charts, and templates. 
* Browse, deploy, and edit capabilities for Kubernetes resources from within VS Code. 
* An intellisense check for resource requests or limits being set in the pod specifications:

    ![VS Code extension for Kubernetes warning about missing memory limits](media/developer-best-practices-resource-management/vs-code-kubernetes-extension.png)

## Regularly check for application issues with kube-advisor

> **Best practice guidance** 
> 
> Regularly run the latest version of `kube-advisor` open-source tool to detect issues in your cluster. Run `kube-advisor` before applying resource quotas on an existing AKS cluster to find pods that don't have resource requests and limits defined.

The [kube-advisor][kube-advisor] tool is an associated AKS open-source project that scans a Kubernetes cluster and reports on identified issues. One useful check is to identify pods without resource requests and limits in place.

While the `kube-advisor` tool can report on resource requests and limits missing in PodSpecs for Windows and Linux applications, `kube-advisor` itself must be scheduled on a Linux pod. Use a [node selector][k8s-node-selector] in the pod's configuration to schedule a pod to run on a node pool with a specific OS.

In an AKS cluster that hosts many development teams and applications, you'll find it easier to track pods using resource requests and limits. As a best practice, regularly run `kube-advisor` on your AKS clusters.

## Next steps

This article focused on how to run your cluster and workloads from a cluster operator perspective. For information about administrative best practices, see [Cluster operator best practices for isolation and resource management in Azure Kubernetes Service (AKS)][operator-best-practices-isolation].

To implement some of these best practices, see the following articles:

* [Develop with Bridge to Kubernetes][btk]
* [Check for issues with kube-advisor][aks-kubeadvisor]

<!-- EXTERNAL LINKS -->
[k8s-resource-limits]: https://kubernetes.io/docs/concepts/configuration/manage-compute-resources-container/
[vscode-kubernetes]: https://github.com/Azure/vscode-kubernetes-tools
[kube-advisor]: https://github.com/Azure/kube-advisor
[minikube]: https://kubernetes.io/docs/setup/minikube/

<!-- INTERNAL LINKS -->
[aks-kubeadvisor]: kube-advisor-tool.md
[btk]: /visualstudio/containers/overview-bridge-to-kubernetes
[operator-best-practices-isolation]: operator-best-practices-cluster-isolation.md
[resource-quotas]: operator-best-practices-scheduler.md#enforce-resource-quotas
[k8s-node-selector]: concepts-clusters-workloads.md#node-selectors
