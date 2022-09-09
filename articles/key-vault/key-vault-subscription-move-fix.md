---
title: Change the key vault tenant ID after a subscription move - Azure Key Vault | Microsoft Docs
description: Learn how to switch the tenant ID for a key vault after a subscription is moved to a different tenant
services: key-vault
author: amitbapat
manager: rkarlin
tags: azure-resource-manager

ms.service: key-vault
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: ambapat

---
# Change a key vault tenant ID after a subscription move

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## Q: My subscription was moved from tenant A to tenant B. How do I change the tenant ID for my existing key vault and set correct ACLs for principals in tenant B?

When you create a new key vault in a subscription, it is automatically tied to the default Azure Active Directory tenant ID for that subscription. All access policy entries are also tied to this tenant ID. When you move your Azure subscription from tenant A to tenant B, your existing key vaults are inaccessible by the principals (users and applications) in tenant B. To fix this issue, you need to:

* Change the tenant ID associated with all existing key vaults in this subscription to tenant B.
* Remove all existing access policy entries.
* Add new access policy entries that are associated with tenant B.

For example, if you have key vault 'myvault' in a subscription that has been moved from tenant A to tenant B, here's how to change the tenant ID for this key vault and remove old access policies.

<pre>
Select-AzSubscription -SubscriptionId YourSubscriptionID                   # Select your Azure Subscription
$vaultResourceId = (Get-AzKeyVault -VaultName myvault).ResourceId          # Get your Keyvault's Resource ID 
$vault = Get-AzResource –ResourceId $vaultResourceId -ExpandProperties     # Get the properties for your Keyvault
$vault.Properties.TenantId = (Get-AzContext).Tenant.TenantId               # Change the Tenant that your Keyvault resides in
$vault.Properties.AccessPolicies = @()                                     # Accesspolicies can be updated with real
                                                                           # applications/users/rights so that it does not need to be                                                                              # done after this whole activity. Here we are not setting 
                                                                           # any access policies. 
Set-AzResource -ResourceId $vaultResourceId -Properties $vault.Properties  # Modifies the kevault's properties.
</pre>

Because this vault was in tenant A before the move, the original value of **$vault.Properties.TenantId** is tenant A, while **(Get-AzContext).Tenant.TenantId** is tenant B.

Now that your vault is associated with the correct tenant ID and old access policy entries are removed, set new access policy entries with [Set-AzKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/az.keyvault/Set-azKeyVaultAccessPolicy).

If you are using MSI, you'll also have to update the MSI identity since the old identity will no longer be in the correct AAD tenant.

## Next steps

If you have questions about Azure Key Vault, visit the [Azure Key Vault Forums](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault).
