---
title: 'Tutorial: Create Azure Database for PostgreSQL - Flexible Server and Azure App Service Web App in same virtual network'
description: Quickstart guide to create Azure Database for PostgreSQL - Flexible Server with Web App in a virtual network
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 09/22/2020
ms.custom: mvc, devx-track-azurecli
---

# Tutorial: Create an Azure Database for PostgreSQL - Flexible Server with App Services Web App in Virtual network

> [!IMPORTANT]
> Azure Database for PostgreSQL - Flexible Server is in preview

This tutorial shows you how create a Azure App Service Web app with Azure Database for PostgreSQL - Flexible Server (Preview) inside a [Virtual network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview).

In this tutorial you will
>[!div class="checklist"]
> * Create a PostgreSQL flexible server in a virtual network
> * Create a web app
> * Add the web app to the virtual network
> * Connect to Postgres from the web app 

## Prerequisites

If you don't have an Azure subscription, create a [free](https://azure.microsoft.com/free/) account before you begin.

This article requires that you're running the Azure CLI version 2.0 or later locally. To see the version installed, run the `az --version` command. If you need to install or upgrade, see [Install Azure CLI](/cli/azure/install-azure-cli).

You'll need to login to your account using the [az login](/cli/azure/authenticate-azure-cli?view=interactive-log-in) command. Note the **id** property from the command output for the corresponding subscription name.

```azurecli
az login
```

If you have multiple subscriptions, choose the appropriate subscription in which the resource should be billed. Select the specific subscription ID under your account using [az account set](/cli/azure/account) command. Substitute the **subscription ID** property from the **az login** output for your subscription into the subscription ID placeholder.

```azurecli
az account set --subscription <subscription id>
```

## Create a PostgreSQL Flexible Server in a new virtual network

Create a private flexible server inside a virtual network (VNET) using the following command:
```azurecli
az postgres flexible-server create --resource-group myresourcegroup --location westus2
```
This command performs the following actions, which may take a few minutes:

- Create the resource group if it doesn't already exist.
- Generates a server name if it is not provided.
- Create a new virtual network for your new postgreSQL server. Make a note of virtual network name and subnet name created for your server since you need to add the web app to the same virtual network.
- Creates admin username , password for your server if not provided.
- Creates an empty database called **postgres**

> [!NOTE]
> - Make a note of your password that will be generate for you if not provided. If you forget the password you would have to reset the password using ``` az postgres flexible-server update``` command
> - If you are not using App Service Environment , you would need to enable Allow access from any Azure IPs using this command. 
>  ```azurecli
>  az postgres flexible-server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
>  ```


## Create a Web App
In this section, you create app host in App Service app, connect this app to the Postgres database, then deploy your code to that host. Make sure you're in the repository root of your application code in the terminal.

Create an App Service app (the host process) with the az webapp up command

```azurecli
az webapp up --resource-group myresourcegroup --location westus2 --plan testappserviceplan --sku B1 --name mywebapp
```

> [!NOTE]
> - For the --location argument, use the same location as you did for the database in the previous section.
> - Replace <app-name> with a unique name across all Azure (the server endpoint is https://\<app-name>.azurewebsites.net). Allowed characters for <app-name> are A-Z, 0-9, and -. A good pattern is to use a combination of your company name and an app identifier.

This command performs the following actions, which may take a few minutes:

- Create the resource group if it doesn't already exist. (In this command you use the same resource group in which you created the database earlier.)
- Create the App Service plan ```testappserviceplan``` in the Basic pricing tier (B1), if it doesn't exist. --plan and --sku are optional.
- Create the App Service app if it doesn't exist.
- Enable default logging for the app, if not already enabled.
- Upload the repository using ZIP deployment with build automation enabled.

## Add the Web App to the virtual network
Use **az webapp vnet-integration** command to add a regional virtual network integration to a webapp. Replace <vnet-name> and <subnet-name> with the virtual network and subnet name that the flexible server is using.

```azurecli
az webapp vnet-integration add -g myresourcegroup -n  mywebapp --vnet <vnet-name> --subnet <subnet-name>
```

## Configure environment variables to connect the database
With the code now deployed to App Service, the next step is to connect the app to the flexible server in Azure. The app code expects to find database information in a number of environment variables. To set environment variables in App Service, you create "app settings" with the ```az webapp config appsettings``` set command.

```azurecli
az webapp config appsettings set --settings DBHOST="<postgres-server-name>.postgres.database.azure.com" DBNAME="postgres" DBUSER="<username>" DBPASS="<password>"
```


- Replace ```postgres-server-name```,```username```, ```password``` for the newly created flexible server command.
- Replace <username> and <password> with the credentials that the command also generated for you.
- The resource group and app name are drawn from the cached values in the .azure/config file.
- The command creates settings named ```DBHOST```,```DBNAME```,```DBUSER```, and ```DBPASS```. If your application code is using different name for the database information then use those names for the app settings as mentioned in the code.

## Clean up resources

Clean up all resources you created in the tutorial using the following command. This command deletes all the resources in this resource group.

```azurecli
az group delete -n myresourcegroup
```


## Next steps
> [!div class="nextstepaction"]
> [Map an existing custom DNS name to Azure App Service](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain)