---
title: Select a page contract - Azure Active Directory B2C | Microsoft Docs
description: Learn about how to select a page contract in Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg

ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: marsma
ms.subservice: B2C
---

# Select a page contract in Azure Active Directory B2C using custom policies

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

You can enable JavaScript client-side code in your Azure Active Directory (Azure AD) B2C policies, whether you’re using user flows or custom policies. To enable JavaScript for your applications, you must add an element to your [custom policy](active-directory-b2c-overview-custom.md), select a page contract, and use [b2clogin.com](b2clogin.md) in your requests. A page contract is an association of elements that Azure AD B2C provides and the content that you provide. This article discusses how to select a page contract in Azure AD B2C by configuring it in a custom policy.

> [!NOTE]
> If you want to enable JavaScript for user flows, see [JavaScript and page contract versions in Azure Active Directory B2C](user-flow-javascript-overview.md).

## Replace DataUri values

In your custom policies, you may have [ContentDefinitions](contentdefinitions.md) that define the HTML templates used in the user journey. The **ContentDefinition** contains a **DataUri** that refers to the page elements provided by Azure AD B2C. The **LoadUri** is the relative path to the HTML and CSS content that you provide.

```XML
<ContentDefinition Id="api.idpselections">
  <LoadUri>~/tenant/default/idpSelector.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.0.0</DataUri>
  <Metadata>
    <Item Key="DisplayName">Idp selection page</Item>
    <Item Key="language.intro">Sign in</Item>
  </Metadata>
</ContentDefinition>
```

To select a page contract, you change the **DataUri** values in your [ContentDefinitions](contentdefinitions.md) in your policies. By switching from the old **DataUri** values to the new values, you're selecting an immutable package. The benefit of using this package is that you’ll know it won't change and cause unexpected behavior on your page. 

To set up a page contract, use the following table to find **DataUri** values. 

| Old DataUri value | New DataUri value |
| ----------------- | ----------------- |
| urn:com:microsoft:aad:b2c:elements:idpselection:1.0.0 | urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.0.0 |
| urn:com:microsoft:aad:b2c:elements:unifiedssd:1.0.0 | urn:com:microsoft:aad:b2c:elements:contract:unifiedssd:1.0.0 | 
| urn:com:microsoft:aad:b2c:elements:claimsconsent:1.0.0 | urn:com:microsoft:aad:b2c:elements:contract:claimsconsent:1.0.0 |
| urn:com:microsoft:aad:b2c:elements:multifactor:1.0.0 | urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.0.0 |
| urn:com:microsoft:aad:b2c:elements:multifactor:1.1.0 | urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.1.0 |
| urn:com:microsoft:aad:b2c:elements:selfasserted:1.0.0 | urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.0.0 |
| urn:com:microsoft:aad:b2c:elements:selfasserted:1.1.0 | urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.1.0 | 
| urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0 | urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.0.0 |
| urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0 | urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.1.0 |
| urn:com:microsoft:aad:b2c:elements:globalexception:1.0.0 | urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.0.0 |
| urn:com:microsoft:aad:b2c:elements:globalexception:1.1.0 | urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.1.0 |

## Next steps

Find more information about how you can customize the user interface of your applications in [Customize the user interface of your application using a custom policy in Azure Active Directory B2C](active-directory-b2c-ui-customization-custom.md).



