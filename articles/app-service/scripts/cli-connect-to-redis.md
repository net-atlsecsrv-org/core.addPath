---
title: 'CLI: Connect an app to Redis'
description: Learn how to use the Azure CLI to automate deployment and management of your App Service app. This sample shows how to connect an app to an Azure Cache for Redis.
author: msangapu-msft
tags: azure-service-management

ms.assetid: bc8345b2-8487-40c6-a91f-77414e8688e6
ms.devlang: azurecli
ms.topic: sample
ms.date: 12/11/2017
ms.author: msangapu
ms.custom: mvc, seodec18
---

# Connect an App Service app to an Azure Cache for Redis using CLI

This sample script creates an Azure Cache for Redis and an App Service app. It then links the Azure Cache for Redis to the app using app settings.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

If you choose to install and use the CLI locally, you need Azure CLI version 2.0 or later. To find the version, run `az --version`. If you need to install or upgrade, see [Install Azure CLI]( /cli/azure/install-azure-cli).

## Sample script

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/connect-to-redis/connect-to-redis.sh "Azure Cache for Redis")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## Script explanation

This script uses the following commands to create a resource group, App Service app, Azure Cache for Redis, and all related resources. Each command in the table links to command specific documentation.

| Command | Notes |
|---|---|
| [`az group create`](/cli/azure/group?view=azure-cli-latest#az-group-create) | Creates a resource group in which all resources are stored. |
| [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create) | Creates an App Service plan. |
| [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) | Creates an App Service app. |
| [`az redis create`](/cli/azure/redis?view=azure-cli-latest#az-redis-create) | Create new Azure Cache for Redis instance. |
| [`az redis list-keys`](/cli/azure/redis?view=azure-cli-latest#az-redis-list-keys) | Lists the access keys for the Azure Cache for Redis instance. |
| [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) | Creates or updates an app setting for an App Service app. App settings are exposed as environment variables for your app. |

## Next steps

For more information on the Azure CLI, see [Azure CLI documentation](/cli/azure).

Additional App Service CLI script samples can be found in the [Azure App Service documentation](../samples-cli.md).