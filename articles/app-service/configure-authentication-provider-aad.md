---
title: Configure Azure Active Directory authentication - Azure App Service
description: Learn how to configure Azure Active Directory authentication for your App Services application.
author: mattchenderson
services: app-service
documentationcenter: ''
manager: syntaxc4
editor: ''

ms.assetid: 6ec6a46c-bce4-47aa-b8a3-e133baef22eb
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 02/20/2019
ms.author: mahender
ms.custom: seodec18

---
# Configure your App Service app to use Azure Active Directory sign-in

> [!NOTE]
> At this time, AAD V2 (including MSAL) is not supported for Azure App Services and Azure Functions. Please check back for updates.
>

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

This article shows you how to configure Azure App Services to use Azure Active Directory as an authentication provider.

## <a name="express"> </a>Configure with express settings

1. In the [Azure portal], navigate to your App Service app. In the left navigation, select **Authentication / Authorization**.
2. If **Authentication / Authorization** is not enabled, select **On**.
3. Select **Azure Active Directory**, and then select **Express** under **Management Mode**.
4. Select **OK** to register the App Service app in Azure Active Directory. This creates a new app registration. If you want to choose an existing
   app registration instead, click **Select an existing app** and then search for the name of a previously created app registration within your tenant.
   Click the app registration to select it and click **OK**. Then click **OK** on the Azure Active Directory settings page.
   By default, App Service provides authentication but does not restrict authorized access to your site content and APIs. You must authorize users in your app code.
5. (Optional) To restrict access to your site to only users authenticated by Azure Active Directory, set **Action to take when request is not authenticated** to **Log in with Azure Active Directory**. This requires that all requests be authenticated, and all unauthenticated requests are redirected to Azure Active Directory for authentication.

> [!CAUTION]
> Restricting access in this way applies to all calls to your app, which may not be desirable for apps wanting a publicly available home page, as in many single-page applications. For such applications, **Allow anonymous requests (no action)** may be preferred, with the app manually starting login itself, as described [here](overview-authentication-authorization.md#authentication-flow).

6. Click **Save**.

## <a name="advanced"> </a>Configure with advanced settings

You can also provide configuration settings manually. This is the preferred solution if the Azure Active Directory tenant you wish to use is different from the tenant with which you sign into Azure. To complete the configuration, you must first create a registration in Azure Active Directory, and then you must provide some of the registration details to App Service.

### <a name="register"> </a>Register your App Service app with Azure Active Directory

1. Sign in to the [Azure portal], and navigate to your App Service app. Copy your app **URL**. You will use this to configure your Azure Active Directory app registration.
2. Navigate to **Active Directory**, then select the **App registrations**, then click **New application registration** at the top to start a new app registration. 
3. In the **Create** page, enter a **Name** for your app registration, select the  **Web App / API** type, in the **Sign-on URL** box paste the application URL (from step 1). Then click to **Create**.
4. In a few seconds, you should see the new app registration you just created.
5. Once the app registration has been added, click on the app registration name, click on **Settings** at the top, then click on **Properties** 
6. In the **App ID URI** box, paste in the Application URL (from step 1), also in the **Home Page URL** paste in the Application URL (from step 1) as well, then click **Save**
7. Now click on the **Reply URLs**, edit the **Reply URL**, paste in the Application URL (from step 1), then append it to the end of the URL, */.auth/login/aad/callback* (For example, `https://contoso.azurewebsites.net/.auth/login/aad/callback`). Click **Save**.

   > [!NOTE]
   > You can use the same app registration for multiple domains by adding additional **Reply URLs**. Make sure to model each App Service instance with its own registration, so it has its own permissions and consent. Also consider using separate app registrations for separate site slots. This is to avoid permissions being shared between environments, so that a bug in new code you are testing does not affect production.
    
8. At this point, copy the **Application ID** for the app. Keep it for later use. You will need it to configure your App Service app.
9. Close the **Registered app** page. On the **App registrations** page, click on the **Endpoints** button at the top, then copy the **WS-FEDERATION SIGN-ON ENDPOINT** URL but remove the `/wsfed` ending from the URL. The end result should look like `https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000`. The domain name may be different for a sovereign cloud. This will serve as the Issuer URL for later.

### <a name="secrets"> </a>Add Azure Active Directory information to your App Service app

1. Back in the [Azure portal], navigate to your App Service app. Click **Authentication/Authorization**. If the Authentication/Authorization feature is not enabled, turn the switch to **On**. Click on **Azure Active Directory**, under Authentication Providers, to configure your app.

    (Optional) By default, App Service provides authentication but does not restrict authorized access to your site content and APIs. You must authorize users in your app code. Set **Action to take when request is not authenticated** to **Log in with Azure Active Directory**. This option requires that all requests be authenticated, and all unauthenticated requests are redirected to Azure Active Directory for authentication.
2. In the Active Directory Authentication configuration, click **Advanced** under **Management Mode**. Paste the Application ID into the Client ID box (from step 8) and paste in the URL (from step 9) into the Issuer URL value. Then click **OK**.
3. On the Active Directory Authentication configuration page, click **Save**.

You are now ready to use Azure Active Directory for authentication in your App Service app.

## Configure a native client application
You can register native clients, which provides greater control over permissions mapping. You need this if you wish to perform sign-ins using a client library such as the **Active Directory Authentication Library**.

1. Navigate to **Azure Active Directory** in the [Azure portal].
2. In the left navigation, select **App registrations**. Click **New app registration** at the top.
4. In the **Create** page, enter a **Name** for your app registration. Select **Native** in **Application type**.
5. In the **Redirect URI** box, enter your site's */.auth/login/done* endpoint, using the HTTPS scheme. This value should be similar to *https://contoso.azurewebsites.net/.auth/login/done*. If creating a Windows application, instead use the [package SID](../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#package-sid) as the URI.
5. Click **Create**.
6. Once the app registration has been added, select it to open it. Find the **Application ID** and make a note of this value.
7. Click **All settings** > **Required permissions** > **Add** > **Select an API**.
8. Type the name of the App Service app that you registered earlier to search for it, then select it and click **Select**.
9. Select **Access \<app_name>**. Then click **Select**. Then click **Done**.

You have now configured a native client application that can access your App Service app.

## <a name="related-content"> </a>Related Content

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-webapp-url.png
[1]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app_registration.png
[2]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app-registration-create.png
[3]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app-registration-config-appidurl.png
[4]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app-registration-config-replyurl.png
[5]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-endpoints.png
[6]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-endpoints-fedmetadataxml.png
[7]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-webapp-auth.png
[8]: ./media/configure-authentication-provider-aad/app-service-webapp-auth-config.png



<!-- URLs. -->

[Azure portal]: https://portal.azure.com/
[alternative method]:#advanced
