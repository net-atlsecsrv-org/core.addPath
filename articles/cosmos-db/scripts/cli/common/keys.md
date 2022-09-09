---
title: Work with account keys and connection strings for an Azure Cosmos account
description: Work with account keys and connection strings for an Azure Cosmos account
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 9/25/2019
---

# Work with account keys and connection strings for an Azure Cosmos account using Azure CLI

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

If you choose to install and use the CLI locally, this topic requires that you are running the Azure CLI version 2.0.73 or later. Run `az --version` to find the version. If you need to install or upgrade, see [Install Azure CLI](/cli/azure/install-azure-cli).

## Sample script

This script demonstrates four operations.

- List all account keys
- List read only account keys
- List connection strings
- Regenerate account keys

> [!NOTE]
> This sample demonstrates using a SQL (Core) API account but the account key and connection string operations are identical across all database APIs in Cosmos DB.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/common/keys.sh "Keys and connection string operations for Cosmos DB.")]

## Clean up deployment

After the script sample has been run, the following command can be used to remove the resource group and all resources associated with it.

```azurecli-interactive
az group delete --name $resourceGroupName
```

## Script explanation

This script uses the following commands. Each command in the table links to command specific documentation.

| Command | Notes |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Creates a resource group in which all resources are stored. |
| [az cosmosdb create](/cli/azure/cosmosdb#az-cosmosdb-create) | Creates an Azure Cosmos DB account. |
| [az cosmosdb keys list](/cli/azure/cosmosdb/keys#az-cosmosdb-keys-list) | List the keys for an Azure Cosmos DB account. |
| [az cosmosdb list-read-only-keys](/cli/azure/cosmosdb#az-cosmosdb-list-read-only-keys) | List the read only keys for an Azure Cosmos DB account. |
| [az cosmosdb list-connection-strings](/cli/azure/cosmosdb#az-cosmosdb-list-connection-strings) | List the connection strings for an Azure Cosmos DB account. |
| [az cosmosdb regenerate-key](/cli/azure/cosmosdb#az-cosmosdb-regenerate-key) | Regenerate keys for an Azure Cosmos DB account. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Deletes a resource group including all nested resources. |

## Next steps

For more information on the Azure Cosmos DB CLI, see [Azure Cosmos DB CLI documentation](/cli/azure/cosmosdb).

All Azure Cosmos DB CLI script samples can be found in the [Azure Cosmos DB CLI GitHub Repository](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
