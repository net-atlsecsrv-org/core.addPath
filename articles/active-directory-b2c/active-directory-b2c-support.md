---
title: Support for Azure Active Directory B2C | Microsoft Docs
description: How to file support requests for Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg

ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/06/2016
ms.author: davidmu
ms.subservice: B2C
---

# Azure Active Directory B2C: File Support Requests
You can file support requests for Azure Active Directory (Azure AD) B2C on the Azure portal using the following steps:

1. Switch from your B2C tenant to another tenant that has an Azure subscription associated with it. Typically, the latter is your employee tenant or the default tenant created for you when you signed up for an Azure subscription. To learn more, see [how an Azure subscription is related to Azure AD](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).
   
    ![Support - Switch tenants](./media/active-directory-b2c-support/support-switch-dir.png)

3. After switching tenants, click **Help + support**.
   
    ![Support - Help + Support](./media/active-directory-b2c-support/support-support.png)
    
4. Click **New support request**.
   
    ![Support - New](./media/active-directory-b2c-support/support-new.png)
5. In the **Basics** blade, use these details and click **Next**.
   
   * **Issue type** is **Technical**.
   * Choose the appropriate **Subscription**.
   * **Service** is **Active Directory**.
   * Choose the appropriate **Support plan**. If you don't have one, you can sign up for one [here](https://azure.microsoft.com/support/plans/).
     
     ![Support - Basics](./media/active-directory-b2c-support/support-basics.png)
6. In the **Problem** blade, use these details and click **Next**.
   
   * Choose the appropriate **Severity** level.
   * **Problem type** is **B2C**.
   * Choose the appropriate **Category**.
   * Describe your issue in the **Details** field. Provide details such as the B2C tenant name, description of the problem, error messages, correlation IDs (if available), and so on.
   * In the **Time frame** field, provide the date and time (including time zone) that the issue occurred.
   * Under **File upload**, upload all screenshots and files that you think would assist in resolving the issue.
     
     ![Support - Problem](./media/active-directory-b2c-support/support-problem.png)
7. In the **Contact information** blade, add your contact information. Click **Create**.
   
    ![Support - Contact](./media/active-directory-b2c-support/support-contact.png)
8. After submitting your support request, you can monitor it by clicking **Help + support** on the Startboard, and then **Manage support requests**.

## Known issue: Filing a support request in the context of a B2C tenant
If you missed step 2 outlined above and try to create a support request in the context of your B2C tenant, you will see the following error.

> [!IMPORTANT]
> Don't attempt to sign up for a new Azure subscription in your B2C tenant.  
> 
> 

![Support - No subscription](./media/active-directory-b2c-support/support-no-sub.png)

