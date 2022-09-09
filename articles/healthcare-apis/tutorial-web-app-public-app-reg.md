---
title: Web App Tutorial - Client Application Setup
description: This tutorial walks through the steps of registering a public application for getting ready to deploy a web application
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: mihansen
ms.author: cavoeg
author: caitlinv39
ms.date: 01/03/2020
---

# Client application registration
In the previous tutorial, you deployed and set up your Azure API for FHIR. Now that you have your Azure API for FHIR setup, we will register a public client application. You can read through the full [register a public client app](register-public-azure-ad-client-app.md) how-to guide for more details or troubleshooting, but we have called out the major steps for this tutorial below.

1. Navigate to Azure Active Directory
1. Select **App Registration** --> **New Registration**
1. Name your application and set up the redirect URI to https://www.getpostman.com/oauth2/callback

   :::image type="content" source="media/tutorial-web-app/register-public-app.png" alt-text="Screenshot of the Register an application pane, and an example application name and redirect URL.":::

## Client application settings

Once your client application is registered, copy the Application (client) ID and the Tenant ID from the Overview Page. You will need these two values later when accessing the client.

:::image type="content" source="media/tutorial-web-app/client-id-tenant-id.png" alt-text="Screenshot of the client application settings pane, with the application and directory IDs highlighted.":::

### Connect with web app

If you have [written your web app](tutorial-web-app-write-web-app.md) to connect with the Azure API for FHIR, you also need to set the correct authentication options. 

1. In the left menu, under **Manage**, select **Authentication**. 

1. To add a new platform configuration, select **Web**.

1. Set up the redirect URI in preparation for when you create your web application in the fourth part of this tutorial. To do this, add `https://\<WEB-APP-NAME>.azurewebsites.net` to the redirect URI list. If you choose a different name during the step where you [write your web app](tutorial-web-app-write-web-app.md), you will need to come back and update this.

1. Select the **Access Token** and **ID token** check boxes.

   :::image type="content" source="media/tutorial-web-app/web-app-authentication.png" alt-text="Screenshot of the app Authentication settings blade, with the steps to add a platform highlighted.":::

## Add API permissions

Now that you have setup the correct authentication, set the API permissions:

1. Select **API permissions** and click **Add a permission**.
1. Under **APIs my organization uses**, search for Azure Healthcare APIs.
1. Select **user_impersonation** and click **add permissions**.

:::image type="content" source="media/tutorial-web-app/api-permissions.png" alt-text="Screenshot of the Add API permissions blade, with the steps to add API permissions highlighted.":::

## Next Steps
You now have a public client application. In the next tutorial, we will walk through testing and gaining access to this application through Postman.

>[!div class="nextstepaction"]
>[Test client application in Postman](tutorial-web-app-test-postman.md)
