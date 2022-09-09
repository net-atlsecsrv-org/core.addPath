---
title: 'Quickstart: Create a server - Azure portal - Azure Database for MySQL'
description: This article walks you through using the Azure portal to create a sample Azure Database for MySQL server in about five minutes.
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 11/04/2020
---

# Quickstart: Create an Azure Database for MySQL server by using the Azure portal

Azure Database for MySQL is a managed service that you use to run, manage, and scale highly available MySQL databases in the cloud. This quickstart shows you how to use the Azure portal to create an Azure Database for MySQL single server. It also shows you how to connect to the server.

## Prerequisites
An Azure subscription is required. If you don't have an Azure subscription, create a [free Azure account](https://azure.microsoft.com/free/) before you begin.

## Create an Azure Database for MySQL single server
1. Go to the [Azure portal](https://portal.azure.com/) to create a MySQL Single Server database. Search for and select **Azure Database for MySQL**:

   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/find-azure-mysql-in-portal.png" alt-text="Find Azure Database for MySQL":::

1. Select **Add**.

2. On the **Select Azure Database for MySQL deployment option** page, select  **Single server**:
   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/choose-singleserver.png" alt-text="Screenshot that shows the Single server option.":::

3. Enter the basic settings for a new single server:

   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/4-create-form.png" alt-text="Screenshot that shows the Create MySQL server page.":::

   **Setting** | **Suggested value** | **Description**
   ---|---|---
   Subscription | Your subscription | Select the desired Azure subscription.
   Resource group | **myresourcegroup** | Enter a new resource group or an existing one from your subscription.
   Server name | **mydemoserver** | Enter a unique name. The server name can contain only lowercase letters, numbers, and the hyphen (-) character. It must contain 3 to 63 characters.
   Data source |**None** | Select **None** to create a new server from scratch. Select **Backup** only if you're restoring from a geo-backup of an existing server.
   Location |Your desired location | Select a location from the list.
   Version | The latest major version| Use the latest major version. See [all supported versions](../postgresql/concepts-supported-versions.md).
   Compute + storage | Use the defaults| The default pricing tier is **General Purpose** with **4 vCores** and **100 GB** storage. Backup retention is set to **7 days**, with the **Geographically Redundant** backup option.<br/>Review the [pricing](https://azure.microsoft.com/pricing/details/mysql/) page, and update the defaults if you need to.
   Admin username | **mydemoadmin** | Enter your server admin user name. You can't use **azure_superuser**, **admin**, **administrator**, **root**, **guest**, or **public** for the admin user name.
   Password | A password | A new password for the server admin user. The password must be 8 to 128 characters long and contain a combination of uppercase or lowercase letters, numbers, and non-alphanumeric characters (!, $, #, %, and so on).
  

   > [!NOTE]
   > Consider using the Basic pricing tier if light compute and I/O are adequate for your workload. Note that servers created in the Basic pricing tier can't later be scaled to General Purpose or Memory Optimized.

4. Select **Review + create** to provision the server.

5. Wait for the portal page to display **Your deployment is complete**. Select **Go to resource** to go to the newly created server page:

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/deployment-complete.png" alt-text="Screenshot that shows the Your deployment is complete message.":::

[Having problems? Let us know.](https://aka.ms/mysql-doc-feedback)

## Configure a server-level firewall rule

By default, the new server is protected with a firewall. To connect, you must provide access to your IP by completing these steps:

1. Go to **Connection security** from the left pane for your server resource. If you don't know how to find your resource, see [How to open a resource](../azure-resource-manager/management/manage-resources-portal.md#open-resources).

   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/add-current-ip-firewall.png" alt-text="Screenshot that shows the Connection security > Firewall rules page.":::

2. Select **Add current client IP address**, and then select **Save**.

   > [!NOTE]
   > To avoid connectivity problems, check if your network allows outbound traffic over port 3306, which is used by Azure Database for MySQL. 

You can add more IPs or provide an IP range to connect to your server from those IPs. For more information, see [How to manage firewall rules on an Azure Database for MySQL server](./concepts-firewall-rules.md).


[Having problems? Let us know](https://aka.ms/mysql-doc-feedback)

## Connect to the server by using mysql.exe
You can use either [mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) or [MySQL Workbench](./connect-workbench.md) to connect to the server from your local environment. In this quickstart, we'll use mysql.exe in [Azure Cloud Shell](../cloud-shell/overview.md) to connect to the server.


1. Open Azure Cloud Shell in the portal by selecting the first button on the toolbar, as shown in the following screenshot. Note the server name, server admin name, and subscription for your new server in the **Overview** section, as shown in the screenshot.

    > [!NOTE]
    > If you're opening Cloud Shell for the first time, you'll be prompted to create a resource group and storage account. This is a one-time step. It will be automatically attached for all sessions.

   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/use-in-cloud-shell.png" alt-text="Screenshot that shows Cloud Shell in the Azure portal.":::
2. Run the following command in the Azure Cloud Shell terminal. Replace the values shown here with your actual server name and admin user name. For Azure Database for MySQL, you need to add `@\<servername>` to the admin user name, as shown here: 

      ```azurecli-interactive
      mysql --host=mydemoserver.mysql.database.azure.com --user=myadmin@mydemoserver -p
      ```

      Here's what it looks like in the Cloud Shell terminal:

      ```
      Requesting a Cloud Shell.Succeeded.
      Connecting terminal...

      Welcome to Azure Cloud Shell

      Type "az" to use Azure CLI
      Type "help" to learn about Cloud Shell

      user@Azure:~$mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
      Enter password:
      Welcome to the MySQL monitor.  Commands end with ; or \g.
      Your MySQL connection id is 64796
      Server version: 5.6.42.0 Source distribution

      Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.
      Oracle is a registered trademark of Oracle Corporation and/or its
      affiliates. Other names may be trademarks of their respective
      owners.

      Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
      mysql>
      ```
3. In the same Azure Cloud Shell terminal, create a database named `guest`:
      ```
      mysql> CREATE DATABASE guest;
      Query OK, 1 row affected (0.27 sec)
      ```
4. Switch to the `guest` database:
      ```
      mysql> USE guest;
      Database changed
      ```
5. Enter `quit`, and then select **Enter** to quit mysql.

[Having problems? Let us know.](https://aka.ms/mysql-doc-feedback)

## Clean up resources
You have now created an Azure Database for MySQL server in a resource group.  If you don't expect to need these resources in the future, you can delete them by deleting the resource group, or you can just delete the MySQL server. To delete the resource group, complete these steps:
1. In the Azure portal, search for and select **Resource groups**.
2. In the list of resource groups, select the name of your resource group.
3. On the **Overview** page for your resource group, select **Delete resource group**.
4. In the confirmation dialog box, type the name of your resource group, and then select **Delete**.

To delete the server, you can select **Delete** on the **Overview** page for your server, as shown here:
> [!div class="mx-imgBorder"]
> :::image type="content" source="media/quickstart-create-mysql-server-database-using-azure-portal/delete-server.png" alt-text="Screenshot that shows the Delete button on the server overview page.":::

## Next steps
> [!div class="nextstepaction"]
>[Build a PHP app on Windows with MySQL](../app-service/tutorial-php-mysql-app.md) <br/>

> [!div class="nextstepaction"]
>[Build PHP app on Linux with MySQL](../app-service/tutorial-php-mysql-app.md?pivots=platform-linux%3fpivots%3dplatform-linux)<br/><br/>

[Can't find what you're looking for? Let us know.](https://aka.ms/mysql-doc-feedback)