---
title: Link your partner ID to track your impact on delegated resources
description: Learn how to associate your partner ID to receive partner earned credit (PEC) on customer resources you manage through Azure Lighthouse.
ms.date: 10/30/2020
ms.topic: how-to
---

# Link your partner ID to track your impact on delegated resources 

If you're a member of the [Microsoft Partner Network](https://partner.microsoft.com/), you can link your partner ID with the credentials used to manage delegated customer resources. Partner Admin Link (PAL) enables Microsoft to identify and recognize partners who drive Azure customer success. This link also allows [CSP (Cloud Solution Provider)](/partner-center/csp-overview) partners to receive [partner earned credit for managed services (PEC)](/partner-center/partner-earned-credit) for customers who have [signed the Microsoft Customer Agreement (MCA)](/partner-center/confirm-customer-agreement) and are [under the Azure plan](/partner-center/azure-plan-get-started).

If you [onboard customers with Managed Service offers in Azure Marketplace](publish-managed-services-offers.md), linking happens automatically using the MPN ID associated with the Partner Center account used to publish the offers. No further action is needed in order to track your impact for these customers.

If you [onboard customers by using Azure Resource Management templates](onboard-customer.md), you'll need to take action to create this link. This is done by [linking your MPN ID](../../cost-management-billing/manage/link-partner-id.md) with at least one user account in your managing tenant that has access to each of your onboarded subscriptions.

## Associate your partner ID when you onboard new customers

When onboarding customers through Azure Resource Manager templates (ARM templates), use the following process to link your partner ID (and enable partner earned credit, if applicable). You'll need to know your [MPN partner ID](/partner-center/partner-center-account-setup#locate-your-mpn-id) to complete these steps. Be sure to use the **Associated MPN ID** shown on your partner profile.

For simplicity, we recommend creating a service principal account in your tenant, linking it to your **Associated MPN ID**, then granting it access to every customer you onboard with an [Azure built-in role that is eligible for PEC](/partner-center/azure-roles-perms-pec).

1. [Create a service principal account](../../active-directory/develop/howto-authenticate-service-principal-powershell.md) in your managing tenant. For this example, we'll use the name *Provider Automation Account* for this service principal.
1. Using that service principal account, [link to your Associated MPN ID](../../cost-management-billing/manage/link-partner-id.md#link-to-a-partner-id) in your managing tenant. You only need to do this one time.
1. When you [onboard a customer using ARM templates](onboard-customer.md), be sure to include an authorization which includes the Provider Automation Account as a user with an [Azure built-in role that is eligible for PEC](/partner-center/azure-roles-perms-pec).

By following these steps, every customer tenant you manage will be associated with your partner ID. The Provider Automation Account does not need to authenticate or perform any actions in the customer tenant.

:::image type="content" source="../media/lighthouse-pal.jpg" alt-text="Diagram showing the PAL process with Azure Lighthouse.":::

## Add your partner ID to previously onboarded customers

If you have already onboarded a customer, you may not want to perform another deployment to add your Provider Automation Account service principal. Instead, you can link your **Associated MPN ID** with a user account which already has access to work in that customer's tenant. Be sure that the account has been granted an [Azure built-in role that is eligible for PEC](/partner-center/azure-roles-perms-pec).

Once the account has been [linked to your Associated MPN ID](../../cost-management-billing/manage/link-partner-id.md#link-to-a-partner-id) in your managing tenant, you will be able to track recognition for your impact on that customer.

## Confirm partner earned credit

You can [view PEC details in the Azure portal](/partner-center/partner-earned-credit-explanation#azure-cost-management) and confirm which costs have received the benefit of PEC. Remember that PEC only applies to CSP customers who have signed the MCA and are under the Azure plan.

If you have followed the steps above, and do not see the expected association, open a support request in the Azure portal.

You can also use the [Partner Center SDK](/partner-center/develop/get-invoice-unbilled-consumption-lineitems) and filter on `rateOfPartnerEarnedCredit` to automate PEC verification for a subscription.

## Next steps

- Learn more about the [Microsoft Partner Network](/partner-center/mpn-overview).
- Learn [how PEC is calculated and paid](/partner-center/partner-earned-credit-explanation).
- [Onboard customers](onboard-customer.md) to Azure Lighthouse.