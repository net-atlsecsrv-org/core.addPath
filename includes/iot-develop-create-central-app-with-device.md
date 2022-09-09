---
 title: include file
 description: include file
 author: timlt
 ms.service: iot-develop
 ms.topic: include
 ms.date: 04/28/2021
 ms.author: timlt
 ms.custom: include file
---

## Create an application
There are several ways to connect devices to Azure IoT. In this section, you learn how to connect a device by using Azure IoT Central. IoT Central is an IoT application platform that reduces the cost and complexity of managing devices in an IoT solution.

To create a new application:
1. Browse to [Azure IoT Central](https://apps.azureiotcentral.com/) and sign in with a Microsoft personal, work, or school account.
1. Navigate to **Build** and select **Custom apps**.
   :::image type="content" source="media/iot-develop-create-central-app-with-device/iot-central-build.png" alt-text="IoT Central start page":::
1. In **Application name**, enter a unique name or use the generated name.
1. In **URL**, enter a memorable application URL prefix or use the generated URL prefix.
1. Leave **Application template** set to *Custom application*. 
1. Select a **Pricing plan** option. 
    - To use the application for free for seven days, select **Free**. You can convert a free application to standard pricing before it expires.
    - Optionally, you can select a standard pricing plan. If you select standard pricing, more options appear and you'll need to set a **Directory**, an **Azure subscription**, and a **Location**. To learn about pricing, see [Azure IoT Central pricing](https://azure.microsoft.com/pricing/details/iot-central/). 
        - **Directory** is the Azure Active Directory in which you create your application. An Azure Active Directory contains user identities, credentials, and other organizational information. If you don't have an Azure Active Directory, one is created when you create an Azure subscription.
        - An **Azure subscription** enables you to create instances of Azure services. IoT Central provisions resources in your subscription. If you don't have a subscription, you can create one for [free](https://aka.ms/createazuresubscription). If you have a subscription, you can select it in the dropdown.
        - **Location** is the [Azure geography](https://azure.microsoft.com/global-infrastructure/geographies/) in which you create an application. Select a location that's physically closest to your devices to get optimal performance. After you choose a location, you can't move the application to a different location.

    :::image type="content" source="media/iot-develop-create-central-app-with-device/iot-central-pricing.png" alt-text="IoT Central new application dialog":::
1. Select **Create**.
    
    After IoT Central creates the application, it redirects you to the application dashboard.
    :::image type="content" source="media/iot-develop-create-central-app-with-device/iot-central-created.png" alt-text="IoT Central new application dashboard":::

## Add a device
In this section, you add a new device to your IoT Central application. The device is an instance of a device template that represents a real or simulated device that you'll connect to the application. 

To create a new device:
1. In the left pane select **Devices**, then select **+New**.
1. Leave **Device template** set to *Unassigned*.

    > [!NOTE]
    > In this quickstart for simplicity, you connect a simulated device that uses an unassigned template. If you continue using IoT Central to manage devices, you'll learn about using device templates. For an overview of working with device templates, see [Quickstart: Add a simulated device to your IoT Central application](../articles/iot-central/core/quick-create-simulated-device.md).
1. Set a friendly **Device name** and **Device ID**. Optionally, use the generated values.
    :::image type="content" source="media/iot-develop-create-central-app-with-device/iot-central-create-device.png" alt-text="IoT Central new device dialog":::
1. Select **Create**.

    The created device appears in the **All devices** list.
    :::image type="content" source="media/iot-develop-create-central-app-with-device/iot-central-devices-list.png" alt-text="IoT Central all devices list":::
    
To get connection details for the new device:
1. In the **All devices** list, click the linked device name to display details. 
1. In the top menu, select **Connect**.

    The **Device connection** dialog displays the connection details:
    :::image type="content" source="media/iot-develop-create-central-app-with-device/iot-central-device-connect.png" alt-text="IoT Central device connection details":::
1. Copy the following values from the **Device connection** dialog to a safe location. You'll use these values to connect your device to IoT Central.
    * `ID scope`
    * `Device ID`
    * `Primary key`