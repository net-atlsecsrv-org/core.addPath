---
title: Link an Azure account to a partner ID
description: Track engagements with Azure customers by linking a partner ID to the user account that you use to manage the customer's resources.
author: dhirajgandhi
ms.reviewer: dhgandhi
ms.author: banders
ms.date: 10/05/2020
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
---

# Link a partner ID to your Azure accounts

Microsoft partners provide services that help customers achieve business and mission objectives using Microsoft products. When acting on behalf of the customer managing, configuring, and supporting Azure services, the partner users will need access to the customer’s environment. Using Partner Admin Link (PAL), partners can associate their partner network ID with the credentials used for service delivery.

PAL enables Microsoft to identify and recognize partners who drive Azure customer success. Microsoft can attribute influence and Azure consumed revenue to your organization based on the account's permissions (Azure role) and scope (subscription, resource group, resource ).

## Get access from your customer

Before you link your partner ID, your customer must give you access to their Azure resources by using one of the following options:

- **Guest user**: Your customer can add you as a guest user and assign any Azure roles. For more information, see [Add guest users from another directory](../../active-directory/external-identities/what-is-b2b.md).

- **Directory account**: Your customer can create a user account for you in their own directory and assign any Azure role.

- **Service principal**: Your customer can add an app or script from your organization in their directory and assign any Azure role. The identity of the app or script is known as a service principal.

- **Azure Lighthouse**: Your customer can delegate a subscription (or resource group) so that your users can work on it from within your tenant. For more information, see [Azure delegated resource management](../../lighthouse/concepts/azure-delegated-resource-management.md).

## Link to a partner ID

When you have access to the customer's resources, use the Azure portal, PowerShell, or the Azure CLI to link your Microsoft Partner Network ID (MPN ID) to your user ID or service principal. Link the partner ID in each customer tenant.

### Use the Azure portal to link to a new partner ID

