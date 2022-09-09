---
title: Configure server parameters - Azure portal - Azure Database for MySQL Flexible Server
description: This article describes how to configure MySQL server parameters in Azure Database for MySQL flexible server using the Azure portal.
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: how-to
ms.date: 11/10/2020
---

# Configure server parameters in Azure Database for MySQL - Flexible Server using the Azure portal

> [!IMPORTANT] 
> Azure Database for MySQL - Flexible Server is currently in public preview.

You can manage Azure Database for MySQL Flexible Server configuration using server parameters. The server parameters are configured with the default and recommended value when you create the server.  

This article describes how to view and configure server parameters by using the Azure portal. The server parameter blade on Azure portal shows both the modifiable and non-modifiable server parameter. The non-modifiable server parameters are greyed out.

>[!Note]
> Server parameters can be updated globally at the server-level, use the [Azure CLI](./how-to-configure-server-parameters-cli.md) or [Azure portal](./how-to-configure-server-parameters-portal.md).

## Configure server parameters

1. Sign in to the [Azure portal](https://portal.azure.com), then locate your Azure Database for MySQL flexible server.
2. Under the **SETTINGS** section, click **Server parameters** to open the server parameters page for the Azure Database for MySQL flexible server.
[:::image type="content" source="./media/how-to-server-parameters/azure-portal-server-parameters.png" alt-text="Azure portal server parameters page":::](./media/how-to-server-parameters/azure-portal-server-parameters.png#lightbox)
3. Locate any server parameter you need to adjust. Review the **Description** column to understand the purpose and allowed values.
[:::image type="content" source="./media/how-to-server-parameters/3-toggle-parameter.png" alt-text="Enumerate drop down":::](./media/how-to-server-parameters/3-toggle-parameter.png#lightbox)
4. Click  **Save** to save your changes.
[:::image type="content" source="./media/how-to-server-parameters/4-save-parameters.png" alt-text="Save or Discard changes":::](./media/how-to-server-parameters/4-save-parameters.png#lightbox)
5. The static parameters are the one which require server reboot to take in effect. If you are modifying the static parameter, you will be prompted to **Restart now** or **Restart later**.
[:::image type="content" source="./media/how-to-server-parameters/5-save-parameter.png" alt-text="Restart on static parameter save":::](./media/how-to-server-parameters/5-save-parameter.png#lightbox)
6. If you have saved new values for the parameters, you can always revert everything back to the default values by selecting **Reset all to default**.
[:::image type="content" source="./media/how-to-server-parameters/6-reset-parameters.png" alt-text="Reset all to default":::](./media/how-to-server-parameters/6-reset-parameters.png#lightbox)

## Setting non-modifiable server parameters

If the server parameter you want to update is non-modifiable, you can optionally set the parameter at the connection level using `init_connect`. This sets the server parameters for each client connecting to the server. 

1. Under the **SETTINGS** section, click **Server parameters** to open the server parameters page for the Azure Database for MySQL server.
2. Search for `init_connect`
3. Add the server parameters in the format: `SET parameter_name=YOUR_DESIRED_VALUE` in value the value column.

    For example, you can change the character set of your server by setting of `init_connect` to `SET character_set_client=utf8;SET character_set_database=utf8mb4;SET character_set_connection=latin1;SET character_set_results=latin1;`
4. Click **Save** to save your changes.

>[!Note]
> `init_connect` can be used to change parameters that do not require SUPER privilege(s) at the session level. To verify if you can set the parameter using `init_connect`, execute the `set session parameter_name=YOUR_DESIRED_VALUE;` command and if it errors out with **Access denied; you need SUPER privileges(s)** error, then you cannot set the parameter using `init_connect'.

## Working with the time zone parameter

### Populating the time zone tables

The time zone tables on your server can be populated by calling the `mysql.az_load_timezone` stored procedure from a tool like the MySQL command line or MySQL Workbench.

> [!NOTE]
> If you are running the `mysql.az_load_timezone` command from MySQL Workbench, you may need to turn off safe update mode first using `SET SQL_SAFE_UPDATES=0;`.

```sql
CALL mysql.az_load_timezone();
```

> [!IMPORTANT]
> You should restart the server to ensure the time zone tables are properly populated.<!-- FIX ME To restart the server, use the [Azure portal](how-to-restart-server-portal.md) or [CLI](how-to-restart-server-cli.md).-->

To view available time zone values, run the following command:

```sql
SELECT name FROM mysql.time_zone_name;
```

### Setting the global level time zone

The global level time zone can be set from the **Server parameters** page in the Azure portal. The below sets the global time zone to the value "US/Pacific".

[:::image type="content" source="./media/how-to-server-parameters/timezone.png" alt-text="Set time zone parameter":::](./media/how-to-server-parameters/timezone.png#lightbox)

### Setting the session level time zone

The session level time zone can be set by running the `SET time_zone` command from a tool like the MySQL command line or MySQL Workbench. The example below sets the time zone to the **US/Pacific** time zone.

```sql
SET time_zone = 'US/Pacific';
```

Refer to the MySQL documentation for [Date and Time Functions](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_convert-tz).

## Next steps

- How to configure [server parameters in Azure CLI](./how-to-configure-server-parameters-cli.md)
