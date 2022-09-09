---
title: Connect a DevKit device to your Azure IoT Central application | Microsoft Docs
description: As a device developer, learn how to connect an MXChip IoT DevKit device to your Azure IoT Central application using IoT Plug and Play.
author: liydu
ms.author: liydu
ms.date: 08/17/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: jeffya
---

# Connect an MXChip IoT DevKit device to your Azure IoT Central application

This article shows you how to connect an MXChip IoT DevKit (DevKit) device to an Azure IoT Central application. The device uses the certified IoT Plug and Play model for the DevKit device to configure its connection to IoT Central.

In this how-to article, you:

- Get the connection details from your IoT Central application.
- Prepare the device and connect it to your IoT Central application.
- View the telemetry and properties from the device in IoT Central.

## Prerequisites

To complete the steps in this article, you need the following resources:

1. A [DevKit device](https://aka.ms/iot-devkit-purchase).
1. An IoT Central application created from the **Preview application** template. You can follow the steps in [Create an IoT Plug and Play application](./quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

## Get device connection details

In your Azure IoT Central application, select the **Administration** tab and select **Device Connection**. Make a note of the **Scope ID** and **Primary key**.

![Device group connection details](media/howto-connect-devkit-pnp/device-group-connection-details.png)

## Prepare the device

1. Download the latest [pre-built Azure IoT Central Plug and Play firmware](https://github.com/MXCHIP/IoTDevKit/raw/master/pnp/iotc_devkit/bin/iotc_devkit.bin) for the DevKit device from GitHub.

1. Connect the DevKit device to your development machine using a USB cable. In Windows, a file explorer window opens on a drive mapped to the storage on the DevKit device. For example, the drive might be called **AZ3166 (D:)**.

1. Drag the **iotc_devkit.bin** file onto the drive window. When the copying is complete, the device reboots with the new firmware.

    > [!NOTE]
    > If you see errors on the screen such as **No Wi-Fi**, this is because the DevKit has not yet been connected to WiFi.

1. On the DevKit, hold down **button B**, push and release the **Reset** button, and then release **button B**. The device is now in access point mode. To confirm, the screen displays "IoT DevKit - AP" and the configuration portal IP address.

1. On your computer or tablet, connect to the WiFi network name shown on the screen of the device. The WiFi network starts with **AZ-** followed by the MAC address. When you connect to this network, you don't have internet access. This state is expected, and you only connect to this network for a short time while you configure the device.

1. Open your web browser and navigate to [http://192.168.0.1/](http://192.168.0.1/). The following web page displays:

    ![Config UI](media/howto-connect-devkit-pnp/config-ui.png)

    On the web page, enter:

    - The name of your WiFi network (SSID).
    - Your WiFi network password.
    - The connection details: the **Device ID** that you can choose yourself, and the **Scope ID** and **Group SAS Primary Key** you made a note of previously.

    > [!NOTE]
    > Currently, the IoT DevKit only can connect to 2.4 GHz Wi-Fi, 5 GHz is not supported due to hardware restrictions.

1. Choose **Configure Device**, the DevKit device reboots and runs the application:

    ![Reboot UI](media/howto-connect-devkit-pnp/reboot-ui.png)

    The DevKit screen displays a confirmation that the application is running:

    ![DevKit running](media/howto-connect-devkit-pnp/devkit-running.png)

The DevKit first registers a new device in IoT Central application and then starts sending data.

## View the telemetry

In this step, you view the telemetry in your Azure IoT Central application.

In your IoT Central application, select **Devices** tab, select the device you added. In the **Overview** tab, you can see the telemetry from the DevKit device:

   ![IoT Central device overview](media/howto-connect-devkit-pnp/mxchip-overview-page.png)

## Review the code

To review the code or modify and compile it, go to the [MXChip IoT DevKit sample code GitHub repository](https://github.com/MXCHIP/IoTDevKit/tree/master/pnp).

## Next steps

Now that you've learned how to connect a DevKit device to your Azure IoT Central application, the suggested next step is to learn how to [set up a custom device template](./howto-set-up-template-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) for your own IoT device.
