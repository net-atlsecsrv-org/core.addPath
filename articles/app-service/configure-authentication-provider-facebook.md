---
title: Configure Facebook authentication - Azure App Service
description: Learn how to configure Facebook authentication for your App Services application.
services: app-service
documentationcenter: ''
author: mattchenderson
manager: syntaxc4
editor: ''

ms.assetid: b6b4f062-fcb4-47b3-b75a-ec4cb51a62fd
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 04/19/2018
ms.author: mahender
ms.custom: seodec18

---
# How to configure your App Service application to use Facebook login
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

This topic shows you how to configure Azure App Service to use Facebook as an authentication provider.

To complete the procedure in this topic, you must have a Facebook account that has a verified email address and a mobile phone number. To create a new Facebook account, go to [facebook.com].

## <a name="register"> </a>Register your application with Facebook
1. Log on to the [Azure portal], and navigate to your application. Copy your **URL**. You will use this to configure your Facebook app.
2. In another browser window, navigate to the [Facebook Developers] website and sign-in with your Facebook account credentials.
3. (Optional) If you have not already registered, click **Apps** > **Register as a Developer**, then accept the policy and follow the registration steps.
4. Click **My Apps** > **Add a New App**.
5. In **Display Name**, type a unique name for your app. Also provide your **Contact Email**, and then click **Create App ID** and complete the security check. This takes you to the developer dashboard for your new Facebook app.
6. Under **Facebook Login**, click **Set up**, and then choose **Settings** in the left-hand navigation under **Facebook Login**.
7. Add your application's **Redirect URI** to **Valid OAuth redirect URIs**, then click **Save Changes**.
   
   > [!NOTE]
   > Your redirect URI is the URL of your application appended with the path, */.auth/login/facebook/callback*. For example, `https://contoso.azurewebsites.net/.auth/login/facebook/callback`. Make sure that you are using the HTTPS scheme.
   > 
   > 
8. In the left-hand navigation, click **Settings** > **Basic**. On the **App Secret** field, click **Show**, provide your password if requested, then make a note of the values of **App ID** and **App Secret**. You use these later to configure your application in Azure.
   
   > [!IMPORTANT]
   > The app secret is an important security credential. Do not share this secret with anyone or distribute it within a client application.
   > 
   > 
9. The Facebook account which was used to register the application is an administrator of the app. At this point, only administrators can sign into this application. To authenticate other Facebook accounts, click **App Review** and enable **Make \<your-app-name> public** to enable general public access using Facebook authentication.

## <a name="secrets"> </a>Add Facebook information to your application
1. Back in the [Azure portal], navigate to your application. Click **Settings** > **Authentication / Authorization**, and make sure that **App Service Authentication** is **On**.
2. Click **Facebook**, paste in the App ID and App Secret values which you obtained previously, optionally enable any scopes needed by your application, then click **OK**.
   
    ![][0]
   
    By default, App Service provides authentication but does not restrict authorized access to your site content and APIs. You must authorize users in your app code.
3. (Optional) To restrict access to your site to only users authenticated by Facebook, set **Action to take when request is not authenticated** to **Facebook**. This requires that all requests be authenticated, and all unauthenticated requests are redirected to Facebook for authentication.
4. When done configuring authentication, click **Save**.

You are now ready to use Facebook for authentication in your app.

## <a name="related-content"> </a>Related Content
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->
[0]: ./media/app-service-mobile-how-to-configure-facebook-authentication/mobile-app-facebook-settings.png

<!-- URLs. -->
[Facebook Developers]: https://go.microsoft.com/fwlink/p/?LinkId=268286
[facebook.com]: https://go.microsoft.com/fwlink/p/?LinkId=268285
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-dotnet/
[Azure portal]: https://portal.azure.com/
