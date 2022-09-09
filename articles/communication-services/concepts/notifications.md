---
title: Notifications in Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Send notifications to users of apps built on Azure Communication Services.
author: mikben
manager: jken
services: azure-communication-services

ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
---
# Communication Services notifications

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

The Azure Communication Services chat and calling client libraries create a real-time messaging channel that allows signaling messages to be pushed to connected clients in an efficient, reliable manner. This enables you to build rich, real-time communication functionality into your applications without the need to implement complicated HTTP polling logic. However, on mobile applications, this signaling channel only remains connected when your application is active in the foreground. If you want your users to receive incoming calls or chat messages while your application is in the background, you should use push notifications.

Push notifications allow you to send information from your application to users' mobile devices. You can use push notifications to show a dialog, play a sound, or display incoming call UI. Azure Communication Services provides integrations with [Azure Event Grid](../../event-grid/overview.md) and [Azure Notification Hubs](../../notification-hubs/notification-hubs-push-notification-overview.md) that enable you to add push notifications to your apps.

## Trigger push notifications via Azure Event Grid

Azure Communication Services integrates with [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) to deliver real-time event notifications in a reliable, scalable and secure manner. You can leverage this integration to create a notification service that delivers mobile push notifications to your users by creating an event grid subscription that triggers an [Azure Function](../../azure-functions/functions-overview.md) or webhook.

:::image type="content" source="./media/notifications/acs-events-int.png" alt-text="Diagram showing how Communication Services integrates with Event Grid.":::

Learn more about [event handling in Azure Communication Services](./event-handling.md).

## Deliver push notifications via Azure Notification Hubs

You can connect an Azure Notification Hub to your Communication Services resource in order to automatically send push notifications to a user's mobile device when they receive an incoming call. You should used these push notifications to wake up your application from the background and display UI that let's the user accept or decline the call. 

:::image type="content" source="./media/notifications/acs-anh-int.png" alt-text="Diagram showing how communication services integrates with Azure Notifications Hub.":::

Communication Services uses Azure Notification Hub as a pass-through service to communicate with the various platform-specific push notification services using the [Direct Send](/rest/api/notificationhubs/direct-send) API. This allows you to reuse your existing Azure Notification Hub resources and configurations to deliver low latency, reliable calling notifications to your applications.

> [!NOTE]
> Currently only calling push notifications are supported.

### Notification Hub provisioning 

To deliver push notifications to client devices using Notification Hubs, [create a Notification Hub](../../notification-hubs/create-notification-hub-portal.md) within the same subscription as your Communication Services resource. Azure Notification Hubs must be configured for the Platform Notifications Service you want to use. To learn how to get push notifications in your client app from Notification Hubs, see [Getting started with Notification Hubs](../../notification-hubs/notification-hubs-android-push-notification-google-fcm-get-started.md) and select your target client platform from the drop-down list near the top of the page.

> [!NOTE]
> Currently the APNs and FCM platforms are supported.

Once your Notification hub is configured, you can associate it to your Communication Services resource by supplying a connection string for the hub using the Azure Resource Manager Client or through the Azure portal. The connection string should contain "Send" permissions. We recommend creating another access policy with "Send" only permissions specifically for your hub. Learn more about [Notification Hubs security and access policies](../../notification-hubs/notification-hubs-push-notification-security.md)

> [!IMPORTANT]
> This is applicable only to token authentication mode. Certificate authentication mode is not supported as of now.  
In order to enable APNS VOIP notifications, you must set the value of the bundle id when configuring the notification hub to be your application bundle ID with the `.voip` suffix. See [Use APNS VOIP through Notification Hubs](../../notification-hubs/voip-apns.md) for more details.

#### Using the Azure Resource Manager client to configure the Notification Hub

To log into Azure Resource Manager, execute the following and sign in using your credentials.

```console
armclient login
```

 Once successfully logged in execute the following to provision the notification hub:

```console
armclient POST /subscriptions/<sub_id>/resourceGroups/<resource_group>/providers/Microsoft.Communication/CommunicationServices/<resource_id>/linkNotificationHub?api-version=2020-08-20-preview "{'connectionString': '<connection_string>','resourceId': '<resource_id>'}"
```

#### Using the Azure portal to configure the Notification Hub

In the portal, navigate to your Azure Communication Services resource. Inside the Communication Services resource, select Push Notifications from the left menu of the Communication Services page and connect the Notification Hub that you provisioned earlier. You'll need to provide your connection string and resource ID here:

:::image type="content" source="./media/notifications/acs-anh-portal-int.png" alt-text="Screenshot showing the Push Notifications settings within the Azure Portal.":::

> [!NOTE]
> If the Azure Notification Hub connection string is updated the Communication Services resource has to be updated as well.  
Any change on how the hub is linked will be reflected in data plane (i.e., when sending a notification) within a maximum period of ``10`` minutes. This is applicable also when the hub is linked for the first time **if** there were notifications sent before.

#### Device registration 

Refer to the [voice calling quickstart](../quickstarts/voice-video-calling/getting-started-with-calling.md) to learn how to register your device handle with Communication Services.

## Next steps

* For an introduction to Azure Event Grid, see [What is Event Grid?](../../event-grid/overview.md)
* To learn more on the Azure Notification Hub concepts, see [Azure Notification Hubs documentation](../../notification-hubs/index.yml)