---
 title: include file
 description: include file
 services: notification-hubs
 author: spelluru
 ms.service: notification-hubs
 ms.topic: include
 ms.date: 03/11/2019
 ms.author: spelluru
 ms.custom: include file
---

1. Sign in to the [Firebase console](https://firebase.google.com/console/). Create a new Firebase project if you don't already have one.
2. After you create your project, select **Add Firebase to your Android app**. 

    ![Add Firebase to your Android app](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)
3. On the **Add Firebase to your Android app** page, do the following steps: 
    1. For **Android package name**, enter a name for your package. For example: `tutorials.tutoria1.xamarinfcmapp`. 

        ![Specify the package name](./media/notification-hubs-enable-firebase-cloud-messaging/specify-package-name-fcm-settings.png)
    2. Select **Register app**. 
4. Select **Download google-services.json**, save the file into the **app** folder of your project, and then select **Next**. 

    ![Download google-services.json](./media/notification-hubs-enable-firebase-cloud-messaging/download-google-service-button.png)
6. Select **Next** on the page. 
7. Select **Skip this step** on the page. 

    ![Skip the last step](./media/notification-hubs-enable-firebase-cloud-messaging/skip-this-step.png)
8. In the Firebase console, select the cog for your project. Then select **Project Settings**.

    ![Select Project Settings](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)
4. If you haven't downloaded the **google-services.json** file, you can do so on this page. 
5. Switch to the **Cloud Messaging** tab at the top. 
6. Copy and save the **Legacy Server key** for later use. You use this value to configure your notification hub.
