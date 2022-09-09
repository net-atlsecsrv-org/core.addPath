---
title: "include file"
description: "include file"
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: "include file"
ms.topic: "include"
ms.date: 11/02/2020
---


The compute target you use to host your model will affect the cost and availability of your deployed endpoint. Use this table to choose an appropriate compute target.

| Compute target | Used for | GPU support | FPGA support | Description |
| ----- | ----- | ----- | ----- | ----- |
| [Local&nbsp;web&nbsp;service](../articles/machine-learning/how-to-deploy-local-container-notebook-vm.md) | Testing/debugging | &nbsp; | &nbsp; | Use for limited testing and troubleshooting. Hardware acceleration depends on use of libraries in the local system.
| [Azure Kubernetes Service (AKS)](../articles/machine-learning/how-to-deploy-azure-kubernetes-service.md) | Real-time inference |  [Yes](../articles/machine-learning/how-to-deploy-inferencing-gpus.md) (web service deployment) | [Yes](../articles/machine-learning/how-to-deploy-fpga-web-service.md)   |Use for high-scale production deployments. Provides fast response time and autoscaling of the deployed service. Cluster autoscaling isn't supported through the Azure Machine Learning SDK. To change the nodes in the AKS cluster, use the UI for your AKS cluster in the Azure portal. AKS is the only option available for the designer. |
| [Azure Container Instances](../articles/machine-learning/how-to-deploy-azure-container-instance.md) | Testing or development | &nbsp;  | &nbsp; | Use for low-scale CPU-based workloads that require less than 48 GB of RAM. |
| [Azure Machine Learning compute clusters](../articles/machine-learning/how-to-use-parallel-run-step.md) | Batch&nbsp;inference | [Yes](../articles/machine-learning/how-to-use-parallel-run-step.md) (machine learning pipeline) | &nbsp;  | Run batch scoring on serverless compute. Supports normal and low-priority VMs. |

> [!NOTE]
> Although compute targets like local, Azure Machine Learning compute, and Azure Machine Learning compute clusters support GPU for training and experimentation, using GPU for inference _when deployed as a web service_ is supported only on AKS.
>
> Using a GPU for inference _when scoring with a machine learning pipeline_ is supported only on Azure Machine Learning compute.

> [!NOTE]
> * Container instances are suitable only for small models less than 1 GB in size.
> * Use single-node AKS clusters for dev/test of larger models.