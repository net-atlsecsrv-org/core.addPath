---
title: Create resource lock for a Azure Cosmos DB Core (SQL) API database and container
description: Create resource lock for a Azure Cosmos DB Core (SQL) API database and container
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 06/03/2020
---

# Create resource lock for a Azure Cosmos DB Core (SQL) API database and container using Azure CLI

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

If you choose to install and use the CLI locally, this topic requires that you are running the Azure CLI version 2.6.0 or later. Run `az --version` to find the version. If you need to install or upgrade, see [Install Azure CLI](/cli/azure/install-azure-cli).

> [!IMPORTANT]
> Resource locks do not work for changes made by users connecting using any Cosmos DB SDK, any tools that connect via account keys, or the Azure Portal unless the Cosmos DB account is first locked with the `disableKeyBasedMetadataWriteAccess` property enabled. To learn more about how to enable this property see, [Preventing changes from SDKs](../../../role-based-access-control.md#prevent-sdk-changes).

## Sample script

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/sql/lock.sh "Create a resource lock for an Azure Cosmos DB Core (SQL) API database and container.")]

## Script explanation

This script uses the following commands. Each command in the table links to command specific documentation.

| Command | Notes |
|---|---|
| [az lock create](/cli/azure/lock#az-lock-create) | Creates a lock. |
| [az lock list](/cli/azure/lock#az-lock-list) | List lock information. |
| [az lock show](/cli/azure/lock#az-lock-show) | Show properties of a lock. |
| [az lock delete](/cli/azure/lock#az-lock-delete) | Deletes a lock. |

## Next steps

-[Lock resources to prevent unexpected changes](../../../../azure-resource-manager/management/lock-resources.md)

-[Azure Cosmos DB CLI documentation](/cli/azure/cosmosdb).

-[Azure Cosmos DB CLI GitHub Repository](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
