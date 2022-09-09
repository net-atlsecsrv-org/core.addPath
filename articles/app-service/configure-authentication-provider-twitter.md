---
title: Configure Twitter authentication - Azure App Service
description: Learn how to configure Twitter authentication for your App Service app.
services: app-service
documentationcenter: ''
author: mattchenderson
manager: syntaxc4
editor: ''

ms.assetid: c6dc91d7-30f6-448c-9f2d-8e91104cde73
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 04/19/2018
ms.author: mahender
ms.custom: seodec18

---

# Configure your App Service app to use Twitter login

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

This article shows how to configure Azure App Service to use Twitter as an authentication provider.

To complete the procedure in this article, you need a Twitter account that has a verified email address and phone number. To create a new Twitter account, go to [twitter.com].

## <a name="register"> </a>Register your application with Twitter

1. Sign in to the [Azure portal] and go to your application. Copy your **URL**. You'll use it to configure your Twitter app.
1. Go to the [Twitter Developers] website, sign in with your Twitter account credentials, and select **Create New App**.
1. Enter a **Name** and a **Description** for your new app. Paste your application's **URL** into the **Website** field. In the **Callback URL** field, enter the URL of your App Service app and append the path `/.auth/login/aad/callback`. For example, `https://contoso.azurewebsites.net/.auth/login/twitter/callback`. Make sure to use  the HTTPS scheme.
1. At the bottom of the page, read and accept the terms. Select **Create your Twitter application**. The application details are displayed.
1. Select the **Settings** tab, check **Allow this application to be used to sign in with Twitter**, and then select **Update Settings**.
1. Select the **Keys and Access Tokens** tab.

   Make a note of these values:
   - Consumer key (API key)
   - Consumer secret (API secret)

   > [!NOTE]
   > The consumer secret is an important security credential. Do not share this secret with anyone or distribute it with your app.

## <a name="secrets"> </a>Add Twitter information to your application

1. Go to your application in the [Azure portal].
1. Select **Settings** > **Authentication / Authorization**, and make sure that **App Service Authentication** is **On**.
1. Select **Twitter**.
1. Paste in the `API Key` and `API Secret` values that you obtained previously.
1. Select **OK**.

   ![Screenshot of Mobile App Twitter settings][1]

   By default, App Service provides authentication but doesn't restrict authorized access to your site content and APIs. You must authorize users in your app code.

1. (Optional) To restrict access to your site to only users authenticated by Twitter, set **Action to take when request is not authenticated** to **Twitter**. When you set this functionality, your app requires all requests to be authenticated. It also redirects all unauthenticated requests to Twitter for authentication.

   > [!CAUTION]
   > Restricting access in this way applies to all calls to your app, which might not be desirable for apps that have a publicly available home page, as in many single-page applications. For such applications, **Allow anonymous requests (no action)** might be preferred so that the app manually starts authentication itself. For more information, see [Authentication flow](overview-authentication-authorization.md#authentication-flow).

1. Select **Save**.

You are now ready to use Twitter for authentication in your app.

## <a name="related-content"> </a>Next steps

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-twitter-authentication/app-service-twitter-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-twitter-authentication/mobile-app-twitter-settings.png

<!-- URLs. -->

[Twitter Developers]: https://go.microsoft.com/fwlink/p/?LinkId=268300
[twitter.com]: https://go.microsoft.com/fwlink/p/?LinkID=268287
[Azure portal]: https://portal.azure.com/
[xamarin]: ../app-services-mobile-app-xamarin-ios-get-started-users.md
