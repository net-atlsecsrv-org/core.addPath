---
title: Azure CLI script - Change server configurations (PostgreSQL)
description: This sample CLI script lists all available server configuration options and updates the value of one of the options.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 02/28/2018
---

# List and update configurations of an Azure Database for PostgreSQL server using Azure CLI
This sample CLI script lists all available configuration parameters as well as their allowable values for Azure Database for PostgreSQL server, and sets the *log_retention_days* to a value that is other than the default one.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- This article requires version 2.0 or later of the Azure CLI. If using Azure Cloud Shell, the latest version is already installed.

## Sample script
In this sample script, edit the highlighted lines to update the admin username and password to your own.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/change-server-configurations/change-server-configurations.sh?highlight=15-16 "List and update configurations of Azure Database for PostgreSQL.")]

## Clean up deployment
Use the following command to remove the resource group and all resources associated with it after the script has been run. 
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/change-server-configurations/delete-postgresql.sh  "Delete the resource group.")]

## Script explanation
This script uses the commands outlined in the following table:

| **Command** | **Notes** |
|---|---|
| [az group create](/cli/azure/group) | Creates a resource group in which all resources are stored. |
| [az postgres server create](/cli/azure/postgres/server) | Creates a PostgreSQL server that hosts the databases. |
| [az postgres server configuration list](/cli/azure/postgres/server/configuration) | List the configurations of an Azure Database for PostgreSQL server. |
| [az postgres server configuration set](/cli/azure/postgres/server/configuration) | Update the configuration of an Azure Database for PostgreSQL server. |
| [az postgres server configuration show](/cli/azure/postgres/server/configuration) | Show the configuration of an Azure Database for PostgreSQL server. |
| [az group delete](/cli/azure/group) | Deletes a resource group including all nested resources. |

## Next steps
- Read more information on the Azure CLI: [Azure CLI documentation](/cli/azure).
- Try additional scripts: [Azure CLI samples for Azure Database for PostgreSQL](../sample-scripts-azure-cli.md)
- For more information on server parameters, see [How To Configure server parameters in Azure portal](../howto-configure-server-parameters-using-portal.md).
