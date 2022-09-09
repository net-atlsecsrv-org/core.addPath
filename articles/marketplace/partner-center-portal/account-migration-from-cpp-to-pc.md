---
title: Account migration from Cloud Partner Portal to Partner Center - Commercial Marketplace for Azure
description: How to migrate your account from CPP to Partner Center. - Commercial Marketplace for Azure
author: qianw211
manager: evansma
ms.author: v-qiwe
ms.service: marketplace 
ms.topic: conceptual
ms.date: 08/15/2019
---

# Account migration from Cloud Partner Portal to Partner Center

When your offers are migrated over from the Cloud Partner Portal (CPP) to Partner Center (PC), they get locked for editing in CPP. At this point, your account settings need to be migrated over to Partner Center.  Both your account settings, as well as your offers, can be managed in Partner Center.

## Account migration process

When offers are migrated over from the CPP, your account is configured for migration. 
 
If you are a user with the Owner role in CPP for a given account, a yellow banner is shown on your Publisher Profile page.  You are asked to move your account settings over to the Partner Center. 

Click on the banner to initiate your account migration process. You are expected to enter the following items:

### **Work e-mail address**

In most cases, sign in with the e-mail address that you use to sign into CPP. In certain cases, a different e-mail address must be used:

* Microsoft Account: If the CPP account is a Microsoft Account, then you need to enter a valid work e-mail address associated with the tenant, for whom the MPN ID is registered. 

* Tenant mismatch: If your work e-mail address does not belong to the tenant that is associated with the Microsoft Partner Network ID present on your CPP account, then you’ll see an error. To move past this error, enter an e-mail address associated with the tenant. An error message will provide the name of the tenant. 

### Sign up for Microsoft Partner Network program

In the event that your CPP account does not have a Microsoft Partner Network ID, or has one that is invalid, you will have to sign up for the Microsoft Partner Network program as part of the activation process.

## Account activation is complete

The account migration needs to happen only once for a given account. Once a given partner has completed the migration for the account, all Owners will see this behavior on their Publisher Profile page:

1. You are going to see the Partner Settings page in Microsoft Partner Network, where you can manage the Microsoft Partner’s account settings. 
2. Once the account migration is complete, a yellow banner on your Publisher Profile page pops up to users belonging to the Owner role in CPP for a given account, asking them to manage their account settings in Partner Center. 
3. The account settings page in CPP is then converted to read-only mode. 

## Move Dynamics 365-based solutions to Partner Center

If you have Dynamics 365 for Customer Engagement or Dynamics 365 for Finance and Operations solutions in the One Commercial Partner GTM portal, **follow these instructions by August 31, 2019** to move these solutions to Partner Center.

> [!NOTE]
> If your account was originally created in Partner Membership Center (PMC), sign in to [Partner Center](https://partner.microsoft.com/pcv/accountsettings/connectedpartnerprofile) to confirm that your account has been migrated before completing the steps below. If you see a profile screen with your MPN ID, you're ready to proceed. If not, you must start your account migration by following the prompts in the [Partner Membership Center](https://partners.microsoft.com/partnerprogram/Welcome.aspx). If you need help with this, visit [support](https://partner.microsoft.com/support?issueid=100-0077).

1. Go to the [Commercial Marketplace overview page in Partner Center](https://partner.microsoft.com/dashboard/commercial-marketplace/overview). If you see "Commercial Marketplace" in the left navigation pane, you are enrolled and should proceed to the next step. If not, [enroll in the commercial marketplace](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/azureisv) now.
2. Confirm your offers are in AppSource by [searching for your offers](https://appsource.microsoft.com/). If your offers are already in AppSource, proceed to the next step. For any offer not in AppSource, create a [new Dynamics 365 for Customer Engagement offer](create-new-customer-engagement-offer.md) or a [new Dynamics 365 for Operations offer](create-new-operations-offer.md).
3. Verify your enrollment in the Business Applications ISV Connect Program:
  
   * On the [Agreements](https://partner.microsoft.com/dashboard/account/agreements) page in Partner Center, make sure you’ve accepted the **Business Applications ISV Addendum** to enroll in the program.
   * On the [Account settings](https://partner.microsoft.com/dashboard/account/v3/accountsettings/billingprofile) page, provide your billing information.

4. Submit each new and existing offer for certification, even if your offers were previously certified. **We recommend submitting as soon as possible to allow enough time for approval before August 31, 2019.**
5. Go to the [One Commercial Partner GTM portal](https://msgtm.azurewebsites.net/en-US/Profile/SignIn) and add your AppSource listing URL in the Marketplace Links section. If you need help with this step, email us at cosell@microsoft.com.

## Next steps

- [Manage your Commercial Marketplace account in Partner Center](./manage-account.md) 
