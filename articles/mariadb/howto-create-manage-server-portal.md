---
title: Manage server - Azure portal - Azure Database for MariaDB
description: Learn how to manage an Azure Database for MariaDB server from the Azure portal.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 3/18/2020
---

# Manage an Azure Database for MariaDB server using the Azure portal
This article shows you how to manage your Azure Database for MariaDB servers. Management tasks include compute and storage scaling, admin password reset, and viewing server details.

## Sign in
Sign in to the [Azure portal](https://portal.azure.com).

## Create a server
Visit the [quickstart](quickstart-create-mariadb-server-database-using-azure-portal.md) to learn how to create and get started with an Azure Database for MariaDB server.

## Scale compute and storage

After server creation you can scale between the General Purpose and Memory Optimized tiers as your needs change. You can also scale compute and memory by increasing or decreasing vCores. Storage can be scaled up (however, you cannot scale storage down).

### Scale between General Purpose and Memory Optimized tiers

You can scale from General Purpose to Memory Optimized and vice-versa. Changing to and from the Basic tier after server creation is not supported. 

1. Select your server in the Azure portal. Select **Pricing tier**, located in the **Settings** section.

2. Select **General Purpose** or **Memory Optimized**, depending on what you are scaling to. 

    ![Screenshot shows the Azure portal with Pricing tier selected and a value of Memory Optimized selected.](./media/howto-create-manage-server-portal/change-pricing-tier.png)

    > [!NOTE]
    > Changing tiers causes a server restart.

4. Select **OK** to save changes.


### Scale vCores up or down

1. Select your server in the Azure portal. Select **Pricing tier**, located in the **Settings** section.

2. Change the **vCore** setting by moving the slider to your desired value.

    ![scale-compute](./media/howto-create-manage-server-portal/scaling-compute.png)

    > [!NOTE]
    > Scaling vCores causes a server restart.

3. Select **OK** to save changes.


### Scale storage up

1. Select your server in the Azure portal. Select **Pricing tier**, located in the **Settings** section.

2. Change the **Storage** setting by moving the slider up to your desired value.

    ![scale-storage](./media/howto-create-manage-server-portal/scaling-storage.png)

    > [!NOTE]
    > Storage cannot be scaled down.

3. Select **OK** to save changes.


## Update admin password
You can change the administrator role's password using the Azure portal.

1. Select your server in the Azure portal. In the **Overview** window select **Reset password**.

   ![overview](./media/howto-create-manage-server-portal/overview-reset-password.png)

2. Enter a new password and confirm the password. The textbox will prompt you about password complexity requirements.

   ![Screenshot shows the Reset the password dialog box with Password and Confirm password.](./media/howto-create-manage-server-portal/reset-password.png)

3. Select **OK** to save the new password.


## Delete a server

You can delete your server if you no longer need it. 

1. Select your server in the Azure portal. In the **Overview** window select **Delete**.

    ![delete](./media/howto-create-manage-server-portal/overview-delete.png)

2. Type the name of the server into the input box to confirm that this is the server you want to delete.

    ![Screenshot shows a dialog that verifies whether you want to delete a database, which is irreversible.](./media/howto-create-manage-server-portal/confirm-delete.png)

    > [!NOTE]
    > Deleting a server is irreversible.

3. Select **Delete**.


## Next steps
- Learn about [backups and server restore](howto-restore-server-portal.md)
- Learn about [tuning and monitoring options in Azure Database for MariaDB](concepts-monitoring.md)
