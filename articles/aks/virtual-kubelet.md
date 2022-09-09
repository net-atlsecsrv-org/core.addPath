---
title: Run Virtual Kubelet in an Azure Kubernetes Service (AKS) cluster
description: Learn how to use Virtual Kubelet with Azure Kubernetes Service (AKS) to run Linux and Windows containers on Azure Container Instances.
services: container-service
author: iainfoulds
manager: jeconnoc

ms.service: container-service
ms.topic: article
ms.date: 08/14/2018
ms.author: iainfou
---

# Use Virtual Kubelet with Azure Kubernetes Service (AKS)

Azure Container Instances (ACI) provide a hosted environment for running containers in Azure. When using ACI, there is no need to manage the underlying compute infrastructure, Azure handles this management for you. When running containers in ACI, you are charged by the second for each running container.

When using the Virtual Kubelet provider for Azure Container Instances, both Linux and Windows containers can be scheduled on a container instance as if it is a standard Kubernetes node. This configuration allows you to take advantage of both the capabilities of Kubernetes and the management value and cost benefit of container instances.

> [!NOTE]
> AKS now has built-in support for scheduling containers on ACI, called *virtual nodes*. These virtual nodes currently support Linux container instances. If you need to schedule Windows container instances, you can continue using Virtual Kubelet. Otherwise, you should use virtual nodes instead of the manual Virtual Kubelet instructions noted in this article. You can get started with virtual nodes using the [Azure CLI][virtual-nodes-cli] or [Azure portal][virtual-nodes-portal].
>
> Virtual Kubelet is an experimental open source project and should be used as such. To contribute, file issues, and read more about virtual kubelet, see the [Virtual Kubelet GitHub project][vk-github].

## Before you begin

This document assumes that you have an AKS cluster. If you need an AKS cluster, see the [Azure Kubernetes Service (AKS) quickstart][aks-quick-start].

You also need the Azure CLI version **2.0.33** or later. Run `az --version` to find the version. If you need to install or upgrade, see [Install Azure CLI](/cli/azure/install-azure-cli).

