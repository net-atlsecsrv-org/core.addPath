---
title: "Quickstart: Create an instance using the Azure portal"
titleSuffix: Azure Database Migration Service
description: Use the Azure portal to create an instance of Azure Database Migration Service.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: "seo-lt-2019"
ms.topic: quickstart
ms.date: 07/21/2020
---

# Quickstart: Create an instance of the Azure Database Migration Service by using the Azure portal

In this Quickstart, you use the Azure portal to create an instance of Azure Database Migration Service.  After you create the instance, you can use it to migrate data from SQL Server to Azure SQL Database.

If you don't have an Azure subscription, create a [free](https://azure.microsoft.com/free/) account before you begin.

## Sign in to the Azure portal

Open your web browser, navigate to the [Microsoft Azure portal](https://portal.azure.com/), and then enter your credentials to sign in to the portal.

The default view is your service dashboard.

> [!NOTE]
> You can create up to 10 instances of DMS per subscription. If you require a greater number of instances, please create a support ticket.

## Register the resource provider

Register the Microsoft.DataMigration resource provider before you create your first instance of the Database Migration Service.

1. In the Azure portal, select **All services**, and then select **Subscriptions**.

2. Select the subscription in which you want to create the instance of Azure Database Migration Service, and then select **Resource providers**.

3. Search for migration, and then to the right of **Microsoft.DataMigration**, select **Register**.

    ![Register resource provider](media/quickstart-create-data-migration-service-portal/dms-register-provider.png)

## Create an instance of the service

1. Select +**Create a resource** to create an instance of Azure Database Migration Service.

2. Search the marketplace for "migration", select **Azure Database Migration Service**, and then on the **Azure Database Migration Service** screen, select **Create**.

3. On the **Create Migration Service** screen:

    - Choose a **Service Name** that is memorable and unique to identify your instance of Azure Database Migration Service.
    - Select the Azure **Subscription** in which you want to create the instance.
    - Select an existing **Resource Group** or create a new one.
    - Choose the **Location** that is closest to your source or target server.
    - Select an existing **Virtual network** or create one.

        The virtual network provides Azure Database Migration Service with access to the source database and target environment.

        For more information on how to create a virtual network in the Azure portal, see the article [Create a virtual network using the Azure portal](https://aka.ms/vnet).

    - Select Basic: 1 vCore for the **Pricing tier**.

        ![Create migration service](media/quickstart-create-data-migration-service-portal/dms-create-service1.png)

4. Select **Create**.

    After a few moments, your instance of Azure Database Migration service is created and ready to use. Azure Database Migration Service displays as shown in the following image:

    ![Migration service created](media/quickstart-create-data-migration-service-portal/dms-service-created.png)

## Clean up resources

You can clean up the resources created in this Quickstart by deleting the [Azure resource group](../azure-resource-manager/management/overview.md). To delete the resource group, navigate to the instance of the Azure Database Migration Service that you created. Select the **Resource group** name, and then select **Delete resource group**. This action deletes all assets in the resource group as well as the group itself.

## Next steps

> [!div class="nextstepaction"]
> [Migrate SQL Server to Azure SQL Database](tutorial-sql-server-to-azure-sql.md)
