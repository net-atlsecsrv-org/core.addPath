---
title: "Configurations and GitOps - Azure Arc enabled Kubernetes"
services: azure-arc
ms.service: azure-arc
ms.date: 02/17/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: "This article provides a conceptual overview of GitOps and configurations capability of Azure Arc enabled Kubernetes."
keywords: "Kubernetes, Arc, Azure, containers, configuration, GitOps"
---

# Configurations and GitOps with Azure Arc enabled Kubernetes

In relation to Kubernetes, GitOps is the practice of declaring the desired state of Kubernetes cluster configurations (deployments, namespaces, etc.) in a Git repository. This declaration is followed by a polling and pull-based deployment of these cluster configurations using an operator. The Git repository can contain:
* YAML-format manifests describing any valid Kubernetes resources, including Namespaces, ConfigMaps, Deployments, DaemonSets, etc.
* Helm charts for deploying applications.

[Flux](https://docs.fluxcd.io/), a popular open-source tool in the GitOps space, can be deployed on the Kubernetes cluster to ease the flow of configurations from a Git repository to a Kubernetes cluster. Flux supports the deployment of its operator at both the cluster and namespace scopes. A flux operator deployed with namespace scope can only deploy Kubernetes objects within that specific namespace. The ability to choose between cluster or namespace scope helps you achieve multi-tenant deployment patterns on the same Kubernetes cluster.

## Configurations

[ ![Configurations architecture](./media/conceptual-configurations.png) ](./media/conceptual-configurations.png#lightbox)

The connection between your cluster and a Git repository is created as a `Microsoft.KubernetesConfiguration/sourceControlConfigurations` extension resource on top of the Azure Arc enabled Kubernetes resource (represented by `Microsoft.Kubernetes/connectedClusters`) in Azure Resource Manager. 

The `sourceControlConfiguration` resource properties are used to deploy Flux operator on the cluster with the appropriate parameters, such as the Git repository from which to pull manifests and the polling interval at which to pull them. The `sourceControlConfiguration` data is stored encrypted and at rest in an Azure Cosmos DB database to ensure data confidentiality.

The `config-agent` running in your cluster is responsible for:
* Tracking new or updated `sourceControlConfiguration` extension resources on the Azure Arc enabled Kubernetes resource.
* Deploying a Flux operator to watch the Git repository for each `sourceControlConfiguration`.`
* Applying any updates made to any `sourceControlConfiguration`. 

You can create multiple namespace-scoped `sourceControlConfiguration` resources on the same Azure Arc enabled Kubernetes cluster to achieve multi-tenancy.

> [!NOTE]
> * `config-agent` continually monitors for new or updated `sourceControlConfiguration` extension resources available on the Azure Arc enabled Kubernetes resource. Thus, agents require consistent connectivity to pull the desired state properties to the cluster. If agents are unable to connect to Azure, the desired state is not applied on the cluster.
> * Sensitive customer inputs like private key, known hosts content, HTTPS username, and token or password are stored for up to 48 hours in the Azure Arc enabled Kubernetes services. If you are using sensitive inputs for configurations, bring the clusters online as regularly as possible.

## Apply configurations at scale

Since Azure Resource Manager manages your configurations, you can automate creating the same configuration across all Azure Arc enabled Kubernetes resources using Azure Policy, within scope of a subscription or a resource group. 

This at-scale enforcement ensures a common baseline configuration (containing configurations like ClusterRoleBindings, RoleBindings, and NetworkPolicy) can be applied across an entire fleet or inventory of Azure Arc enabled Kubernetes clusters.

## Next steps

* [Connect a cluster to Azure Arc](./connect-cluster.md)
* [Create configurations on your Arc enabled Kubernetes cluster](./use-gitops-connected-cluster.md)
* [Use Azure Policy to apply configurations at scale](./use-azure-policy.md)