1. Go to [Link to a partner ID](https://portal.azure.com/#blade/Microsoft_Azure_Billing/managementpartnerblade) in the Azure portal.

2. Sign in to the Azure portal.

3. Enter the Microsoft partner ID. The partner ID is the [Microsoft Partner Network](https://partner.microsoft.com/) ID for your organization. Be sure to use the **Associated MPN ID** shown on your partner profile.

   ![Screenshot that shows Link to a partner ID](./media/link-partner-id/link-partner-id01.png)

4. To link a partner ID for another customer, switch the directory. Under **Switch directory**, select your directory.

   ![Screenshot that shows Switch directory](./media/link-partner-id/directory-switcher.png)

### Use PowerShell to link to a new partner ID

1. Install the [Az.ManagementPartner](https://www.powershellgallery.com/packages/Az.ManagementPartner/) PowerShell module.

2. Sign in to the customer's tenant with either the user account or the service principal. For more information, see [Sign in with PowerShell](/powershell/azure/authenticate-azureps).

   ```azurepowershell-interactive
    C:\> Connect-AzAccount -TenantId XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
   ```

3. Link to the new partner ID. The partner ID is the [Microsoft Partner Network](https://partner.microsoft.com/) ID for your organization. Be sure to use the **Associated MPN ID** shown on your partner profile.


    ```azurepowershell-interactive
    C:\> new-AzManagementPartner -PartnerId 12345
    ```

#### Get the linked partner ID
```azurepowershell-interactive
C:\> get-AzManagementPartner
```

#### Update the linked partner ID
```azurepowershell-interactive
C:\> Update-AzManagementPartner -PartnerId 12345
```
#### Delete the linked partner ID
```azurepowershell-interactive
C:\> remove-AzManagementPartner -PartnerId 12345
```

### Use the Azure CLI to link to a new partner ID
1. Install the Azure CLI extension.

    ```azurecli-interactive
    C:\ az extension add --name managementpartner
    ```

2. Sign in to the customer's tenant with either the user account or the service principal. For more information, see [Sign in with the Azure CLI](/cli/azure/authenticate-azure-cli).

    ```azurecli-interactive
    C:\ az login --tenant <tenant>
    ```

3. Link to the new partner ID. The partner ID is the [Microsoft Partner Network](https://partner.microsoft.com/) ID for your organization.

     ```azurecli-interactive
     C:\ az managementpartner create --partner-id 12345
      ```  

#### Get the linked partner ID
```azurecli-interactive
C:\ az managementpartner show
```

#### Update the linked partner ID
```azurecli-interactive
C:\ az managementpartner update --partner-id 12345
```

#### Delete the linked partner ID
```azurecli-interactive
C:\ az managementpartner delete --partner-id 12345
```

## Next steps

Join the discussion in the [Microsoft Partner Community](https://aka.ms/PALdiscussion) to receive updates or send feedback.

## Frequently asked questions

**Who can link the partner ID?**

Any user from the partner organization who manages a customer's Azure resources can link the partner ID to the account.

**Can a partner ID be changed after it's linked?**

Yes. A linked partner ID can be changed, added, or removed.

**What if a user has an account in more than one customer tenant?**

The link between the partner ID and the account is done for each customer tenant. Link the partner ID in each customer tenant.

However, if you are managing customer resources through Azure Lighthouse, you should create the link in your service provider tenant, using an account that has access to the customer resources. For more information, see [Link your partner ID to track your impact on delegated resources](../../lighthouse/how-to/partner-earned-credit.md).

**Can other partners or customers edit or remove the link to the partner ID?**

The link is associated at the user account level. Only you can edit or remove the link to the partner ID. The customer and other partners can't change the link to the partner ID.

**Which MPN ID should I use if my company has multiple?**

Be sure to use the **Associated MPN ID** shown in your partner profile.

**Where can I find influenced revenue reporting for linked partner ID?**

Cloud Product Performance reporting is available to partners in Partner Center at [My Insights dashboard](https://partner.microsoft.com/membership/reports/myinsights). You need to select Partner Admin Link as the partner association type.

**Why can't I see my customer in the reports?**

You can't see the customer in the reports due to following reasons

1. The linked user account doesn't have [Azure role-based access control (Azure RBAC)](../../role-based-access-control/overview.md) on any customer Azure subscription or resource.

2. The Azure subscription where the user has [Azure role-based access control (Azure RBAC)](../../role-based-access-control/overview.md) access doesn't have any usage.

**Does link partner ID works with Azure Stack?**

Yes, You can link your partner ID for Azure Stack.

**How do I link my partner ID if my company uses [Azure Lighthouse](../../lighthouse/overview.md) to access customer resources?**

If you onboard customers to Azure delegated resource management by [publishing a managed services offer to Azure Marketplace](../../lighthouse/how-to/publish-managed-services-offers.md), your MPN ID will automatically be associated.

If you [onboard customers by deploying Azure Resource Manager templates](../../lighthouse/how-to/onboard-customer.md), you'll need to associate your MPN ID with at least one user account that has access to each of your onboarded subscriptions. Note that you'll need to do this in your service provider tenant rather than in each customer tenant. For simplicity, we recommend creating a service principal account in your tenant, associating it with your MPN ID, then granting it access to every customer you onboard with an [Azure built-in role that is eligible for partner earned credit](/partner-center/azure-roles-perms-pec). For more information, see [Link your partner ID to track your impact on delegated resources](../../lighthouse/how-to/partner-earned-credit.md).

**How do I explain Partner Admin Link (PAL) to my Customer?**

Partner Admin Link (PAL) enables Microsoft to identify and recognize those partners who are helping customers achieve business objectives and realize value in the cloud. Customers must first provide a partner access to their Azure resource. Once access is granted, the partner’s Microsoft Partner Network ID (MPN ID) is associated. This association helps Microsoft understand the ecosystem of IT service providers and to refine the tools and programs needed to best support our common customers.

**What data does PAL collect?**

The PAL association to existing credentials provides no new customer data to Microsoft. It simply provides the telemetry to Microsoft where a partner is actively involved in a customer’s Azure environment. Microsoft can attribute influence and Azure consumed revenue from customer environment to partner organization based on the account's permissions (Azure role) and scope (Management Group, Subscription, Resource Group, Resource) provided to the partner by customer. 

**Does this impact the security of a customer’s Azure Environment?**

PAL association only adds partner’s MPN ID to the credential already provisioned and it does not alter any permissions (Azure role) or provide additional Azure service data to partner or Microsoft.