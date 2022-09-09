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
ms.date: 08/23/2019
---

| Compute target | Used for | GPU support | FPGA support | Description |
| ----- | ----- | ----- | ----- | ----- |
| [Local&nbsp;web&nbsp;service](../articles/machine-learning/service/how-to-deploy-and-where.md#local) | Testing/debugging | &nbsp; | &nbsp; | Use for limited testing and troubleshooting. Hardware acceleration depends on use of libraries in the local system.
| [Notebook VM&nbsp;web&nbsp;service](../articles/machine-learning/service/how-to-deploy-and-where.md#notebookvm) | Testing/debugging | &nbsp; | &nbsp; | Use for limited testing and troubleshooting.
| [Azure Kubernetes Service (AKS)](../articles/machine-learning/service/how-to-deploy-and-where.md#aks) | Real-time inference |  [Yes](../articles/machine-learning/service/how-to-deploy-inferencing-gpus.md) (web service deployment) | [Yes](../articles/machine-learning/service/how-to-deploy-fpga-web-service.md)   |Use for high-scale production deployments. Provides fast response time and autoscaling of the deployed service. Cluster autoscaling isn't supported through the Azure Machine Learning SDK. To change the nodes in the AKS cluster, use the UI for your AKS cluster in the Azure portal. AKS is the only option available for the visual interface. |
| [Azure Container Instances](../articles/machine-learning/service/how-to-deploy-and-where.md#aci) | Testing or development | &nbsp;  | &nbsp; | Use for low-scale CPU-based workloads that require less than 48 GB of RAM. |
| [Azure Machine Learning Compute](../articles/machine-learning/service/how-to-run-batch-predictions.md) | (Preview) Batch&nbsp;inference | [Yes](../articles/machine-learning/service/how-to-run-batch-predictions.md) (machine learning pipeline) | &nbsp;  | Run batch scoring on serverless compute. Supports normal and low-priority VMs. |
| [Azure IoT Edge](../articles/machine-learning/service/how-to-deploy-and-where.md#iotedge) | (Preview) IoT&nbsp;module |  &nbsp; | &nbsp; | Deploy and serve ML models on IoT devices. |
| [Azure Data Box Edge](../articles/databox-online/data-box-edge-overview.md)   | Via IoT Edge |  &nbsp; | Yes | Deploy and serve ML models on IoT devices. |

> [!NOTE]
> Although compute targets like local, Notebook VM, and Azure Machine Learning Compute support GPU for training and experimentation, using GPU for inference __when deployed as a web service__ is supported only on Azure Kubernetes Service.
>
> Using a GPU for inference __when scoring with a machine learning pipeline__ is supported only on Azure Machine Learning Compute.