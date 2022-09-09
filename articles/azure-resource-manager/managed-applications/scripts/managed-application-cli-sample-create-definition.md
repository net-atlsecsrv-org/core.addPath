---
title:  Create Managed Application definition - Azure CLI
description: Provides an Azure CLI script sample that creates a managed application definition in the subscription.
author: tfitzmac

ms.devlang: azurecli
ms.topic: sample
ms.date: 10/25/2017
ms.author: tomfitz 
ms.custom: devx-track-azurecli
---

# Create a managed application definition with Azure CLI

This script publishes a managed application definition to a service catalog. 


[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## Sample script

[!code-azurecli[main](../../../../cli_scripts/managed-applications/create-definition/create-definition.sh "Create definition")]


## Script explanation

This script uses the following command to create the managed application definition. Each command in the table links to command-specific documentation.

| Command | Notes |
|---|---|
| [az managedapp definition create](/cli/azure/managedapp/definition#az-managedapp-definition-create) | Create a managed application definition. Provide the package that contains the required files. |


## Next steps

* For an introduction to managed applications, see [Azure Managed Application overview](../overview.md).
* For more information on the Azure CLI, see [Azure CLI documentation](/cli/azure).
