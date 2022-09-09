---
title: Supported Kubernetes versions in Azure Kubernetes Service
description: Understand the Kubernetes version support policy and lifecycle of clusters in Azure Kubernetes Service (AKS)
services: container-service
author: sauryadas

ms.service: container-service
ms.topic: article
ms.date: 05/20/2019
ms.author: saudas
---

# Supported Kubernetes versions in Azure Kubernetes Service (AKS)

The Kubernetes community releases minor versions roughly every three months. These releases include new features and improvements. Patch releases are more frequent (sometimes weekly) and are only intended for critical bug fixes in a minor version. These patch releases include fixes for security vulnerabilities or major bugs impacting a large number of customers and products running in production based on Kubernetes.

A new Kubernetes minor version is made available in [aks-engine][aks-engine] on day one. The AKS Service Level Objective (SLO) targets releasing the minor version for AKS clusters within 30 days, subject to the stability of the release.

## Kubernetes version support policy

AKS supports four minor versions of Kubernetes:

- The current minor version that is released upstream (n)
- Three previous minor versions. Each supported minor version also supports two stable patches.

For example, if AKS introduces *1.13.x* today, support is also provided for *1.12.a* + *1.12.b*, *1.11.c* + *1.11d*, *1.10.e* + *1.10f* (where the lettered patch releases are two latest stable builds).

When a new minor version is introduced, the oldest minor version and patch releases supported are retired. 30 days before the release of the new minor version and upcoming version retirement, an announcement is made through the [Azure update channels][azure-update-channel]. In the example above where *1.13.x* is released, the retired versions are *1.9.g* + *1.9.h*.

When you deploy an AKS cluster in the portal or with the Azure CLI, the cluster is always set to the n-1 minor version and latest patch. For example, if AKS supports *1.13.x*, *1.12.a* + *1.12.b*, *1.11.c* + *1.11d*, *1.10.e* + *1.10f*, the default version for new clusters is *1.11.b*.

## List currently supported versions

To find out what versions are currently available for your subscription and region, use the [az aks get-versions][az-aks-get-versions] command. The following example lists the available Kubernetes versions for the *EastUS* region:

```azurecli-interactive
az aks get-versions --location eastus --output table
```

The output is similar to the following example, which shows that Kubernetes version *1.13.5* is the most recent version available:

```
KubernetesVersion    Upgrades
-------------------  ------------------------
1.13.5               None available
1.12.7               1.13.5
1.12.6               1.12.7, 1.13.5
1.11.9               1.12.6, 1.12.7
1.11.8               1.11.9, 1.12.6, 1.12.7
1.10.13              1.11.8, 1.11.9
1.10.12              1.10.13, 1.11.8, 1.11.9
```

## FAQ

**What happens when a customer upgrades a Kubernetes cluster with a minor version that is not supported?**

If you are on the *n-4* version, you are out of the SLO. If your upgrade from version n-4 to n-3 succeeds, then you are back in the SLO. For example:

- If the supported AKS versions are *1.13.x*, *1.12.a* + *1.12.b*, *1.11.c* + *1.11d*, and *1.10.e* + *1.10f* and you are on *1.9.g* or *1.9.h*, you are out of the SLO.
- If the upgrade from *1.9.g* or *1.9.h* to *1.10.e* or *1.10.f* succeeds, you are back in the SLO.

Upgrades to versions older than *n-4* are not supported. In such cases, we recommend customers create new AKS clusters and redeploy their workloads.

**What happens when a customer scales a Kubernetes cluster with a minor version that is not supported?**

For minor versions not supported by AKS, scaling in or out continues to work without any issues.

**Can a customer stay on a Kubernetes version forever?**

Yes. However, if the cluster is not on one of the versions supported by AKS, the cluster is out of the AKS SLO. Azure does not automatically upgrade your cluster or delete it.

**What version does the master support if the agent cluster is not in one of the supported AKS versions?**

The master is automatically updated to the latest supported version.

## Next steps

For information on how to upgrade your cluster, see [Upgrade an Azure Kubernetes Service (AKS) cluster][aks-upgrade].

<!-- LINKS - External -->
[aks-engine]: https://github.com/Azure/aks-engine
[azure-update-channel]: https://azure.microsoft.com/updates/?product=kubernetes-service

<!-- LINKS - Internal -->
[aks-upgrade]: upgrade-cluster.md
[az-aks-get-versions]: /cli/azure/aks#az-aks-get-versions
