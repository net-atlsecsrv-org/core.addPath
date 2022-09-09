---
title: Register the Azure VMware Solution resource provider
description: Steps to register the Azure VMware Solution resource provider.
ms.topic: include
ms.date: 02/17/2021
---

<!-- Used in deploy-azure-vmware-solution.md and tutorial-create-private-cloud.md -->

To use Azure VMware Solution, you must first register the resource provider with your subscription. For more information about resource providers, see [Azure resource providers and types](../../azure-resource-manager/management/resource-providers-and-types.md).


### [Portal](#tab/azure-portal)
 
1. Sign in to the [Azure portal](https://portal.azure.com).

1. On the Azure portal menu, select **All services**.

1. In the **All services** box, enter **subscription**, and then select **Subscriptions**.

1. Select the subscription from the subscription list to view.

1. Select **Resource providers** and enter **Microsoft.AVS** into the search. 
 
1. If the resource provider is not registered, select **Register**.

### [Azure CLI](#tab/azure-cli)

To begin using Azure CLI:

[!INCLUDE [azure-cli-prepare-your-environment-no-header](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

Sign in to the Azure subscription you use for the Azure VMware Solution deployment through the Azure CLI. Register the `Microsoft.AVS` resource provider with the [az provider register](/cli/azure/provider#az_provider_register) command:

```azurecli-interactive
az provider register -n Microsoft.AVS --subscription <your subscription ID>
```

You can use the [az provider list](/cli/azure/provider#az_provider_list) command to see all available providers.

---


 
