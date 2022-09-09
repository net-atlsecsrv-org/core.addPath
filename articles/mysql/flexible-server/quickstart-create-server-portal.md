---
title: 'Quickstart: Create an Azure Database for MySQL flexible server - Azure portal'
description: This article walks you through using the Azure portal to create an Azure Database for MySQL flexible server in minutes. 
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 10/22/2020
---

# Quickstart: Use the Azure portal to create an Azure Database for MySQL flexible server

Azure Database for MySQL Flexible Server is a managed service that you can use to run, manage, and scale highly available MySQL servers in the cloud. This quickstart shows you how to create a flexible server by using the Azure portal.

> [!IMPORTANT] 
> Azure Database for MySQL Flexible Server is currently in public preview.

If you don't have an Azure subscription, create a [free Azure account](https://azure.microsoft.com/free/) before you begin.

## Sign in to the Azure portal
Go to the [Azure portal](https://portal.azure.com/). Enter your credentials to sign in to the portal. The default view is your service dashboard.

## Create an Azure Database for MySQL flexible server

You create a flexible server with a defined set of [compute and storage resources](./concepts-compute-storage.md). You create the server within an [Azure resource group](../../azure-resource-manager/management/overview.md).

Complete these steps to create a flexible server:

1. Search for and select **Azure Database for MySQL servers** in the portal:
    
    > :::image type="content" source="./media/quickstart-create-server-portal/find-mysql-portal.png" alt-text="Screenshot that shows a search for Azure Database for MySQL servers.":::

2. Select **Add**. 

3. On the **Select Azure Database for MySQL deployment option** page, select **Flexible server** as the deployment option:
     
    > :::image type="content" source="./media/quickstart-create-server-portal/deployment-option.png" alt-text="Screenshot that shows the Flexible server option.":::    

4. On the **Basics** tab, enter the following information: 

    > :::image type="content" source="./media/quickstart-create-server-portal/create-form.png" alt-text="Screenshot that shows the Basics tab of the Flexible server page."::: 
                                    
    |**Setting**|**Suggested value**|**Description**|
    |---|---|---|
    Subscription|Your subscription name|The Azure subscription that you want to use for your server. If you have multiple subscriptions, choose the subscription in which you want to be billed for the resource.|
    Resource group|**myresourcegroup**| A new resource group name or an existing one from your subscription.|
    Server name |**mydemoserver**|A unique name that identifies your flexible server. The domain name `mysql.database.azure.com` is appended to the server name you provide. The server name can contain only lowercase letters, numbers, and the hyphen (-) character. It must contain between 3 and 63 characters.|
    Admin username |**mydemouser**| Your own sign-in account to use when you connect to the server. The admin user name can't be **azure_superuser**, **admin**, **administrator**, **root**, **guest**, or **public**.|
    Password |Your password| A new password for the server admin account. It must contain between 8 and 128 characters. It must also contain characters from three of the following categories: English uppercase letters, English lowercase letters, numbers (0 through 9), and non-alphanumeric characters (!, $, #, %, and so on).|
    Region|The region closest to your users| The location that's closest to your users.|
    Version|**5.7**| A MySQL major version.|
    Compute + storage | **Burstable**, **Standard_B1ms**, **10 GiB**, **7 days** | The compute, storage, and backup configurations for your new server. Select **Configure server**. **Burstable**, **Standard_B1ms**, **10 GiB**, and **7 days** are the default values for **Compute tier**, **Compute size**, **Storage size**, and backup **Retention period**. You can leave those values as is or adjust them. To save the compute and storage selection, select **Save** to continue with the configuration. The following screenshot shows the compute and storage options.|
    
    > :::image type="content" source="./media/quickstart-create-server-portal/compute-storage.png" alt-text="Screenshot that shows compute and storage options.":::

5. Configure networking options.

    On the **Networking** tab, you can choose how your server is reachable. Azure Database for MySQL Flexible Server provides two ways to connect to your server: 
   - Public access (allowed IP addresses)
   - Private access (VNet Integration) 
   
   When you use public access, access to your server is limited to allowed IP addresses that you add to a firewall rule. This method prevents external applications and tools from connecting to the server and any databases on the server, unless you create a rule to open the firewall for a specific IP address or range. When you use private access (VNet Integration), access to your server is limited to your virtual network. In this quickstart, you'll learn how to enable public access to connect to the server. [Learn more about connectivity methods in the concepts article.](./concepts-networking.md)

    > [!NOTE]
    > You can't change the connectivity method after you create the server. For example, if you select **Public access (allowed IP addresses)** when you create the server, you can't change to **Private access (VNet Integration)** after the server is created. We highly recommend that you create your server with private access to help secure access to your server via VNet Integration. [Learn more about private access in the concepts article.](./concepts-networking.md)

    > :::image type="content" source="./media/quickstart-create-server-portal/networking.png" alt-text="Screenshot that shows the Networking tab.":::  

6. Select **Review + create** to review your flexible server configuration.

7. Select **Create** to provision the server. Provisioning can take a few minutes.

8. Select **Notifications** on the toolbar (the bell button) to monitor the deployment process. After the deployment is done, you can select **Pin to dashboard**, which creates a tile for the flexible server on your Azure portal dashboard. This tile is a shortcut to the server's **Overview** page. When you select **Go to resource**, the server's **Overview** page opens.

By default, these databases are created under your server: information_schema, mysql, performance_schema, and sys.

> [!NOTE]
> To avoid connectivity problems, check if your network allows outbound traffic over port 3306, which is used by Azure Database for MySQL Flexible Server.  

## Connect to the server by using mysql.exe

If you created your flexible server by using private access (VNet Integration), you'll need to connect to your server from a resource within the same virtual network as your server. You can create a virtual machine and add it to the virtual network created with your flexible server.

If you created your flexible server by using public access (allowed IP addresses), you can add your local IP address to the list of firewall rules on your server.

You can use either [mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) or [MySQL Workbench](./connect-workbench.md) to connect to the server from your local environment. 

If you're using mysql.exe, connect by using the following command. Use your server name, user name, and password in the command. 

```bash
 mysql -h mydemoserver.mysql.database.azure.com -u mydemouser -p
```
## Clean up resources
You have now created an Azure Database for MySQL flexible server in a resource group. If you don't expect to need these resources in the future, you can delete them by deleting the resource group, or you can just delete the MySQL server. To delete the resource group, complete these steps:

1. In the Azure portal, search for and select **Resource groups**.
1. In the list of resource groups, select the name of your resource group.
1. In the **Overview** page for your resource group, select **Delete resource group**.
1. In the confirmation dialog box, type the name of your resource group, and then select **Delete**.

To delete the server, you can select **Delete** on **Overview** page for your server, as shown here:

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstart-create-server-portal/delete-server.png" alt-text="Screenshot that shows how to delete a server.":::

## Next steps

> [!div class="nextstepaction"]
> [Build a PHP (Laravel) web app with MySQL](tutorial-php-database-app.md)