---
title: Managing public network access for Azure IoT hub
description: Documentation on how to disable and enable public network access for IoT hub
author: jlian
ms.author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/22/2021
---

# Managing public network access for your IoT hub

To restrict access to only [private endpoint for your IoT hub in your VNet](virtual-network-support.md), disable public network access. To do so, use the Azure portal or the `publicNetworkAccess` API. You can also allow public access by using the portal or the `publicNetworkAccess` API.

## Turn off public network access using Azure portal

1. Visit the [Azure portal](https://portal.azure.com)
2. Navigate to your IoT hub. Go to **Resource Groups**, choose the appropriate group, and select your IoT Hub.
3. Select **Networking** from the left-side menu.
4. Under “Allow public network access to”, select **Disabled**
5. Select **Save**.

:::image type="content" source="media/iot-hub-publicnetworkaccess/turn-off-public-network-access.png" alt-text="Image showing Azure portal where to turn off public network access" lightbox="media/iot-hub-publicnetworkaccess/turn-off-public-network-access.png":::

To turn on public network access, selected **All networks**, then **Save**.

### Accessing the IoT Hub after disabling public network access

After public network access is disabled, the IoT Hub is only accessible through [its VNet private endpoint using Azure private link](virtual-network-support.md). This restriction includes accessing through Azure portal, because API calls to the IoT Hub service are made directly using your browser with your credentials.

### IoT Hub endpoint, IP address, and ports after disabling public network access

IoT Hub is a multi-tenant Platform-as-a-Service (PaaS), so different customers share the same pool of compute, networking, and storage hardware resources. IoT Hub's hostnames map to a public endpoint with a publicly routable IP address over the internet. Different customers share this IoT Hub public endpoint, and IoT devices in over wide-area networks and on-premises networks can all access it. 

Disabling public network access is enforced on a specific IoT hub resource, ensuring isolation. To keep the service active for other customer resources using the public path, its public endpoint remains resolvable, IP addresses discoverable, and ports remain open. This is not a cause for concern as Microsoft integrates multiple layers of security to ensure complete isolation between tenants. To learn more, see [Isolation in the Azure Public Cloud](../security/fundamentals/isolation-choices.md#tenant-level-isolation).

### IP Filter

If public network access is disabled, all [IP Filter](iot-hub-ip-filtering.md) rules are ignored. This is because all IPs from the public internet are blocked. To use IP Filter, use the **Selected IP ranges** option.

### Bug fix with built-in Event Hub compatible endpoint

There is a bug with IoT Hub where the [built-in Event Hub compatible endpoint](iot-hub-devguide-messages-read-builtin.md) continues to be accessible via public internet when public network access to the IoT Hub is disabled. To learn more and contact us about this bug, see [Disabling public network access for IoT Hub disables access to built-in Event Hub endpoint](https://azure.microsoft.com/updates/iot-hub-public-network-access-bug-fix).

## Turn on network access using Azure portal

1. Visit the [Azure portal](https://portal.azure.com)
2. Navigate to your IoT hub. Go to **Resource Groups**, choose the appropriate group, and select your hub.
3. Select **Networking** from the left-side menu.
4. Under “Allow public network access to”, select **Selected IP Ranges**.
5. In the **IP Filter** dialog that opens, select **Add your client IP address** and enter a name and an address range.
6. Select **Save**. If the button is greyed out, make sure your client IP address is already added as an IP filter.

:::image type="content" source="media/iot-hub-publicnetworkaccess/turn-on-public-network-access.png" alt-text="Image showing Azure portal where to turn on public network access":::

### Turn on all network ranges

1. Navigate to your IoT hub. Go to **Resource Groups**, choose the appropriate group, and select your hub.
1. Select **Networking** from the left-side menu.
1. Under “Allow public network access to”, select **All networks**.
1. Select **Save**.

### Check IoT hub access using Cloud Shell

You can check IoT hub access by using Azure Cloud Shell. Make sure that you've turned on all network ranges and then issue the following commands. Replace "SubscriptionName" with the name of your subscription and "MyIoTHub" with the name of your hub.

```azurecli
  az account set -s "SubscriptionName"
  az iot hub device-identity list --hub-name "MyIoTHub"
```

```azurepowershell
  Set-AzContext -Name "SubscriptionName"
  Get-AzIoTHubDevice -IotHubName "MyIoTHub"
```
### Troubleshooting

If you have trouble accessing your IoT hub, your network configuration could be the problem. For example, if you see the following error message when trying to access the IoT devices page, check the **Networking** page to see if public network access is disabled or restricted to selected IP ranges.

```
  Unable to retrieve devices. Please ensure that your network connection is online and network settings allow connections from your IP address.
```

To get access to the IoT hub, request permission from your IT administrator to add your IP address in the IP address range or to enable public network access to all networks. If that fails to resolve the issue, check your local network settings or contact your local network administrator to fix connectivity to IoT Hub. For example, sometimes a proxy in the local network can interfere with access to IoT Hub.

If the preceding commands do not work or you cannot turn on all network ranges, contact Microsoft support.
