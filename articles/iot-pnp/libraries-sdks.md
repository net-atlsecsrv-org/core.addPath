---
title: IoT Plug and Play libraries and SDKs
description: Information about the device and service libraries available for developing IoT Plug and Play enabled solutions.
author: rido-min
ms.author: rmpablos
ms.date: 07/22/2020
ms.topic: reference
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
---

# Microsoft SDKs for IoT Plug and Play

The IoT Plug and Play libraries and SDKs enable developers to build IoT solutions using a variety of programming languages on multiple platforms. The following table includes links to samples and quickstarts to help you get started:

## Device SDKs

| Language | Package | Code Repository | Samples | Quickstart | Reference |
|---|---|---|---|---|---|
| C - Device | [vcpkg 1.3.9](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/setting_up_vcpkg.md) | [GitHub](https://github.com/Azure/azure-iot-sdk-c) | [Samples](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/pnp) | [Connect to IoT Hub](quickstart-connect-device-c.md) | [Reference](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| .NET - Device | [NuGet 1.31.0](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/) | [Samples](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Samples/device/PnpDeviceSamples) | [Connect to IoT Hub](quickstart-connect-device-csharp.md) | [Reference](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client?view=azure-dotnet&preserve-view=true) |
| Java - Device | [Maven 1.26.0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-device-client) | [GitHub](https://github.com/Azure/azure-iot-sdk-java/tree/master/) | [Samples](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/pnp-device-sample) | [Connect to IoT Hub](quickstart-connect-device-java.md) | [Reference](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device?view=azure-java-stable&preserve-view=true) |
| Python - Device | [pip 2.3.0](https://pypi.org/project/azure-iot-device/) | [GitHub](https://github.com/Azure/azure-iot-sdk-python/tree/master/) | [Samples](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/pnp) | [Connect to IoT Hub](quickstart-connect-device-python.md) | [Reference](https://docs.microsoft.com/python/api/azure-iot-device/azure.iot.device?view=azure-python&preserve-view=true) |
| Node - Device | [npm 1.17.2](https://www.npmjs.com/package/azure-iot-device)  | [GitHub](https://github.com/Azure/azure-iot-sdk-node/tree/master/) | [Samples](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples/pnp) | [Connect to IoT Hub](quickstart-connect-device-node.md) | [Reference](https://docs.microsoft.com/javascript/api/azure-iot-device/?view=azure-node-latest&preserve-view=true) |
| Embedded C - Device | N/A | [GitHub](https://github.com/Azure/azure-sdk-for-c/)| [Samples](howto-use-embedded-c.md#samples) | [How to use Embedded C](howto-use-embedded-c.md) | N/A

## Service SDKs

| Platform  | Package | Code Repository | Samples | Quickstart | Reference |
|---|---|---|---|---|---|
| .NET - IoT Hub service | [NuGet 1.27.1](https://www.nuget.org/packages/Microsoft.Azure.Devices ) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp) | [Samples](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Samples/service/PnpServiceSamples) | N/A | [Reference](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices?view=azure-dotnet&preserve-view=true) |
| Java - IoT Hub service | [Maven 1.26.0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-service-client/1.26.0) | [GitHub](https://github.com/Azure/azure-iot-sdk-java) | [Samples](https://github.com/Azure/azure-iot-sdk-java/tree/master/service/iot-service-samples/pnp-service-sample) | N/A | [Reference](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.service?view=azure-java-stable&preserve-view=true) |
| Node - IoT Hub service | [npm 1.13.0](https://www.npmjs.com/package/azure-iothub) | [GitHub](https://github.com/Azure/azure-iot-sdk-node) | [Samples](https://github.com/Azure/azure-iot-sdk-node/tree/master/service/samples) | N/A | [Reference](https://docs.microsoft.com/javascript/api/azure-iothub/?view=azure-node-latest&preserve-view=true) |
| Python - Digital Twins service | [pip 2.2.3](https://pypi.org/project/azure-iot-hub) | [GitHub](https://github.com/Azure/azure-iot-sdk-python) | [Samples](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-hub/samples) | [Interact with IoT Hub Digital Twins API](quickstart-service-python.md) | N/A |
| Node - Digital Twins service | [npm 1.13.0](https://www.npmjs.com/package/azure-iot-digitaltwins-service) | [GitHub](https://github.com/Azure/azure-iot-sdk-node) | [Samples](https://github.com/Azure/azure-iot-sdk-node/tree/master/service/samples/javascript) | [Interact with IoT Hub Digital Twins API](quickstart-service-node.md) | N/A |

## Next steps

To try out the SDKs and libraries, see the  [Developer Guide](concepts-developer-guide-device-csharp.md) and the [device quickstarts](quickstart-connect-device-c.md) and [service quickstarts](quickstart-service-node.md).
