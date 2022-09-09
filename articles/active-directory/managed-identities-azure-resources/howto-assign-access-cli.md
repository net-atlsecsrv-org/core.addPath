---
title: Assign a managed identity access to a resource using Azure CLI - Azure AD
description: Step-by-step instructions for assigning a managed identity on one resource, access to another resource, using Azure CLI.
services: active-directory
documentationcenter: 
author: barclayn
manager: daveba
editor: 

ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2017
ms.author: barclayn
ms.collection: M365-identity-device-management 
ms.custom: devx-track-azurecli
---

# Assign a managed identity access to a resource using Azure CLI

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Once you've configured an Azure resource with a managed identity, you can give the managed identity access to another resource, just like any security principal. This example shows you how to give an Azure virtual machine or virtual machine scale set's managed identity access to an Azure storage account using Azure CLI.

## Prerequisites

- If you're unfamiliar with managed identities for Azure resources, check out the [overview section](overview.md). **Be sure to review the [difference between a system-assigned and user-assigned managed identity](overview.md#managed-identity-types)**.
- If you don't already have an Azure account, [sign up for a free account](https://azure.microsoft.com/free/) before continuing.
- To run the example scripts, you have two options:
    - Use the [Azure Cloud Shell](../../cloud-shell/overview.md), which you can open using the **Try It** button on the top right corner of code blocks.
    - Run scripts locally by installing the latest version of the [Azure CLI](/cli/azure/install-azure-cli), then sign in to Azure using [az login](/cli/azure/reference-index#az-login). Use an account associated with the Azure subscription in which you'd like to create resources.

## Use Azure RBAC to assign a managed identity access to another resource

After you've enabled managed identity on an Azure resource, such as an [Azure virtual machine](qs-configure-cli-windows-vm.md) or [Azure virtual machine scale set](qs-configure-cli-windows-vmss.md): 

1. In this example, we are giving an Azure virtual machine access to a storage account. First we use [az resource list](/cli/azure/resource/#az-resource-list) to get the service principal for the virtual machine named myVM:

   ```azurecli-interactive
   spID=$(az resource list -n myVM --query [*].identity.principalId --out tsv)
   ```
   For an Azure virtual machine scale set, the command is the same except here, you get the service principal for the virtual machine scale set named "DevTestVMSS":
   
   ```azurecli-interactive
   spID=$(az resource list -n DevTestVMSS --query [*].identity.principalId --out tsv)
   ```

1. Once you have the service principal ID, use [az role assignment create](/cli/azure/role/assignment#az-role-assignment-create) to give the virtual machine or virtual machine scale set "Reader" access to a storage account called "myStorageAcct":

   ```azurecli-interactive
   az role assignment create --assignee $spID --role 'Reader' --scope /subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/myStorageAcct
   ```

## Next steps

- [Managed identities for Azure resources overview](overview.md)
- To enable managed identity on an Azure virtual machine, see [Configure managed identities for Azure resources on an Azure VM using Azure CLI](qs-configure-cli-windows-vm.md).
- To enable managed identity on an Azure virtual machine scale set, see [Configure managed identities for Azure resources on a virtual machine scale set using Azure CLI](qs-configure-cli-windows-vmss.md).
