---
title: Tutorial to connect to, configure, activate Azure Stack Edge device with GPU in Azure portal | Microsoft Docs
description: Tutorial to deploy Azure Stack Edge GPU instructs you to connect, set up, and activate your physical device.
services: databox
author: alkohli

ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 08/29/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Azure Stack Edge so I can use it to transfer data to Azure. 
---
# Tutorial: Configure device settings for Azure Stack Edge with GPU

This tutorial describes how you configure device related settings for your Azure Stack Edge device with an onboard GPU. You can set up your device name, update server, and time server via the local web UI.

The device settings can take around 5-7 minutes to complete.

In this tutorial, you learn about:

> [!div class="checklist"]
>
> * Prerequisites
> * Configure device settings
> * Configure update 
> * Configure time

## Prerequisites

Before you configure device related settings on your Azure Stack Edge device with GPU, make sure that:

* For your physical device:

    - You've installed the physical device as detailed in [Install Azure Stack Edge](azure-stack-edge-gpu-deploy-install.md).
    - You've configured network and enabled and configured compute network on your device as detailed in [Tutorial: Configure network for Azure Stack Edge with GPU](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md).


## Configure device settings

Follow these steps to configure device related settings.
 
1. On the **Device setup** tile, for **Device**, select **Configure**.

    ![Local web UI "Device" page](./media/azure-stack-edge-gpu-deploy-set-up-device-update-time/device-1.png)

    On the **Device** page, take the following steps:

    1. Enter a friendly name for your device. The friendly name must contain from 1 to 13 characters and can have letter, numbers, and hyphens.

    2. Provide a **DNS domain** for your device. This domain is used to set up the device as a file server.

    3. To validate and apply the configured device settings, select **Apply**.

        ![Local web UI "Device" page](./media/azure-stack-edge-gpu-deploy-set-up-device-update-time/device-2.png)

        If you have changed the device name and the DNS domain, the automatically generated self-signed certificates on the device will not work. You need to choose one of the following options when you configure certificates.: 
        
        - Generate and download the device certificates. 
        - Bring your own certificates for the device including the signing chain.
    

        ![Local web UI "Device" page](./media/azure-stack-edge-gpu-deploy-set-up-device-update-time/device-3.png)

    4. When the device name and the DNS domain are changed, the SMB and NFS endpoints are created.  

    5. After the settings are applied, go back to **Get started**.

## Configure update

1. On the **Device setup** tile, for **Update**, select **Configure**. You can now configure the location from where to download the updates for your device.  

    ![Local web UI "Update Server" page](./media/azure-stack-edge-gpu-deploy-set-up-device-update-time/update-1.png)

    - You can get the updates directly from the **Microsoft Update server**.

        ![Local web UI "Update Server" page](./media/azure-stack-edge-gpu-deploy-set-up-device-update-time/update-2.png)

        You can also choose to deploy updates from the **Windows Server Update services** (WSUS). Provide the path to the WSUS server.
        
        ![Local web UI "Update Server" page](./media/azure-stack-edge-gpu-deploy-set-up-device-update-time/update-3.png)

        > [!NOTE] 
        > If a separate Windows Update server is configured and if you choose to connect over *https* (instead of *http*), then signing chain certificates required to connect to the update server are needed. For information on how to create and upload certificates, go to [Manage certificates](azure-stack-edge-j-series-manage-certificates.md). 

2. Select **Apply**.
3. After the update server is configured, go back to **Get started**.
    

## Configure time

Follow these steps to configure time settings on your device. 

1. On the **Device setup** tile, select **Time**. You can select the time zone, and the primary and secondary NTP servers for your device.  

    > [!IMPORTANT]
    > Though the time settings are optional, we strongly recommend that you configure a primary NTP and a secondary NTP server on the local network for your device. If local server is not available, public NTP servers can be configured.
    
    NTP servers are required because your device must synchronize time so that it can authenticate with your cloud service providers.

    ![Local web UI "Time" page](./media/azure-stack-edge-gpu-deploy-set-up-device-update-time/time-1.png)

2. On the **Time** page, do the following:
    
    1. In the **Time zone** drop-down list, select the time zone that corresponds to the geographic location in which the device is being deployed.
        The default time zone for your device is PST. Your device will use this time zone for all scheduled operations.

    2. In the **Primary NTP server** box, enter the primary server for your device or accept the default value of time.windows.com.  
        Ensure that your network allows NTP traffic to pass from your datacenter to the internet.

    3. Optionally, in the **Secondary NTP server** box, enter a secondary server for your device.

    4. To validate and apply the configured time settings, select **Apply**.

        ![Local web UI "Time" page](./media/azure-stack-edge-gpu-deploy-set-up-device-update-time/time-2.png)

3. After the settings are applied, go back to **Get started**.



## Next steps

In this tutorial, you learn about:

> [!div class="checklist"]
>
> * Prerequisites
> * Configure device settings
> * Configure update 
> * Configure time

To learn how to configure certificates for your Azure Stack Edge device, see:

> [!div class="nextstepaction"]
> [Configure certificates](./azure-stack-edge-gpu-deploy-configure-certificates.md)
