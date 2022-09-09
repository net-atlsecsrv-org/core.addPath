---
title: What are compute targets
titleSuffix: Azure Machine Learning
description: Define where you want to train or deploy your model with Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 09/29/2020
# As a data scientist, I want to understand what a compute target is and why I need it.
---

#  What are compute targets in Azure Machine Learning? 

A **compute target** is a designated compute resource/environment where you run your training script or host your service deployment. This location may be your local machine or a cloud-based compute resource. Using compute targets make it easy for you to later change your compute environment without having to change your code.  

In a typical model development lifecycle, you might:
1. Start by developing and experimenting on a small amount of data. At this stage, we recommend your local environment (local computer or cloud-based VM) as your compute target. 
2. Scale up to larger data, or do distributed training using one of these [training compute targets](#train).  
3. Once your model is ready, deploy it to a web hosting environment or IoT device with one of these [deployment compute targets](#deploy).

The compute resources you use for your compute targets are attached to a [workspace](concept-workspace.md). Compute resources other than the local machine are shared by users of the workspace.

## <a name="train"></a> Training compute targets
Azure Machine Learning has varying support across different compute targets. A typical model development lifecycle starts with dev/experimentation on a small amount of data. At this stage, we recommend using a local environment. For example, your local computer or a cloud-based VM. As you scale up your training on larger data sets, or perform distributed training, we recommend using Azure Machine Learning Compute to create a single- or multi-node cluster that autoscales each time you submit a run. You can also attach your own compute resource, although support for various scenarios may vary as detailed below:

[!INCLUDE [aml-compute-target-train](../../includes/aml-compute-target-train.md)]

Learn more about how to [submit a training run to a compute target](how-to-set-up-training-targets.md).

## <a name="deploy"></a> Compute targets for inference

The following compute resources can be used to host your model deployment.

[!INCLUDE [aml-compute-target-deploy](../../includes/aml-compute-target-deploy.md)]

When performing inference, Azure Machine Learning creates a Docker container that hosts the model and associated resources needed to use it. This container is then used in one of the following deployment scenarios:

* As a __web service__ that is used for real-time inference. Web service deployments use one of the following compute targets:

    * [Local computer](how-to-attach-compute-targets.md#local)
    * [Azure Machine Learning compute instance](how-to-create-manage-compute-instance.md)
    * [Azure Container Instances](how-to-attach-compute-targets.md#aci)
    * [Azure Kubernetes Services](how-to-create-attach-kubernetes.md)
    * Azure Functions (preview). Deployment to Azure Functions only relies on Azure Machine Learning to build the Docker container. From there, it is deployed using Azure Functions. For more information, see [Deploy a machine learning model to Azure Functions (preview)](how-to-deploy-functions.md).

* As a __batch inference__ endpoint that is used to periodically process batches of data. Batch inferences uses [Azure Machine Learning compute cluster](how-to-create-attach-compute-cluster.md).

* To an __IoT device__ (preview). Deployment to an IoT device only relies on Azure Machine Learning to build the Docker container. From there, it is deployed using Azure IoT Edge. For more information, see [Deploy as an IoT Edge module (preview)](/azure/iot-edge/tutorial-deploy-machine-learning).

Learn [where and how to deploy your model to a compute target](how-to-deploy-and-where.md).

<a name="amlcompute"></a>
## Azure Machine Learning compute (managed)

A managed compute resource is created and managed by Azure Machine Learning. This compute is optimized for machine learning workloads. Azure Machine Learning compute clusters and [compute instances](concept-compute-instance.md) are the only managed computes. 

You can create Azure Machine Learning compute instances or compute clusters from:
* [Azure Machine Learning studio](how-to-create-attach-compute-studio.md)
* Python SDK and CLI:
    * [Compute instance](how-to-create-manage-compute-instance.md)
    * [Compute cluster](how-to-create-attach-compute-cluster.md)
* [R SDK](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-compute-targets) (preview)
* Resource Manager template. For an example template, see the [create Azure Machine Learning compute template](https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-compute-create-amlcompute).
* Machine learning [extension for the Azure CLI](reference-azure-machine-learning-cli.md#resource-management).  

When created these compute resources are automatically part of your workspace, unlike other kinds of compute targets.


|Capability  |Compute cluster  |Compute instance  |
|---------|---------|---------|
|Single- or multi-node cluster     |    **&check;**       |         |
|Autoscales each time you submit a run     |     **&check;**      |         |
|Automatic cluster management and job scheduling     |   **&check;**        |     **&check;**      |
|Support for both CPU and GPU resources     |  **&check;**         |    **&check;**       |


> [!NOTE]
> When a compute cluster is idle, it autoscales to 0 nodes, so you don't pay when it's not in use.  A compute *instance*, however, is always on and does not autoscale.  You should [stop the compute instance](how-to-create-manage-compute-instance.md#manage) when you are not using it to avoid extra cost. 

### Supported VM series and sizes

When you select a node size for a managed compute resource in Azure Machine Learning, you can choose from among select VM sizes available in Azure. Azure offers a range of sizes for Linux and Windows for different workloads. Refer here to learn more about the different [VM types and sizes](https://docs.microsoft.com/azure/virtual-machines/linux/sizes).

There are a few exceptions and limitations to choosing a VM size:
* Some VM series are not supported in Azure Machine Learning.
* Some VM series are restricted. To use a restricted series, contact support and request a quota increase for the series. For information on contacting support, see [Azure support options](https://azure.microsoft.com/support/options/)

See the following table to learn more about supported series and restrictions. 

| **Supported VM series**  | **Restrictions** |
|------------|------------|
| D | None |
| Dv2 | None |  
| Dv3 | None|
| DSv2 | None | 
| DSv3 | None|
| FSv2 | None | 
| HBv2 | Requires approval |  
| HCS | Requires approval |  
| M | Requires approval |
| NC | None |    
| NCsv2 | Requires approval |
| NCsv3 | Requires approval |  
| NDs | Requires approval |
| NDv2 | Requires approval |
| NV | None |
| NVv3 | Requires approval | 


While Azure Machine Learning supports these VM series, they may not be available in all Azure regions. You can check with VM series are available here: [Products Available by Region](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines).

### Compute Isolation

Azure Machine Learning Compute offers virtual machine sizes that are Isolated to a specific hardware type and dedicated to a single customer. Isolated virtual machine sizes are best suited for workloads that require a high degree of isolation from other customers’ workloads for reasons that include meeting compliance and regulatory requirements. Utilizing an isolated size guarantees that your virtual machine will be the only one running on that specific server instance.

The current Isolated virtual machine offerings include:
* Standard_M128ms
* Standard_F72s_v2
* Standard_NC24s_v3
* Standard_NC24rs_v3*

*RDMA capable

Refer here to learn more about [Isolation in the Azure Public Cloud](https://docs.microsoft.com/azure/security/fundamentals/isolation-choices).

## Unmanaged compute

An unmanaged compute target is *not* managed by Azure Machine Learning. You create this type of compute target outside Azure Machine Learning, then attach it to your workspace. Unmanaged compute resources can require additional steps for you to maintain or to improve performance for machine learning workloads.

## Next steps

Learn how to:
* [Use a compute target to train your model](how-to-set-up-training-targets.md)
* [Deploy your model to a compute target](how-to-deploy-and-where.md)
