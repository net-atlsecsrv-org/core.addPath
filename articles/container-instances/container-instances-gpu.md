---
title: Deploy GPU-enabled Azure container instances 
description: Learn how to deploy Azure container instances to run compute-intensive container apps using GPU resources.
services: container-instances
author: dlepow
manager: gwallace

ms.service: container-instances
ms.topic: article
ms.date: 04/17/2019
ms.author: danlep
---

# Deploy container instances that use GPU resources

To run certain compute-intensive workloads on Azure Container Instances, deploy your [container groups](container-instances-container-groups.md) with *GPU resources*. The container instances in the group can access one or more NVIDIA Tesla GPUs while running container workloads such as CUDA and deep learning applications.

This article shows how to add GPU resources when you deploy a container group by using a [YAML file](container-instances-multi-container-yaml.md) or [Resource Manager template](container-instances-multi-container-group.md). You can also specify GPU resources when you deploy a container instance using the Azure portal.

> [!IMPORTANT]
> This feature is currently in preview, and some [limitations apply](#preview-limitations). Previews are made available to you on the condition that you agree to the [supplemental terms of use][terms-of-use]. Some aspects of this feature may change prior to general availability (GA).

## Preview limitations

In preview, the following limitations apply when using GPU resources in container groups. 

[!INCLUDE [container-instances-gpu-regions](../../includes/container-instances-gpu-regions.md)]

Support will be added for additional regions over time.

**Supported OS types**: Linux only

**Additional limitations**: GPU resources can't be used when deploying a container group into a [virtual network](container-instances-vnet.md).

## About GPU resources

### Count and SKU

To use GPUs in a container instance, specify a *GPU resource* with the following information:

* **Count** - The number of GPUs: **1**, **2**, or **4**.
* **SKU** - The GPU SKU: **K80**, **P100**, or **V100**. Each SKU maps to the NVIDIA Tesla GPU in one the following Azure GPU-enabled VM families:

  | SKU | VM family |
  | --- | --- |
  | K80 | [NC](../virtual-machines/linux/sizes-gpu.md#nc-series) |
  | P100 | [NCv2](../virtual-machines/linux/sizes-gpu.md#ncv2-series) |
  | V100 | [NCv3](../virtual-machines/linux/sizes-gpu.md#ncv3-series) |

[!INCLUDE [container-instances-gpu-limits](../../includes/container-instances-gpu-limits.md)]

When deploying GPU resources, set CPU and memory resources appropriate for the workload, up to the maximum values shown in the preceding table. These values are currently larger than the CPU and memory resources available in container groups without GPU resources.  

### Things to know

* **Deployment time** - Creation of a container group containing GPU resources takes up to **8-10 minutes**. This is due to the additional time to provision and configure a GPU VM in Azure. 

* **Pricing** - Similar to container groups without GPU resources, Azure bills for resources consumed over the *duration* of a container group with GPU resources. The duration is calculated from the time to pull your first container's image until the container group terminates. It does not include the time to deploy the container group.

  See [pricing details](https://azure.microsoft.com/pricing/details/container-instances/).

* **CUDA drivers** - Container instances with GPU resources are pre-provisioned with NVIDIA CUDA drivers and container runtimes, so you can use container images developed for CUDA workloads.

  We support CUDA 9.0 at this stage. For example, you can use following base images for your Docker file:
  * [nvidia/cuda:9.0-base-ubuntu16.04](https://hub.docker.com/r/nvidia/cuda/)
  * [tensorflow/tensorflow: 1.12.0-gpu-py3](https://hub.docker.com/r/tensorflow/tensorflow)
    
## YAML example

One way to add GPU resources is to deploy a container group by using a [YAML file](container-instances-multi-container-yaml.md). Copy the following YAML into a new file named *gpu-deploy-aci.yaml*, then save the file. This YAML creates a container group named *gpucontainergroup* specifying a container instance with a K80 GPU. The instance runs a sample CUDA vector addition application. The resource requests are sufficient to run the workload.

```YAML
additional_properties: {}
apiVersion: '2018-10-01'
name: gpucontainergroup
properties:
  containers:
  - name: gpucontainer
    properties:
      image: k8s-gcrio.azureedge.net/cuda-vector-add:v0.1
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
          gpu:
            count: 1
            sku: K80
  osType: Linux
  restartPolicy: OnFailure
```

Deploy the container group with the [az container create][az-container-create] command, specifying the YAML file name for the `--file` parameter. You need to supply the name of a resource group and a location for the container group such as *eastus* that supports GPU resources.  

```azurecli
az container create --resource-group myResourceGroup --file gpu-deploy-aci.yaml --location eastus
```

The deployment takes several minutes to complete. Then, the container starts and runs a CUDA vector addition operation. Run the [az container logs][az-container-logs] command to view the log output:

```azurecli
az container logs --resource-group myResourceGroup --name gpucontainergroup --container-name gpucontainer
```

Output:

```Console
[Vector addition of 50000 elements]
Copy input data from the host memory to the CUDA device
CUDA kernel launch with 196 blocks of 256 threads
Copy output data from the CUDA device to the host memory
Test PASSED
Done
```

## Resource Manager template example

Another way to deploy a container group with GPU resources is by using a [Resource Manager template](container-instances-multi-container-group.md). Start by creating a file named `gpudeploy.json`, then copy the following JSON into it. This example deploys a container instance with a V100 GPU that runs a [TensorFlow](https://www.tensorflow.org/) training job against the MNIST dataset. The resource requests are sufficient to run the workload.

```JSON
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "containerGroupName": {
        "type": "string",
        "defaultValue": "gpucontainergrouprm",
        "metadata": {
          "description": "Container Group name."
        }
      }
    },
    "variables": {
      "containername": "gpucontainer",
      "containerimage": "microsoft/samples-tf-mnist-demo:gpu"
    },
    "resources": [
      {
        "name": "[parameters('containerGroupName')]",
        "type": "Microsoft.ContainerInstance/containerGroups",
        "apiVersion": "2018-10-01",
        "location": "[resourceGroup().location]",
        "properties": {
            "containers": [
            {
              "name": "[variables('containername')]",
              "properties": {
                "image": "[variables('containerimage')]",
                "resources": {
                  "requests": {
                    "cpu": 4.0,
                    "memoryInGb": 12.0,
                    "gpu": {
                        "count": 1,
                        "sku": "V100"
                  }
                }
              }
            }
          }
        ],
        "osType": "Linux",
        "restartPolicy": "OnFailure"
        }
      }
    ]
}
```

Deploy the template with the [az group deployment create][az-group-deployment-create] command. You need to supply the name of a resource group that was created in a region such as *eastus* that supports GPU resources.

```azurecli-interactive
az group deployment create --resource-group myResourceGroup --template-file gpudeploy.json
```

The deployment takes several minutes to complete. Then, the container starts and runs the TensorFlow job. Run the [az container logs][az-container-logs] command to view the log output:

```azurecli
az container logs --resource-group myResourceGroup --name gpucontainergrouprm --container-name gpucontainer
```

Output:

```Console
2018-10-25 18:31:10.155010: I tensorflow/core/platform/cpu_feature_guard.cc:137] Your CPU supports instructions that this TensorFlow binary was not compiled to use: SSE4.1 SSE4.2 AVX AVX2 FMA
2018-10-25 18:31:10.305937: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1030] Found device 0 with properties:
name: Tesla K80 major: 3 minor: 7 memoryClockRate(GHz): 0.8235
pciBusID: ccb6:00:00.0
totalMemory: 11.92GiB freeMemory: 11.85GiB
2018-10-25 18:31:10.305981: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1120] Creating TensorFlow device (/device:GPU:0) -> (device: 0, name: Tesla K80, pci bus id: ccb6:00:00.0, compute capability: 3.7)
2018-10-25 18:31:14.941723: I tensorflow/stream_executor/dso_loader.cc:139] successfully opened CUDA library libcupti.so.8.0 locally
Successfully downloaded train-images-idx3-ubyte.gz 9912422 bytes.
Extracting /tmp/tensorflow/input_data/train-images-idx3-ubyte.gz
Successfully downloaded train-labels-idx1-ubyte.gz 28881 bytes.
Extracting /tmp/tensorflow/input_data/train-labels-idx1-ubyte.gz
Successfully downloaded t10k-images-idx3-ubyte.gz 1648877 bytes.
Extracting /tmp/tensorflow/input_data/t10k-images-idx3-ubyte.gz
Successfully downloaded t10k-labels-idx1-ubyte.gz 4542 bytes.
Extracting /tmp/tensorflow/input_data/t10k-labels-idx1-ubyte.gz
Accuracy at step 0: 0.097
Accuracy at step 10: 0.6993
Accuracy at step 20: 0.8208
Accuracy at step 30: 0.8594
...
Accuracy at step 990: 0.969
Adding run metadata for 999
```

## Clean up resources

Because using GPU resources may be expensive, ensure that your containers don't run unexpectedly for long periods. Monitor your containers in the Azure portal, or check the status of a container group with the [az container show][az-container-show] command. For example:

```azurecli
az container show --resource-group myResourceGroup --name gpucontainergroup --output table
```

When you're done working with the container instances you created, delete them with the following commands:

```azurecli
az container delete --resource-group myResourceGroup --name gpucontainergroup -y
az container delete --resource-group myResourceGroup --name gpucontainergrouprm -y
```

## Next steps

* Learn more about deploying a container group using a [YAML file](container-instances-multi-container-yaml.md) or [Resource Manager template](container-instances-multi-container-group.md).
* Learn more about [GPU optimized VM sizes](../virtual-machines/linux/sizes-gpu.md) in Azure.


<!-- IMAGES -->
[aci-vnet-01]: ./media/container-instances-vnet/aci-vnet-01.png

<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
