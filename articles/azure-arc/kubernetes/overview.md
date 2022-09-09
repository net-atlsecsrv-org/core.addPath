---
title: "Overview of Azure Arc enabled Kubernetes"
services: azure-arc
ms.service: azure-arc
#ms.subservice: azure-arc-kubernetes coming soon
ms.date: 05/19/2020
ms.topic: overview
author: mlearned
ms.author: mlearned
description: "This article provides an overview of Azure Arc enabled Kubernetes."
keywords: "Kubernetes, Arc, Azure, containers"
ms.custom: references_regions
---

# What is Azure Arc enabled Kubernetes Preview?

You can attach and configure Kubernetes clusters inside or outside of Azure by using Azure Arc enabled Kubernetes Preview. When a Kubernetes cluster is attached to Azure Arc, it will appear in the Azure portal. It will have an Azure Resource Manager ID and a managed identity. Clusters are attached to standard Azure subscriptions, are located in a resource group, and can receive tags just like any other Azure resource. 

To connect a Kubernetes cluster to Azure, the cluster administrator needs to deploy agents. These agents run in a Kubernetes namespace named `azure-arc` and are standard Kubernetes deployments. The agents are responsible for connectivity to Azure, collecting Azure Arc logs and metrics, and watching for configuration requests. 

Azure Arc enabled Kubernetes supports industry-standard SSL to secure data in transit. Also, data is stored encrypted at rest in an Azure Cosmos DB database to ensure data confidentiality.
 
> [!NOTE]
> Azure Arc enabled Kubernetes is in preview. We don't recommend it for production workloads.

## Supported Kubernetes distributions

Azure Arc enabled Kubernetes works with any Cloud Native Computing Foundation (CNCF) certified Kubernetes cluster such as AKS-engine on Azure, AKS-engine on Azure Stack Hub, GKE, EKS and VMware vSphere cluster.

Azure Arc enabled Kubernetes features have been tested by the Arc team on following distributions:
* RedHat OpenShift 4.3
* Rancher RKE 1.0.8
* Canonical Charmed Kubernetes 1.18
* AKS Engine
* AKS Engine on Azure Stack Hub
* Cluster API Provider Azure

## Supported scenarios 

Azure Arc enabled Kubernetes supports these scenarios: 

* Connect Kubernetes running outside of Azure for inventory, grouping, and tagging.

* Deploy applications and apply configuration by using GitOps-based configuration management. 

* Use Azure Monitor for containers to view and monitor your clusters. 

* Apply policies by using Azure Policy for Kubernetes. 

[!INCLUDE [azure-lighthouse-supported-service](../../../includes/azure-lighthouse-supported-service.md)]

## Supported regions 

Azure Arc enabled Kubernetes is currently supported in these regions: 

* East US 
* West Europe

## Frequently Asked Questions

* What is the difference between Azure Arc enabled Kubernetes and Azure Kubernetes Service (AKS)?

    Azure Kubernetes Service (AKS) is the managed Kubernetes offering by Azure. AKS makes it simple to deploy a managed Kubernetes cluster in Azure. AKS reduces the complexity and operational overhead of managing Kubernetes by offloading much of that responsibility to Azure. The Kubernetes masters are managed by Azure. You only manage and maintain the agent nodes.

    Azure Arc enabled Kubernetes allows you to connect Kubernetes clusters to Azure for extending Azure's management capabilities like Azure Monitor and Azure Policy. The maintenance of the underlying Kubernetes cluster itself is done by you.

* Do I need to connect my Azure Kubernetes Service clusters running on Azure to Azure Arc?

    No. All features of Azure Arc enabled Kubernetes like Azure Monitor, Azure Policy (Gatekeeper) are natively available with AKS, which already has a resource representation in Azure.
    
* Should I connect my AKS cluster on Azure Stack HCI to Azure Arc? What about Kubernetes clusters running on Azure Stack Hub or Azure Stack Engine?

    Yes, connecting these clusters to Azure Arc does have benefits. It provides a resource representation for these Kubernetes clusters in Azure Resource Manager. Using this resource representation, capabilities like Cluster Configuration, Azure Monitor, Azure Policy (Gatekeeper) can be extended to these Kubernetes clusters

## Next steps

* [Connect a cluster](./connect-cluster.md)
