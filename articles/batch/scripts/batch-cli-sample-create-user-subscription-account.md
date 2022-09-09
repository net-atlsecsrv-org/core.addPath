---
title: Azure CLI Script Example - Create Batch account - user subscription
description: This script creates an Azure Batch account in user subscription mode. This account allocates compute nodes into your subscription.
ms.topic: sample
ms.date: 01/29/2018 
ms.custom: devx-track-azurecli
---

# CLI example: Create a Batch account in user subscription mode

This script creates an Azure Batch account in user subscription mode. An account that allocates compute nodes into your subscription must be authenticated via an Azure Active Directory token. The compute nodes allocated count toward your subscription's vCPU (core) quota. 

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- This tutorial requires version 2.0.20 or later of the Azure CLI. If using Azure Cloud Shell, the latest version is already installed.  

## Example script

[!code-azurecli-interactive[main](../../../cli_scripts/batch/create-account/create-account-user-subscription.sh "Create Account using user subscription")]

## Clean up deployment

Run the following command to remove the
resource group and all resources associated with it.

```azurecli-interactive
az group delete --name myResourceGroup
```

## Script explanation

This script uses the following commands. Each command in the table links to command-specific documentation.

| Command | Notes |
|---|---|
| [az role assignment create](/cli/azure/role) | Create a new role assignment for a user, group, or service principal. |
| [az group create](/cli/azure/group#az-group-create) | Creates a resource group in which all resources are stored. |
| [az keyvault create](/cli/azure/keyvault#az-keyvault-create) | Creates a key vault. |
| [az keyvault set-policy](/cli/azure/keyvault#az-keyvault-set-policy) | Update the security policy of the specified key vault. |
| [az batch account create](/cli/azure/batch/account#az-batch-account-create) | Creates the Batch account.  |
| [az batch account login](/cli/azure/batch/account#az-batch-account-login) | Authenticates against the specified Batch account for further CLI interaction.  |
| [az group delete](/cli/azure/group#az-group-delete) | Deletes a resource group including all nested resources. |

## Next steps

For more information on the Azure CLI, see [Azure CLI documentation](/cli/azure).
