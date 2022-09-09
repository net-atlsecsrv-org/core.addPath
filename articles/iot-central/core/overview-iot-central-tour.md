---
title: Take a tour of the Azure IoT Central UI | Microsoft Docs
description: Become familiar with the key areas of the Azure IoT Central UI that you use to create, manage, and use your IoT solution.
author: ankitscribbles
ms.author: ankitgup
ms.date: 02/09/2021
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: corywink
---

# Take a tour of the Azure IoT Central UI

This article introduces you to Azure IoT Central UI. You can use the UI to create, manage, and use an IoT Central application and its connected devices.

## IoT Central homepage

The [IoT Central homepage](https://aka.ms/iotcentral-get-started) page is the place to learn more about the latest news and features available on IoT Central, create new applications, and see and launch your existing applications.

:::image type="content" source="media/overview-iot-central-tour/iot-central-homepage.png" alt-text="IoT Central homepage":::

### Create an application

In the **Build** section you can browse the list of industry-relevant IoT Central templates, or start from scratch using a Custom app template.  

:::image type="content" source="media/overview-iot-central-tour/iot-central-build.png" alt-text="IoT Central build page":::

To learn more, see the [Create an Azure IoT Central application](quick-deploy-iot-central.md) quickstart.

### Launch your application

You launch your IoT Central application by navigating to the URL you chose during app creation. You can also see a list of all the applications you have access to in the [IoT Central app manager](https://aka.ms/iotcentral-apps).

:::image type="content" source="media/overview-iot-central-tour/app-manager.png" alt-text="IoT Central app manager":::

## Navigate your application

Once you're inside your IoT application, use the left pane to access various features. You can expand or collapse the left pane by selecting the three-lined icon on top of the pane:

> [!NOTE]
> The items you see in the left pane depend on your user role. Learn more about [managing users and roles](howto-manage-users-roles.md). 

:::row:::
  :::column span="":::
      :::image type="content" source="media/overview-iot-central-tour/navigation-bar.png" alt-text="left pane":::

  :::column-end:::
  :::column span="2":::
     **Dashboard** displays all application and personal dashboards. 
     
     **Devices** lets you manage all your devices.

     **Device groups** lets you view and create collections of devices specified by a query. Device groups are used through the application to perform bulk operations.

     **Rules** lets you create and edit rules to monitor your devices. Rules are evaluated based on device data and trigger customizable actions.

     **Analytics** exposes rich capabilities to analyze historical trends and correlate various telemetries from your devices.

     **Jobs** lets you manage your devices at scale by running bulk operations.

     **Device templates** lets you create and manage the characteristics of devices that connect to your application.

     **Data export** lets you configure a continuous export to external services such as storage and queues.

     **Administration** lets you manage your application's settings, customization, billing, users, and roles.

     **My apps** lets you jump back to the IoT Central app manager.
     
   :::column-end:::
:::row-end:::

### Search, help, theme, and support

The top menu appears on every page:

:::image type="content" source="media/overview-iot-central-tour/toolbar.png" alt-text="IoT Central Toolbar":::

* To search for devices, enter a **Search** value.
* To change the UI language or theme, choose the **Settings** icon. Learn more about [managing your application preferences](howto-manage-preferences.md)
* To get help and support, choose the **Help** drop-down for a list of resources. You can [get information about your application](./howto-get-app-info.md) from the **About your app** link. In an application on the free pricing plan, the support resources include access to [live chat](howto-show-hide-chat.md).
* To sign out of the application, choose the **Account** icon.

You can choose between a light theme or a dark theme for the UI:

> [!NOTE]
> The option to choose between light and dark themes isn't available if your administrator has configured a custom theme for the application.

:::image type="content" source="media/overview-iot-central-tour/themes.png" alt-text="Screenshot of IoT Central Choose a Theme.":::

### Dashboard

:::image type="content" source="Media/overview-iot-central-tour/dashboard.png" alt-text="Screenshot of IoT Central Dashboard.":::

* **Dashboard** is the first page you see when you sign in to your IoT Central application. You can create and customize multiple application dashboards. Learn more about [adding tiles to your dashboard](howto-add-tiles-to-your-dashboard.md)

* Personal dashboards can also be created to monitor what you care about. To learn more, see the [Create Azure IoT Central personal dashboards](howto-create-personal-dashboards.md) how-to article.

### Devices

:::image type="content" source="Media/overview-iot-central-tour/devices.png" alt-text="Screenshot of Devices Page.":::

This page shows the devices in your IoT Central application grouped by _device template_.

* A device template defines a type of device that can connect to your application.
* A device represents either a real or simulated device in your application.

To learn more, see the [Monitor your devices](./quick-monitor-devices.md) quickstart. 

### Device groups

:::image type="content" source="Media/overview-iot-central-tour/device-groups.png" alt-text="Device Group page":::

This page lets you create and view device groups in your IoT Central application. You can use device groups to do bulk operations in your application or to analyze data. To learn more, see the [Use device groups in your Azure IoT Central application](tutorial-use-device-groups.md) article.

### Rules
:::image type="content" source="Media/overview-iot-central-tour/rules.png" alt-text="Screenshot of Rules Page.":::

This page lets you view and create rules based on device data. When a rule fires, it can trigger one or more actions such as sending an email or invoking a webhook. To learn, see the [Configuring rules](tutorial-create-telemetry-rules.md) tutorial.

### Analytics

:::image type="content" source="Media/overview-iot-central-tour/analytics.png" alt-text="Screenshot of Analytics page.":::

Analytics exposes rich capabilities to analyze historical trends and correlate various telemetries from your devices. To learn more, see the [Create analytics for your Azure IoT Central application](howto-create-analytics.md) article.

### Jobs

:::image type="content" source="Media/overview-iot-central-tour/jobs.png" alt-text="Jobs Page":::

This page lets you view and create jobs that can be used for bulk device management operations on your devices. You can update device properties, settings, and execute commands against device groups. To learn more, see the [Run a job](howto-run-a-job.md) article.

### Device templates

:::image type="content" source="Media/overview-iot-central-tour/templates.png" alt-text="Screenshot of Device Templates.":::

The device templates page is where you can view and create device templates in the application. To learn more, see the [Define a new device type in your Azure IoT Central application](howto-set-up-template.md) tutorial.

### Data export

:::image type="content" source="Media/overview-iot-central-tour/export.png" alt-text="Data Export":::

Data export enables you to set up streams of data to external systems. To learn more, see the [Export your data in Azure IoT Central](./howto-export-data.md) article.

### Administration

:::image type="content" source="media/overview-iot-central-tour/administration.png" alt-text="Screenshot of IoT Administration.":::

The administration page allows you to configure and customize your IoT Central application. Here you can change your application name, URL, theming, manage users and roles, create API tokens, and export your application. To learn more, see the [Administer your Azure IoT Central application](howto-administer.md) article.

## Next steps

Now that you have an overview of Azure IoT Central and are familiar with the layout of the UI, the suggested next step is to complete the [Create an Azure IoT Central application](quick-deploy-iot-central.md) quickstart.
