---
 title: include file
 description: include file
 services: digital-twins
 ms.author: alinast
 author: alinamstanciu
 manager: bertvanhoof
 ms.service: digital-twins
 ms.topic: include
 ms.date: 11/11/2019
 ms.custom: include file
---

>[!NOTE]
>This section provides instructions for [Azure AD app registration](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app).

1. In the [Azure portal](https://portal.azure.com), open **Azure Active Directory** from the expandable left menu, and then open the **App registrations** pane. 

    [![Select the Azure Active Directory pane](./media/digital-twins-permissions/select-aad-pane.png)](./media/digital-twins-permissions/select-aad-pane.png#lightbox)

1. Select the **+ New registration** button.

    [![Select the New registration button](./media/digital-twins-permissions/aad-app-register.png)](./media/digital-twins-permissions/aad-app-register.png#lightbox)

1. Give a friendly name for this app registration in the **Name** box. Under the **Redirect URI (optional)** section, choose **Public client/native (mobile & desktop)** in the drop-down menu on the left, and enter `https://microsoft.com` in the textbox on the right. Select **Register**.

    [![Create pane](./media/digital-twins-permissions/aad-app-reg-create.png)](./media/digital-twins-permissions/aad-app-reg-create.png#lightbox)

1. To make sure that [the app is registered as a **public client**](https://docs.microsoft.com/azure/active-directory/develop/scenario-desktop-app-registration), open the **Authentication** pane for your app registration, and scroll down in that pane. In the **Default client type** section, choose **Yes** for **Treat application as a public client**, and hit **Save**.

    Check **Access tokens** to enable the **oauth2AllowImplicitFlow** setting in your Manifest.json.

    [![Public client configuration setting](./media/digital-twins-permissions/aad-public-client.png)](./media/digital-twins-permissions/aad-public-client.png#lightbox)

1.  Open the **Overview** pane of your registered app, and copy the values of the following entities to a temporary file. You'll use these values to configure your sample application in the following sections.

    - **Application (client) ID**
    - **Directory (tenant) ID**

    [![Azure Active Directory application ID](./media/digital-twins-permissions/aad-app-reg-app-id.png)](./media/digital-twins-permissions/aad-app-reg-app-id.png#lightbox)

1. Open the **API permissions** pane for your app registration. Select **+ Add a permission** button. In the **Request API permissions** pane, select the **APIs my organization uses** tab, and then search for one of the following:
    
    1. `Azure Digital Twins`. Select the **Azure Digital Twins** API.

        [![Search API or Azure Digital Twins](./media/digital-twins-permissions/aad-aap-search-api-dt.png)](./media/digital-twins-permissions/aad-aap-search-api-dt.png#lightbox)

    1. Alternatively, search for `Azure Smart Spaces Service`. Select the **Azure Smart Spaces Service** API.

        [![Search API for Azure Smart Spaces](./media/digital-twins-permissions/aad-app-search-api.png)](./media/digital-twins-permissions/aad-app-search-api.png#lightbox)

    > [!IMPORTANT]
    > The Azure AD API name and ID that will appear depends on your tenant:
    > * Test tenant and customer accounts should search for `Azure Digital Twins`.
    > * Other Microsoft accounts should search for `Azure Smart Spaces Service`.

1. Either API once selected shows up as **Azure Digital Twins** in the same **Request API permissions** pane. Select the **Read** drop-down option, and then select the **Read.Write** checkbox. Select the **Add permissions** button.

    [![Add API permissions](./media/digital-twins-permissions/aad-app-req-permissions.png)](./media/digital-twins-permissions/aad-app-req-permissions.png#lightbox)

1. Depending on your organization's settings, you might need to take additional steps to grant admin access to this API. Contact your administrator for more information. Once the admin access is approved, the **Admin Consent Required** column in the **API permissions** pane will display your permissions. 

    [![Admin consent approval](./media/digital-twins-permissions/aad-app-admin-consent.png)](./media/digital-twins-permissions/aad-app-admin-consent.png#lightbox)

    Verify that **Azure Digital Twins** appears.
