---
title: Define custom attributes in Azure Active Directory B2C | Microsoft Docs
description: Define custom attributes for your application in Azure Active Directory B2C to collect information about your customers.
services: active-directory-b2c
author: davidmu1
manager: celestedg

ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.subservice: B2C
---

# Define custom attributes in Azure Active Directory B2C

 Every customer-facing application has unique requirements for the information that needs to be collected. Your Azure Active Directory (Azure AD) B2C tenant comes with a built-in set of information stored in attributes, such as Given Name, Surname, City, and Postal Code. With Azure AD B2C, you can extend the set of attributes stored on each customer account. 
 
 You can create custom attributes in the [Azure portal](https://portal.azure.com/) and use them in your sign-up user flows, sign-up or sign-in user flows, or profile editing user flows. You can also read and write these attributes by using the [Azure AD Graph API](active-directory-b2c-devquickstarts-graph-dotnet.md). Custom attributes in Azure AD B2C use [Azure AD Graph API Directory Schema Extensions](/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-directory-schema-extensions).  

> [!NOTE]
> Support for newer [Microsoft Graph API](https://docs.microsoft.com/graph/overview?view=graph-rest-1.0) for querying Azure AD B2C tenant is still under development.
>

## Create a custom attribute

1. Sign in to the [Azure portal](https://portal.azure.com/) as the global administrator of your Azure AD B2C tenant.
2. Make sure you're using the directory that contains your Azure AD B2C tenant by switching to it in the top-right corner of the Azure portal. Select your subscription information, and then select **Switch Directory**. 

    ![Switch to your Azure AD B2C tenant](./media/active-directory-b2c-reference-custom-attr/switch-directories.png)

    Choose the directory that contains your tenant.

    ![Select directory](./media/active-directory-b2c-reference-custom-attr/select-directory.png)

3. Choose **All services** in the top-left corner of the Azure portal, search for and select **Azure AD B2C**.
4. Select **User attributes**, and then select **Add**.
5. Provide a **Name** for the custom attribute (for example, "ShoeSize")
6. Choose a **Data Type**. Only **String**, **Boolean**, and **Int** are available.
7. Optionally, enter a **Description** for informational purposes. 
8. Click **Create**.

The custom attribute is now available in the list of **User attributes** and for use in your user flows. A custom attribute is only created the first time it is used in any user flow, and not when you add it to the list of **User attributes**. 


## Use a custom attribute in your user flow

1. In your Azure AD B2C tenant, select **User flows**.
2. Select your policy (for example, "B2C_1_SignupSignin") to open it. 
4. Select **User attributes** and then select the custom attribute (for example, "ShoeSize"). Click **Save**.
5. Select **Application claims** and then select the custom attribute. 
6. Click **Save**.

Once you have created a new user using a user flow which uses the newly created custom attribute, the object can be queried in [Azure AD Graph Explorer](https://docs.microsoft.com/azure/active-directory/develop/active-directory-graph-api-quickstart). Alternatively you can use the [**Run user flow**](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-user-flows) feature on the user flow to verify the customer experience. You should now see **ShoeSize** in the list of attributes collected during the sign-up journey, and see it in the token sent back to your application. 

