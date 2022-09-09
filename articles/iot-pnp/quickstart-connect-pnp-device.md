---
title: Connect IoT Plug and Play Preview sample device code to IoT Hub | Microsoft Docs
description: Build and run IoT Plug and Play Preview sample device code that connects to an IoT hub. Use the Azure IoT explorer tool to view the information sent by the device to the hub.
author: ChrisGMsft
ms.author: chrisgre
ms.date: 08/02/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc

# As a device developer, I want to see a working IoT Plug and Play device sample connecting to IoT Hub and sending properties, commands and telemetry. As a solution developer, I want to use a tool to view the properties, commands, and telemetry an IoT Plug and Play device reports to the IoT hub it connects to.
---

# Quickstart: Connect a sample IoT Plug and Play Preview device application to IoT Hub

This quickstart shows you how to build a sample IoT Plug and Play device application, connect it to your IoT hub, and use the Azure IoT explorer tool to view the information it sends to the hub. The sample application is written in C and is included in the Azure IoT device SDK for C. A solution developer can use the Azure IoT explorer tool to understand the capabilities of an IoT Plug and Play device without the need to view any device code.

## Prerequisites

To complete this quickstart, you need to install the following software on your local machine:

* [Visual Studio (Community, Professional, or Enterprise)](https://visualstudio.microsoft.com/downloads/) - make sure that you include the **NuGet package manager** component and the **Desktop Development with C++** workload when you install Visual Studio.
* [Git](https://git-scm.com/download/).
* [CMake](https://cmake.org/download/).

### Install the Azure IoT explorer

Download and install the Azure IoT explorer tool from the [latest release](https://github.com/Azure/azure-iot-explorer/releases) page.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## Prepare an IoT hub

You also need an Azure IoT hub in your Azure subscription to complete this quickstart. If you don't have an Azure subscription, create a [free account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) before you begin.

> [!NOTE]
> During public preview, IoT Plug and Play features are only available on IoT hubs created in the **Central US**, **North Europe**, and **Japan East** regions.

Add the Microsoft Azure IoT Extension for Azure CLI:

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

Run the following command to create the device identity in your IoT hub. Replace the **YourIoTHubName** and **YourDevice** placeholders with your actual names. If you don't have an IoT Hub, follow [these instructions to create one](../iot-hub/iot-hub-create-using-cli.md):

```azurecli-interactive
az iot hub device-identity create --hub-name [YourIoTHubName] --device-id [YourDevice]
```

Run the following commands to get the _device connection string_ for the device you just registered:

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name [YourIoTHubName] --device-id [YourDevice] --output table
```

Run the following commands to get the _IoT hub connection string_ for your hub:

```azurecli-interactive
az iot hub show-connection-string --hub-name [YourIoTHubName] --output table
```

## Prepare the development environment

### Get Azure IoT device SDK for C

In this quickstart, you prepare a development environment you can use to clone and build the Azure IoT C device SDK.

Open a command prompt. Execute the following command to clone the [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub repository:

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
```

You should expect this operation to take several minutes to complete.

## Build the code

The application you build simulates a device that connects to an IoT hub. The application sends telemetry and properties and receives commands.

1. Create a `cmake` subdirectory in the device SDK root folder, and navigate to that folder:

    ```cmd\sh
    cd <root folder>\azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

1. Run the following commands to build the device SDK and the generated code stub:

    ```cmd\sh
    cmake ..
    cmake --build . -- /m /p:Configuration=Release
    ```

    > [!NOTE]
    > If cmake can't find your C++ compiler, you get build errors when you run the previous command. If that happens, try running this command at the [Visual Studio command prompt](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs).

## Run the device sample

Run your application by passing the IoT hub device connection string as parameter.

```cmd\sh
cd digitaltwin_client\samples\digitaltwin_sample_device\Release
copy ..\EnvironmentalSensor.interface.json .
digitaltwin_sample_device.exe "[IoT Hub device connection string]"
```

The device application starts sending data to IoT Hub.

## Use the Azure IoT explorer to validate the code

1. Open Azure IoT explorer, you see the **App configurations** page.

1. Enter your IoT Hub connection string and click **Connect**.

1. After you connect, you see the device overview page.

1. To add your company repository, select **Settings**, then **+ New**, and then **On the connected device**.

1. On the device overview page, find the device identity you created previously, and select it to view more details.

1. Expand the interface with ID **urn:YOUR_COMPANY_NAME_HERE:EnvironmentalSensor:1** to see the IoT Plug and Play primitives - properties, commands, and telemetry.

1. Select the **Telemetry** page to view the telemetry data the device is sending.

1. Select the **Properties(non-writable)** page to view the non-writable properties reported by the device.

1. Select the **Properties(writable)** page to view the writable properties you can update.

1. Expand property **name**, update with a new name and select **update writable property**. 

1. To see the new name shows up in the **Reported Property** column, click the **Refresh** button on top of the page.

1. Select the **Command** page to view all the commands the device supports.

1. Expand the **blink** command and set a new blink time interval. Select **Send Command** to call the command on the device.

1. Go to the simulated device to verify that the command executed as expected.

## Next steps

In this quickstart, you've learned how to connect an IoT Plug and Play device to an IoT hub. To learn more about how to build a solution that interacts with your IoT Plug and Play devices, see:

> [!div class="nextstepaction"]
> [How-to: Connect to and interact with an IoT Plug and Play Preview device](howto-develop-solution.md)
