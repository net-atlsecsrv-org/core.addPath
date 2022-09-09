---
 title: include file
 description: include file
 services: digital-twins
 ms.author: alinast
 author: alinamstanciu
 manager: bertvanhoof
 ms.service: digital-twins
 ms.topic: include
 ms.date: 09/30/2019
 ms.custom: include file
---

>[!NOTE]
>This section provides instructions for the [new Azure AD app registration](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app). If you still have legacy native app registration, you may use it as long as it's supported. Additionally, if for some reason the new way of app registation is not working in your setup, you may try to create a legacy native AAD app. Read [Register your Azure Digital Twins app with Azure Active Directory legacy](../articles/digital-twins/how-to-use-legacy-aad.md) for more instructions. 

1. In the [Azure portal](https://portal.azure.com), open **Azure Active Directory** from the left pane, and then open the **App registrations** pane. Select the **New registration** button.

    [![App registered](./media/digital-twins-permissions/aad-app-register.png)](./media/digital-twins-permissions/aad-app-register.png#lightbox)

1. Give a friendly name for this app registration in the **Name** box. Under the **Redirect URI (optional)** section, choose **Public client (mobile & desktop)** in the drop-down on the left, and enter `https://microsoft.com` in the textbox on the right. Select **Register**.

    [![Create pane](./media/digital-twins-permissions/aad-app-reg-create.png)](./media/digital-twins-permissions/aad-app-reg-create.png#lightbox)

1. To make sure that [the app is registered as a *native app*](https://docs.microsoft.com/azure/active-directory/develop/scenario-desktop-app-registration), open the **Authentication** pane for your app registration, and scroll down in that pane. In the **Default client type** section, choose **Yes** for **Treat application as a public client**. 

    [![Default native](./media/digital-twins-permissions/aad-app-default-native.png)](./media/digital-twins-permissions/aad-app-default-native.png#lightbox)

1.  Open the **Overview** pane of your registered app, and copy the values of following entities to a temporary file. You'll use these values to configure your sample application in the following sections.

    - **Application (client) ID**
    - **Directory (tenant) ID**

    [![Azure Active Directory application ID](./media/digital-twins-permissions/aad-app-reg-app-id.png)](./media/digital-twins-permissions/aad-app-reg-app-id.png#lightbox)

1. Open the **API permissions** pane for your app registration. Select **Add a permission** button. In the **Request API permissions** pane, select the **APIs my organization uses** tab, and then search for:
    
    1. `Azure Digital Twins`. Select the **Azure Digital Twins** API.

        [![Search API or Azure Digital Twins](./media/digital-twins-permissions/aad-aap-search-api-dt.png)](./media/digital-twins-permissions/aad-aap-search-api-dt.png#lightbox)

    1. Alternatively, search for `Azure Smart Spaces Service`. Select the **Azure Smart Spaces Service** API.

        [![Search API for Azure Smart Spaces](./media/digital-twins-permissions/aad-app-search-api.png)](./media/digital-twins-permissions/aad-app-search-api.png#lightbox)

    > [!IMPORTANT]
    > The Azure AD API name and ID that will appear depends on your tenant:
    > * Test tenant and customer accounts should search for `Azure Digital Twins`.
    > * Other Microsoft accounts should search for `Azure Smart Spaces Service`.

1. The selected API shows up as **Azure Digital Twins** in the same **Request API permissions** pane. Select the **Read (1)** drop down, and then select **Read.Write** checkbox. Select the **Add permissions** button.

    [![Add API permissions](./media/digital-twins-permissions/aad-app-req-permissions.png)](./media/digital-twins-permissions/aad-app-req-permissions.png#lightbox)

1. Depending on your organization's settings, you might need to take additional steps to grant admin access to this API. Contact your administrator for more information. Once the admin access is approved, the **ADMIN CONSENT REQUIRED** column in the **API permissions** pane will show similar to the following for your APIs:

    [![Add API permissions](./media/digital-twins-permissions/aad-app-admin-consent.png)](./media/digital-twins-permissions/aad-app-admin-consent.png#lightbox)

