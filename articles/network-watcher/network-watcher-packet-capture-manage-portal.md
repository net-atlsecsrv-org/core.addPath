---
title: Manage packet captures - Azure portal
titleSuffix: Azure Network Watcher
description: Learn how to manage the packet capture feature of Network Watcher using the Azure portal.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload:  infrastructure-services
ms.date: 09/10/2018
ms.author: damendo
---

# Manage packet captures with Azure Network Watcher using the portal

Network Watcher packet capture allows you to create capture sessions to track traffic to and from a virtual machine. Filters are provided for the capture session to ensure you capture only the traffic you want. Packet capture helps to diagnose network anomalies, both reactively, and proactively. Other uses include gathering network statistics, gaining information on network intrusions, to debug client-server communication, and much more. Being able to remotely trigger packet captures, eases the burden of running a packet capture manually on a desired virtual machine, which saves valuable time.

In this article, you learn to start, stop, download, and delete a packet capture. 

## Before you begin

Packet capture requires the following outbound TCP connectivity:
- to the chosen storage account over port 443
- to 169.254.169.254 over port 80
- to 168.63.129.16 over port 8037

> [!NOTE]
> The ports mentioned in the latter two cases above are common across all Network Watcher features that involve the Network Watcher extension and might occasionally change.


If a network security group is associated to the network interface, or subnet that the network interface is in, ensure that rules exist that allow the previous ports. Similarly, adding user-defined traffic routes to your network may prevent connectivity to the above mentioned IPs and ports. Please ensure they are reachable. 

## Start a packet capture

1. In your browser, navigate to the [Azure portal](https://portal.azure.com) and select **All services**, and then select **Network Watcher** in the **Networking section**.
2. Select **Packet capture** under **Network diagnostic tools**. Any existing packet captures are listed, regardless of their status.
3. Select **Add** to create a packet capture. You can select values for the following properties:
   - **Subscription**: The subscription that the virtual machine you want to create the packet capture for is in.
   - **Resource group**: The resource group of the virtual machine.
   - **Target virtual machine**: The virtual machine that you want to create the packet capture for.
   - **Packet capture name**: A name for the packet capture.
   - **Storage account or file**: Select **Storage account**, **File**, or both. If you select **File**, the capture is written to a path within the virtual machine.
   - **Local file path**: The local path on the virtual machine where the packet capture will be saved (valid only when *File* is selected). The path must be a valid path. If you are using a Linux virtual machine, the path must start with */var/captures*.
   - **Storage accounts**: Select an existing storage account, if you selected *Storage account*. This option is only available if you selected **Storage**.
   
     > [!NOTE]
     > Premium storage accounts are currently not supported for storing packet captures.

   - **Maximum bytes per packet**: The number of bytes from each packet that are captured. If left blank, all bytes are captured.
   - **Maximum bytes per session**: The total number of bytes that are captured. Once the value is reached the packet capture stops.
   - **Time limit (seconds)**: The time limit before the packet capture is stopped. The default is 18,000 seconds.
   - Filtering (Optional). Select **+ Add filter**
     - **Protocol**: The protocol to filter for the packet capture. The available values are TCP, UDP, and Any.
     - **Local IP address**: Filters the packet capture for packets where the local IP address matches this value.
     - **Local port**: Filters the packet capture for packets where the local port matches this value.
     - **Remote IP address**: Filters the packet capture for packets where the remote IP address matches this value.
     - **Remote port**: Filters the packet capture for packets where the remote port matches this value.
    
     > [!NOTE]
     > Port and IP address values can be a single value, range of values, or a range, such as 80-1024, for port. You can define as many filters as you need.

4. Select **OK**.

After the time limit set on the packet capture has expired, the packet capture is stopped, and can be reviewed. You can also manually stop a packet capture session.

> [!NOTE]
> The portal automatically:
>  * Creates a  network watcher in the same region as the region the virtual machine you selected exists in, if the region doesn't already have a network watcher.
>  * Adds the *AzureNetworkWatcherExtension* [Linux](../virtual-machines/extensions/network-watcher-linux.md) or [Windows](../virtual-machines/extensions/network-watcher-windows.md) virtual machine extension to the virtual machine, if it's not already installed.

## Delete a packet capture

1. In the packet capture view, select **...** on the right-side of the packet capture, or right-click an existing packet capture, and select **Delete**.
2. You are asked to confirm you want to delete the packet capture. Select **Yes**.

> [!NOTE]
> Deleting a packet capture does not delete the capture file in the storage account or on the virtual machine.

## Stop a packet capture

In the packet capture view, select **...** on the right-side of the packet capture, or right-click an existing packet capture, and select **Stop**.

## Download a packet capture

Once your packet capture session has completed, the capture file is uploaded to blob storage or to a local file on the virtual machine. The storage location of the packet capture is defined during creation of the packet capture. A convenient tool to access capture files saved to a storage account is Microsoft Azure Storage Explorer, which you can [download](https://storageexplorer.com/).

If a storage account is specified, packet capture files are saved to a storage account at the following location:

```
https://{storageAccountName}.blob.core.windows.net/network-watcher-logs/subscriptions/{subscriptionId}/resourcegroups/{storageAccountResourceGroup}/providers/microsoft.compute/virtualmachines/{VMName}/{year}/{month}/{day}/packetCapture_{creationTime}.cap
```

If you selected **File** when you created the capture, you can view or download the file from the path you configured on the virtual machine.

## Next steps

- To learn how to automate packet captures with virtual machine alerts, see [Create an alert triggered packet capture](network-watcher-alert-triggered-packet-capture.md).
- To determine whether specific traffic is allowed in or out of a virtual machine, see [Diagnose a virtual machine network traffic filter problem](diagnose-vm-network-traffic-filtering-problem.md).