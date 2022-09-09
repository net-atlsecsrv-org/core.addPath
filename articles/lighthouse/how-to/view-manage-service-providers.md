---
title: View and manage service providers
description: Customers can use the Service providers page in the Azure portal to view info about service providers, service provider offers, and delegated resources.
ms.date: 10/12/2020
ms.topic: how-to
---
# View and manage service providers

Customers can use the **Service providers** page in the [Azure portal](https://portal.azure.com) to view details about service providers and service provider offers, delegate specific resources to [Azure Lighthouse](../overview.md), and shop for new service provider offers.

> [!TIP]
> While we'll refer to service providers and customers here, [enterprises managing multiple tenants](../concepts/enterprise.md) can use the same process to consolidate their management experience.

To access the **Service providers** page in the Azure portal, the customer can select **All services**, then search for **Service providers** and select it. They can also find it by entering "Service providers" or "Azure Lighthouse" in the search box near the top of the Azure portal.

> [!NOTE]
> To view the **Service providers** page, a user in the customer's tenant must have the [Reader built-in role](../../role-based-access-control/built-in-roles.md#reader) (or another built-in role which includes Reader access).
>
> To add or update offers, delegate resources, and remove offers, the user must have the [Owner built-in role](../../role-based-access-control/built-in-roles.md#owner) for the subscription.

Keep in mind that the **Service providers** page only shows information about the service providers that have access to the customer's subscriptions or resource groups through Azure Lighthouse. If a customer works with additional service providers who don't use Azure Lighthouse to access the customer's resources, information about those service providers isn't shown here.

> [!TIP]
> Service providers can view information about their customers by navigating to **My customers** in the Azure portal. For more info, see [View and manage customers and delegated resources](view-manage-customers.md).

## View service provider details

To view details about service providers, the customer can select **Service provider offers** on the left side of the **Service providers** page.

For each service provider offer, the customer will see the service provider's name and the offer associated with it, along with the name that the customer entered during the onboarding process.

In the **Delegations** column, the customer sees how many subscriptions and/or resource groups have been delegated to the service provider for that offer. The service provider will be able to access and manage these subscriptions and/or resource groups according to the access levels specified in the offer.

## Add or remove service provider offers

A customer can add a new service provider offer from the **Service provider offers** page by selecting **Add offer**. The service provider must have published an offer for this customer. The customer can then select that offer from the **Private offers** screen and then select **Create**.

If the customer wants to remove a service provider offer, they can select the trash can icon in the row for that offer. After confirming the deletion, that service provider will no longer have access to the customer resources that were formerly delegated for that offer.

## Delegate resources

Before a service provider can access and manage a customer's resources, they must be delegated. If a customer has accepted an offer but has not yet delegated any resources, they'll see a note at the top of the **Service provider offers** section. This lets the customer know that they need to take action before the service provider can access any of the customer's resources.

To delegate subscriptions or resource groups:

1. Check the box for the row containing the service provider, offer, and name. Then select **Delegate resources** at the top of the screen.
1. In the **Offer details** section of the **Delegate resources** page, review the details about the service provider and offer. To review role assignments for the offer, select **Click here to see the details of the selected offer**.
1. In the **Delegate** section, select **Delegate subscriptions** or **Delegate resource groups**.
1. Choose the subscriptions and/or resource groups you'd like to delegate for this offer, then select **Add**.
1. Select the checkbox at the bottom of the page to confirm that you want to grant this service provider access to the resources that you've selected, then select **Delegate**.

## Update service provider offers

After a customer has added an offer, a service provider may publish an updated version of the same offer to Azure Marketplace. For example, they may want to add a new role definition. If a new version of the offer has been published, the **Service provider offers** page will show an "update" icon in the row for that offer. The customer can select this icon to see the differences between the current version of the offer and the new one.

 ![Update offer icon](../media/update-offer.jpg)

After reviewing the changes, the customer can choose to update to the new version. Once they do, the authorizations and other settings specified in the new version will apply to any subscriptions and/or resource groups that have been delegated for that offer.

## View delegations

Delegations represent the role assignments that grant permissions to the service provider for the resources a customer has delegated. To view this info, select **Delegations** on the left side of the **Service providers** page.

Filters at the top of the page let you sort and group your delegation info. You can also filter by specific customers, offers, or keywords.

> [!NOTE]
> Customers will not see these role assignments, or any users from the service provider tenant who have been granted these roles, when [viewing role assignments for the delegated scope in the Azure portal](../../role-based-access-control/role-assignments-list-portal.md#list-role-assignments-at-a-scope) or via APIs.

## Audit delegations in your environment

Customers may want to gain visibility into the subscriptions and/or resource groups that have been delegated to Azure Lighthouse. This is especially useful for those customers with a large number of subscriptions, or who have many users who perform management tasks.

We provide an [Azure Policy built-in policy definition](../../governance/policy/samples/built-in-policies.md#lighthouse) to [audit delegation of scopes to a managing tenant](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Lighthouse/Lighthouse_Delegations_Audit.json). You can assign this policy to a management group that includes all of the subscriptions that you want to audit. When you check for compliance with this policy, any delegated subscriptions and/or resource groups (within the management group to which the policy is assigned) will be shown in a noncompliant state. You can then review the results and confirm that there are no unexpected delegations.

Another [built-in policy definition](../../governance/policy/samples/built-in-policies.md#lighthouse) lets you [restrict delegations to specific managing tenants](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Lighthouse/AllowCertainManagingTenantIds_Deny.json). This policy can similarly be applied to a management group that includes any subscriptions for which you want to limit delegations. After the policy is deployed, any attempts to delegate a subscription to a tenant outside of the ones you specify will be denied.

For more information about how to assign a policy and view compliance state results, see [Quickstart: Create a policy assignment](../../governance/policy/assign-policy-portal.md).

## Next steps

- Learn more about [Azure Lighthouse](../overview.md).
- Learn how service providers can [view and manage customers](view-manage-customers.md) on the **My customers** page in the Azure portal.
