﻿---
title: Manage logs - Azure CLI - Azure Database for PostgreSQL - Single Server
description: This article describes how to configure and access the server logs (.log files) in Azure Database for PostgreSQL - Single Server by using the Azure CLI.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 5/6/2019
---
# Configure and access server logs by using Azure CLI
You can download the PostgreSQL server error logs by using the command-line interface (Azure CLI). However, access to transaction logs isn't supported. 

## Prerequisites
To step through this how-to guide, you need:
- [Azure Database for PostgreSQL server](quickstart-create-server-database-azure-cli.md)
- The [Azure CLI](/cli/azure/install-azure-cli) command-line utility or Azure Cloud Shell in the browser

## Configure logging
You can configure the server to access query logs and error logs. Error logs can have auto-vacuum, connection, and checkpoint information.
1. Turn on logging.
2. To enable query logging, update **log\_statement** and **log\_min\_duration\_statement**.
3. Update retention period.

For more information, see [Customizing server configuration parameters](howto-configure-server-parameters-using-cli.md).

## List logs
To list the available log files for your server, run the [az postgres server-logs list](/cli/azure/postgres/server-logs) command.

You can list the log files for server **mydemoserver.postgres.database.azure.com** under the resource group **myresourcegroup**. Then direct the list of log files to a text file called **log\_files\_list.txt**.
```azurecli-interactive
az postgres server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## Download logs locally from the server
With the [az postgres server-logs download](/cli/azure/postgres/server-logs) command, you can download individual log files for your server. 

Use the following example to download the specific log file for the server **mydemoserver.postgres.database.azure.com** under the resource group **myresourcegroup** to your local environment.
```azurecli-interactive
az postgres server-logs download --name 20170414-mydemoserver-postgresql.log --resource-group myresourcegroup --server mydemoserver
```
## Next steps
- To learn more about server logs, see [Server logs in Azure Database for PostgreSQL](concepts-server-logs.md).
- For more information about server parameters, see [Customize server configuration parameters using Azure CLI](howto-configure-server-parameters-using-cli.md).
