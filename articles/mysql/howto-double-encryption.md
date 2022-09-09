---
title: Infrastructure double encryption - Azure portal - Azure Database for MySQL
description: Learn how to set up and manage Infrastructure double encryption for your Azure Database for MySQL.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: how-to
ms.date: 06/30/2020
---

# Infrastructure double encryption for Azure Database for MySQL

Learn how to use the how set up and manage Infrastructure double encryption for your Azure Database for MySQL.

## Prerequisites

* You must have an Azure subscription and be an administrator on that subscription.

## Create an Azure Database for MySQL server with Infrastructure Double encryption - Portal

Follow these steps to create an Azure Database for MySQL server with Infrastructure double encryption from Azure portal:

1. Select **Create a resource** (+) in the upper-left corner of the  portal.

2. Select **Databases** > **Azure Database for MySQL**. You can also enter **MySQL** in the search box to find the service.

   ![Azure Database for MySQL option](./media/quickstart-create-mysql-server-database-using-azure-portal/2_navigate-to-mysql.png)

3. Provide the basic information of the server. Select **Additional settings** and enabled the **Infrastructure double encryption** checkbox to set the parameter.

    ![Azure Database for MySQL selections](./media/howto-double-encryption/infrastructure-encryption-selected.png)

4. Select **Review + create** to provision the server.

    ![Azure Database for MySQL summary](./media/howto-double-encryption/infrastructure-encryption-summary.png)

5. Once the server is created you can validate the infrastructure double encryption by checking the status in the **Data encryption** server blade.

    ![Azure Database for MySQL validation](./media/howto-double-encryption/infrastructure-encryption-validation.png)

## Create an Azure Database for MySQL server with Infrastructure Double encryption - CLI

Follow these steps to create an Azure Database for MySQL server with Infrastructure double encryption from CLI:

This example creates a resource group named `myresourcegroup` in the `westus` location.

```azurecli-interactive
az group create --name myresourcegroup --location westus
```
The following example creates a MySQL 5.7 server in West US named `mydemoserver` in your resource group `myresourcegroup` with server admin login `myadmin`. This is a **Gen 4** **General Purpose** server with **2 vCores**. This will also enabled infrastructure double encryption for the server created. Substitute the `<server_admin_password>` with your own value.

```azurecli-interactive
az mysql server create --resource-group myresourcegroup --name mydemoserver  --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 5.7 --infrastructure-encryption <Enabled/Disabled>
```

## Next steps

 To learn more about data encryption, see [Azure Database for MySQL data Infrastructure double encryption](concepts-Infrastructure-double-encryption.md).
