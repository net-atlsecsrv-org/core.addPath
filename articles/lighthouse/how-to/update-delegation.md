---
title: Update a delegation
description: Learn how to update a delegation for a customer previously onboarded to Azure Lighthouse.
ms.date: 12/03/2020
ms.topic: how-to
---

# Update a delegation

After you have onboarded a subscription (or resource group) to Azure Lighthouse, you may need to make changes. For example, your customer may want you to take on additional management tasks that require a different Azure built-in role, or you may need to change the tenant to which a customer subscription is delegated.

> [!TIP]
> Though we refer to service providers and customers in this topic, [enterprises managing multiple tenants](../concepts/enterprise.md) can use the same process to set up Azure Lighthouse and consolidate their management experience.

If you [onboarded your customer through Azure Resource Manager (ARM) templates](onboard-customer.md), a new deployment must be performed for that customer. Depending on what you are changing, you may want to update the original offer, or remove the original offer and create a new one.

- **If you are changing authorizations only**: You can update your delegation by changing only the **authorizations** section of the ARM template.
- **If you are changing the managing tenant**: You must create a new ARM template using with a different **mspOfferName** than your previous offer.

## Update your ARM template

To update your delegation, you will need to deploy an ARM template that includes the changes you'd like to make.

If you are only updating authorizations (such as adding a new user group with a role you hadn't previously included, or changing the role for an existing user), you can use the same **mspOfferName** as in the [ARM template](onboard-customer.md#create-an-azure-resource-manager-template) that you used for the previous delegation. You can use your previous template as a starting point. Then, make the changes you need, such as replacing one Azure built-in role with another, or adding a completely new authorization to the template.

If you change the **mspOfferName**, this will be considered a new, separate offer. This is required if you are changing the managing tenant.

It's not necessary to change the **mspOfferName** if the managing tenant remains the same. In most cases, we recommend having only one **mspOfferName** in use by the same customer and managing tenant. If you choose to change it anyway, be sure that the customer's previous delegation is removed before deploying the new one.

## Remove the previous delegation

Before performing a new deployment, you may want to [remove access to the previous delegation](remove-delegation.md). This ensures that all previous permissions are removed, allowing you to start clean with the exact users/groups and roles that should apply going forward.

> [!IMPORTANT]
> If you use a new **mspOfferName** and keep any of the same **principalId** values, you must remove access to the previous delegation before deploying the new offer. If you don't remove the offer first, users who had previously granted permission may lose access completely due to conflicting assignments.

If you are changing the managing tenant, you can leave the previous offer in place if you want both tenants to continue to have access. If you only want the new managing tenant to have access, the earlier offer must be removed. This can be done either before or after you onboard the new offer.

If you are updating the offer to adjust authorizations only, and keeping the same **mspOfferName**, you don't have to remove the previous delegation. The new deployment will replace the previous delegation, and only the authorizations in the newest template will apply.

:::image type="content" source="../media/update-delegation.jpg" alt-text="Diagram showing when to change mspOfferName and remove a previous delegation.":::

Removing access to the delegation can be done by any user in the managing tenant who was granted the [Managed Services Registration Assignment Delete Role](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) in the original delegation. If no user in your managing tenant has this role, you can ask the customer to [remove access to the offer in the Azure portal](view-manage-service-providers.md#add-or-remove-service-provider-offers).

> [!TIP]
> If you have removed the previous delegation following the steps above, and are still unable to deploy the new ARM template, you may need to [remove the registration definition completely](/powershell/module/az.managedservices/remove-azmanagedservicesdefinition). This can be done by any user with the Owner role in the customer tenant.  

## Deploy the ARM template

Your customer can [deploy the updated template](onboard-customer.md#deploy-the-azure-resource-manager-templates) in the same way that they did previously: in the Azure portal, by using PowerShell, or by using Azure CLI.

After the deployment has been completed, [confirm that it was successful](onboard-customer.md#confirm-successful-onboarding). The updated authorizations will then be in effect for the subscription or resource group(s) that the customer has delegated.

## Updating Managed Service offers

If you onboarded your customer through a Managed Service offer published to Azure Marketplace, and you want to update authorizations, you can update the delegation by [publishing a new version of your offer](../../marketplace/partner-center-portal/update-existing-offer.md) with the [authorizations](../../marketplace/partner-center-portal/create-new-managed-service-offer.md#authorization) that you want to use updated in the plan for that customer. The customer will then be able to update to the newest version in the Azure portal.

If you want to change the managing tenant, you will need to [create and publish a new Managed Service offer](../../marketplace/partner-center-portal/create-new-managed-service-offer.md) for the customer to accept.

> [!TIP]
> As mentioned earlier, we recommend that you don’t use multiple different offers between the same customer and managing tenant. If you do publish a new offer for the same customer which uses the same managing tenant, be sure that the earlier offer is removed before the customer accepts the newer offer.

## Next steps

- [View and manage customers](view-manage-customers.md) by going to **My customers** in the Azure portal.
- Learn how to [remove access to a delegation](remove-delegation.md) that was previously onboarded.
