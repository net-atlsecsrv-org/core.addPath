---
title: Run a GPU module on Microsoft Azure Stack Edge GPU device| Microsoft Docs
description: Describes how to configure and run a module on GPU on an Azure Stack Edge device via the Azure portal.
services: databox
author: alkohli

ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/25/2020
ms.author: alkohli
---
# Configure and run a module on GPU on Azure Stack Edge device

Your Azure Stack Edge device contains one or more Graphics Processing Unit (GPU). GPUs are a popular choice for AI computations as they offer parallel processing capabilities and are faster at image rendering than Central Processing Units (CPUs). For more information on the GPU contained in your Azure Stack Edge device, go to [Azure Stack Edge device technical specifications](azure-stack-edge-gpu-technical-specifications-compliance.md).

This article describes how to configure and run a module on the GPU on your Azure Stack Edge device. In this article, you will use a publicly available container module **Digits** written for Nvidia T4 GPUs. This procedure can be used to configure any other modules published by Nvidia for these GPUs.


## Prerequisites

Before you begin, make sure that:

1. You've access to a GPU enabled 1-node Azure Stack Edge device. This device is activated with a resource in Azure.  

## Configure module to use GPU

To configure a module to use the GPU on your Azure Stack Edge device to run a module, follow these steps.

1. In the Azure portal, go to the resource associated with your device. 

2. Go to **Edge compute > Get started**. In the **Configure Edge compute** tile, select Configure.

    ![Configure module to use GPU 1](media/azure-stack-edge-j-series-configure-gpu-modules/configure-compute-1.png)

3. In the **Configure Edge compute** blade:

    1. For **IoT Hub**, choose **Create new**.
    2. Provide a name for the IoT Hub resource that you want to create for your device. TO use a free tier, select an existing resource. 
    3. Make a note of the IoT Edge device and the IoT Gateway device that are created with the IoT Hub resource. You will use this information in the later steps.

    ![Configure module to use GPU 2](media/azure-stack-edge-j-series-configure-gpu-modules/configure-compute-2.png)

4. It takes several minutes to create the IoT Hub resource. After the resource is created, in the **Configure Edge compute** tile, select **View config** to view the details of the IoT Hub resource.

    ![Configure module to use GPU 4](media/azure-stack-edge-j-series-configure-gpu-modules/configure-compute-4.png)

5. Go to **Automatic device management > IoT Edge**.

    ![Configure module to use GPU 6](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-2.png)

    In the right pane, you see the IoT Edge device associated with your Azure Stack Edge device. This corresponds to the IoT Edge device you created in the previous step when creating the IoT Hub resource. 
    
6. Select this IoT Edge device.

   ![Configure module to use GPU 7](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-3.png)

7.  Select **Set modules**.

    ![Configure module to use GPU 8](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-4.png)

8. Select **+ Add** and then select **+ IoT Edge module**. 

    ![Configure module to use GPU 9](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-5.png)

9. In the **Add IoT Edge Module** tab:

    1. Provide the **Image URI**. You will use the publicly available Nvidia module **Digits** here. 
    
    2. Set **Restart policy** to **always**.
    
    3. Set **Desired state** to **running**.
    
    ![Configure module to use GPU 10](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-6.png)

10. In the **Environment variables** tab, provide the Name of the variable and the corresponding value. 

    1. To have the current module use one GPU on this device, use the NVIDIA_VISIBLE_DEVICES. 

    2. Set the value to 0 or 1. This ensures that atleast one GPU is used by the device for this module. When you set the value to 0, 1, that implies that both the GPUs on your device are being used by this module.

        ![Configure module to use GPU 11](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-7.png)

        For more information on environment variables that you can use with the Nvidia GPU, go to [nVidia container runtime](https://github.com/NVIDIA/nvidia-container-runtime#environment-variables-oci-spec).

    > [!NOTE]
    > A GPU can only be mapped to one module. A module can however use one, both or no GPUs. 

11. Enter a name for your module. At this point you can choose to provide container create option and modify module twin settings or if done, select **Add**. 

    ![Configure module to use GPU 12](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-8.png)

12. Make sure that the module is running and select **Review + Create**.    

    ![Configure module to use GPU 13](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-9.png)

13. In the **Review + Create** tab, the deployment options that you selected are displayed. Review the options and select **Create**.
    
    ![Configure module to use GPU 14](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-10.png)

14. Make a note of the **runtime status** of the module. 
    
    ![Configure module to use GPU 15](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-11.png)

    It takes a couple minutes for the module to be deployed. Select **Refresh** and you should see the **runtime status** update to **running**.

    ![Configure module to use GPU 16](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-12.png)


## Next steps

- Learn more about [Environment variables that you can use with the Nvidia GPU](https://github.com/NVIDIA/nvidia-container-runtime#environment-variables-oci-spec).
