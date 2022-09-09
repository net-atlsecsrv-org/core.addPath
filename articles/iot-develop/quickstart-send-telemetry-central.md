---
title: Quickstart - connect a device and send telemetry to Azure IoT Central
description: This quickstart shows device developers how to connect a device securely to Azure IoT Central. You use an Azure IoT device SDK for C, C#, Python, Node.js, or Java, to run a client app on a simulated device, then you connect to IoT Central and send telemetry.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.topic: quickstart
ms.date: 04/27/2021
ms.collection: embedded-developer, application-developer
zone_pivot_groups: iot-develop-set1

#Customer intent: As a device application developer, I want to learn the basic workflow of using an Azure IoT device SDK to build a client app on a device, connect the device securely to Azure IoT Central, and send telemetry.
---

# Quickstart: Send telemetry from a device to Azure IoT Central

**Applies to**: [Device application developers](about-iot-develop.md#device-application-development)

In this quickstart, you learn a basic Azure IoT application development workflow. First you create an Azure IoT Central application for hosting devices. Then you use an Azure IoT device SDK sample to run a simulated temperature controller, connect it securely to IoT Central, and send telemetry.

:::zone pivot="programming-language-ansi-c"

[!INCLUDE [iot-develop-send-telemetry-central-c](../../includes/iot-develop-send-telemetry-central-c.md)]

:::zone-end

:::zone pivot="programming-language-csharp"

[!INCLUDE [iot-develop-send-telemetry-central-csharp](../../includes/iot-develop-send-telemetry-central-csharp.md)]

:::zone-end

:::zone pivot="programming-language-nodejs"

[!INCLUDE [iot-develop-send-telemetry-central-nodejs](../../includes/iot-develop-send-telemetry-central-nodejs.md)]

:::zone-end

:::zone pivot="programming-language-python"

[!INCLUDE [iot-develop-send-telemetry-central-python](../../includes/iot-develop-send-telemetry-central-python.md)]

:::zone-end

## View telemetry
After the simulated device connects to IoT Central, it begins sending telemetry. You can view the telemetry and other details about connected devices in IoT Central. 

In IoT Central, select **Devices**, click your device name, then select the **Raw data** tab. This view displays the raw telemetry from the simulated device.

:::image type="content" source="media/quickstart-send-telemetry-central/iot-central-telemetry-output.png" alt-text="IoT Central device telemetry raw output":::

Your device is now securely connected and sending telemetry to Azure IoT.
    
## Clean up resources
If you no longer need the IoT Central resources created in this quickstart, you can delete them. Optionally, if you plan to continue following the documentation in this guide, you can keep the application you created and reuse it for other samples.

To remove the Azure IoT Central sample application and all its devices and resources:
1. Select **Administration** > **Your application**.
1. Select **Delete**.

## Next steps

In this quickstart, you learned a basic Azure IoT application workflow for securely connecting a device to the cloud and sending device-to-cloud telemetry. You used Azure IoT Central to create an application and a device instance. Then you used an Azure IoT device SDK to create a simulated device, connect to IoT Central, and send telemetry. You also used IoT Central to monitor the telemetry.

As a next step, explore the following articles to learn more about building device solutions with Azure IoT. 

> [!div class="nextstepaction"]
> [Send telemetry to Azure IoT hub](quickstart-send-telemetry-cli-python.md)
> [!div class="nextstepaction"]
> [Create an IoT Central application](../iot-central/core/quick-deploy-iot-central.md)