To install the Virtual Kubelet, [Helm](https://docs.helm.sh/using_helm/#installing-helm) is also required.

### Register Container Instances feature provider

If you have not previously used the Azure Container Instance (ACI) service, register the service provider with your subscription. You can check the status of the ACI provider registration using the [az provider list][az-provider-list] command, as shown in the following example:

```azurecli-interactive
az provider list --query "[?contains(namespace,'Microsoft.ContainerInstance')]" -o table
```

The *Microsoft.ContainerInstance* provider should report as *Registered*, as shown in the following example output:

```
Namespace                    RegistrationState
---------------------------  -------------------
Microsoft.ContainerInstance  Registered
```

If the provider shows as *NotRegistered*, register the provider using the [az provider register][az-provider-register] as shown in the following example:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerInstance
```

### For RBAC-enabled clusters

If your AKS cluster is RBAC-enabled, you must create a service account and role binding for use with Tiller. For more information, see [Helm Role-based access control][helm-rbac]. To create a service account and role binding, create a file named *rbac-virtual-kubelet.yaml* and paste the following definition:

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: tiller
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: tiller
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
  - kind: ServiceAccount
    name: tiller
    namespace: kube-system
```

Apply the service account and binding with [kubectl apply][kubectl-apply] and specify your *rbac-virtual-kubelet.yaml* file, as shown in the following example:

```
$ kubectl apply -f rbac-virtual-kubelet.yaml

clusterrolebinding.rbac.authorization.k8s.io/tiller created
```

Configure Helm to use the tiller service account:

```console
helm init --service-account tiller
```

You can now continue to installing the Virtual Kubelet into your AKS cluster.

## Installation

Use the [az aks install-connector][aks-install-connector] command to install Virtual Kubelet. The following example deploys both the Linux and Windows connector.

```azurecli-interactive
az aks install-connector --resource-group myAKSCluster --name myAKSCluster --connector-name virtual-kubelet --os-type Both
```

These arguments are available for the `aks install-connector` command.

| Argument: | Description | Required |
|---|---|:---:|
| `--connector-name` | Name of the ACI Connector.| Yes |
| `--name` `-n` | Name of the managed cluster. | Yes |
| `--resource-group` `-g` | Name of resource group. | Yes |
| `--os-type` | Container instances operating system type. Allowed values: Both, Linux, Windows. Default: Linux. | No |
| `--aci-resource-group` | The resource group in which to create the ACI container groups. | No |
| `--location` `-l` | The location to create the ACI container groups. | No |
| `--service-principal` | Service principal used for authentication to Azure APIs. | No |
| `--client-secret` | Secret associated with the service principal. | No |
| `--chart-url` | URL of a Helm chart that installs ACI Connector. | No |
| `--image-tag` | The image tag of the virtual kubelet container image. | No |

## Validate Virtual Kubelet

To validate that Virtual Kubelet has been installed, return a list of Kubernetes nodes using the [kubectl get nodes][kubectl-get] command.

```
$ kubectl get nodes

NAME                                    STATUS    ROLES     AGE       VERSION
aks-nodepool1-23443254-0                Ready     agent     16d       v1.9.6
aks-nodepool1-23443254-1                Ready     agent     16d       v1.9.6
aks-nodepool1-23443254-2                Ready     agent     16d       v1.9.6
virtual-kubelet-virtual-kubelet-linux   Ready     agent     4m        v1.8.3
virtual-kubelet-virtual-kubelet-win     Ready     agent     4m        v1.8.3
```

## Run Linux container

Create a file named `virtual-kubelet-linux.yaml` and copy in the following YAML. Take note that a [nodeSelector][node-selector] and [toleration][toleration] are being used to schedule the container on the node.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: aci-helloworld
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aci-helloworld
  template:
    metadata:
      labels:
        app: aci-helloworld
    spec:
      containers:
      - name: aci-helloworld
        image: microsoft/aci-helloworld
        ports:
        - containerPort: 80
      nodeSelector:
        beta.kubernetes.io/os: linux
        kubernetes.io/role: agent
        type: virtual-kubelet
      tolerations:
      - key: virtual-kubelet.io/provider
        operator: Equal
        value: azure
        effect: NoSchedule
```

Run the application with the [kubectl create][kubectl-create] command.

```console
kubectl create -f virtual-kubelet-linux.yaml
```

Use the [kubectl get pods][kubectl-get] command with the `-o wide` argument to output a list of pods with the scheduled node. Notice that the `aci-helloworld` pod has been scheduled on the `virtual-kubelet-virtual-kubelet-linux` node.

```
$ kubectl get pods -o wide

NAME                                READY     STATUS    RESTARTS   AGE       IP             NODE
aci-helloworld-2559879000-8vmjw     1/1       Running   0          39s       52.179.3.180   virtual-kubelet-virtual-kubelet-linux
```

## Run Windows container

Create a file named `virtual-kubelet-windows.yaml` and copy in the following YAML. Take note that a [nodeSelector][node-selector] and [toleration][toleration] are being used to schedule the container on the node.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nanoserver-iis
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nanoserver-iis
  template:
    metadata:
      labels:
        app: nanoserver-iis
    spec:
      containers:
      - name: nanoserver-iis
        image: microsoft/iis:nanoserver
        ports:
        - containerPort: 80
      nodeSelector:
        beta.kubernetes.io/os: windows
        kubernetes.io/role: agent
        type: virtual-kubelet
      tolerations:
      - key: virtual-kubelet.io/provider
        operator: Equal
        value: azure
        effect: NoSchedule
```

Run the application with the [kubectl create][kubectl-create] command.

```console
kubectl create -f virtual-kubelet-windows.yaml
```

Use the [kubectl get pods][kubectl-get] command with the `-o wide` argument to output a list of pods with the scheduled node. Notice that the `nanoserver-iis` pod has been scheduled on the `virtual-kubelet-virtual-kubelet-win` node.

```
$ kubectl get pods -o wide

NAME                                READY     STATUS    RESTARTS   AGE       IP             NODE
nanoserver-iis-868bc8d489-tq4st     1/1       Running   8         21m       138.91.121.91   virtual-kubelet-virtual-kubelet-win
```

## Remove Virtual Kubelet

Use the [az aks remove-connector][aks-remove-connector] command to remove Virtual Kubelet. Replace the argument values with the name of the connector, AKS cluster, and the AKS cluster resource group.

```azurecli-interactive
az aks remove-connector --resource-group myAKSCluster --name myAKSCluster --connector-name virtual-kubelet
```

> [!NOTE]
> If you encounter errors removing both OS connectors, or want to remove just the Windows or Linux OS connector, you can manually specify the OS type. Add the `--os-type` parameter to the previous `az aks remove-connector` command, and specify `Windows` or `Linux`.

## Next steps

For possible issues with the Virtual Kubelet, see the [Known quirks and workarounds][vk-troubleshooting]. To report problems with the Virtual Kubelet, [open a GitHub issue][vk-issues].

Read more about Virtual Kubelet at the [Virtual Kubelet GitHub project][vk-github].

<!-- LINKS - internal -->
[aks-quick-start]: ./kubernetes-walkthrough.md
[aks-remove-connector]: /cli/azure/aks#az-aks-remove-connector
[az-container-list]: /cli/azure/aks#az-aks-list
[aks-install-connector]: /cli/azure/aks#az-aks-install-connector
[virtual-nodes-cli]: virtual-nodes-cli.md
[virtual-nodes-portal]: virtual-nodes-portal.md

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[node-selector]:https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[toleration]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/
[vk-github]: https://github.com/virtual-kubelet/virtual-kubelet
[helm-rbac]: https://docs.helm.sh/using_helm/#role-based-access-control
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[vk-troubleshooting]: https://github.com/virtual-kubelet/virtual-kubelet#known-quirks-and-workarounds
[vk-issues]: https://github.com/virtual-kubelet/virtual-kubelet/issues
