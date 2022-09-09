---
title: Configure Microsoft Account authentication - Azure App Service
description: Learn how to configure Microsoft Account authentication for your App Services application.
author: mattchenderson
services: app-service
documentationcenter: ''
manager: syntaxc4
editor: ''

ms.assetid: ffbc6064-edf6-474d-971c-695598fd08bf
ms.service: app-service
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 04/19/2018
ms.author: mahender
ms.custom: seodec18

---
# How to configure your App Service application to use Microsoft Account login
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

This topic shows you how to configure Azure App Service to use Microsoft Account as an authentication provider. 

## <a name="register-microsoft-account"> </a>Register your app with Microsoft Account
1. Log on to the [Azure portal], and navigate to your application. Copy your **URL**, which later you use to configure your app with Microsoft Account.
2. Navigate to the [My Applications] page in the Microsoft Account Developer Center, and log on with your Microsoft account, if required.
3. Click **Add an app**, then type an application name, and click **Create**.
4. Make a note of the **Application ID**, as you will need it later. 
5. Under "Platforms," click **Add Platform** and select "Web".
6. Under "Redirect URIs" supply the endpoint for your application, then click **Save**. 
   
   > [!NOTE]
   > Your redirect URI is the URL of your application appended with the path, */.auth/login/microsoftaccount/callback*. For example, `https://contoso.azurewebsites.net/.auth/login/microsoftaccount/callback`.   
   > Make sure that you are using the HTTPS scheme.
   
7. Under "Application Secrets," click **Generate New Password**. Make note of the value that appears. Once you leave the page, it will not be displayed again.

    > [!IMPORTANT]
    > The password is an important security credential. Do not share the password with anyone or distribute it within a client application.
    
8. Click **Save**

## <a name="secrets"> </a>Add Microsoft Account information to your App Service application
1. Back in the [Azure portal], navigate to your application, click **Settings** > **Authentication / Authorization**.
2. If the Authentication / Authorization feature is not enabled, switch it **On**.
3. Click **Microsoft Account**. Paste in the Application ID and Password values which you obtained previously, and optionally enable any scopes your application requires. Then click **OK**.
   
    ![][1]
   
    By default, App Service provides authentication but does not restrict authorized access to your site content and APIs. You must authorize users in your app code.
4. (Optional) To restrict access to your site to only users authenticated by Microsoft account, set **Action to take when request is not authenticated** to **Microsoft Account**. This requires that all requests be authenticated, and all unauthenticated requests are redirected to Microsoft account for authentication.
5. Click **Save**.

You are now ready to use Microsoft Account for authentication in your app.

## <a name="related-content"> </a>Related content
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-microsoft-authentication/app-service-microsoftaccount-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-microsoft-authentication/mobile-app-microsoftaccount-settings.png

<!-- URLs. -->

[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Azure portal]: https://portal.azure.com/
