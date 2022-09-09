---
title: Azure SQL Database and Data Warehouse IP firewall rules | Microsoft Docs
description: Learn how to configure a SQL database or SQL Data Warehouse firewall with server-level IP firewall rules to manage access, and configure a single or pooled database with database-level IP firewall rules.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: 
ms.devlang: 
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: carlrab
ms.date: 03/12/2019
---
# Azure SQL Database and SQL Data Warehouse IP firewall rules

> [!NOTE]
> This article applies to Azure SQL server, and to both SQL Database and SQL Data Warehouse databases that are created on the Azure SQL server. For simplicity, SQL Database is used when referring to both SQL Database and SQL Data Warehouse.

> [!IMPORTANT]
> This article does *not* apply to **Azure SQL Database Managed Instance**. for more information about the networking configuration, see [connecting to a Managed Instance](sql-database-managed-instance-connect-app.md) .

When you create new Azure SQL Server named for example *mysqlserver*, the SQL Database firewall blocks all access to the public endpoint for the server (accessible at *mysqlserver.database.windows.net*). 

> [!IMPORTANT]
> SQL Data Warehouse only supports server-level IP firewall rules and does not support database-level IP firewall rules.

## Understanding how the Firewall works
Connection attempts from the internet and Azure must first pass through the firewall before they can reach your Azure SQL server or SQL Database, as shown in the following diagram:

   ![Diagram describing firewall configuration.][1]

- **Server-level IP firewall rules:**

  These rules enable clients to access your entire Azure SQL server, that is, all the databases within the same SQL Database server. These rules are stored in the **master** database. Server-level IP firewall rules can be configured by using the portal or by using Transact-SQL statements. To create server-level IP firewall rules using the Azure portal or PowerShell, you must be the subscription owner or a subscription contributor. To create a server-level IP firewall rule using Transact-SQL, you must connect to the SQL Database instance as the server-level principal login or the Azure Active Directory administrator (which means that a server-level IP firewall rule must first be created by a user with Azure-level permissions).

- **Database-level IP firewall rules:**

  These rules enable clients to access certain (secure) databases within the same SQL Database server. You can create these rules for each database (including the **master** database) and they are stored in the individual databases. Database-level IP firewall rules for master and user databases can only be created and managed by using Transact-SQL statements and only after you have configured the first server-level firewall. If you specify an IP address range in the database-level IP firewall rule that is outside the range specified in the server-level IP firewall rule, only those clients that have IP addresses in the database-level range can access the database. You can have a maximum of 128 database-level IP firewall rules for a database. For more information on configuring database-level IP firewall rules, see the example later in this article and see [sp_set_database_firewall_rule (Azure SQL Database)](https://msdn.microsoft.com/library/dn270010.aspx).

### Recommendations on how to set Firewall rules 

Microsoft recommends using database-level IP firewall rules whenever possible to enhance security and to make your database more portable. Use server-level IP firewall rules for administrators and when you have many databases that have the same access requirements and you don't want to spend time configuring each database individually.


> [!IMPORTANT]
> Windows Azure SQL Database supports a maximum of 128 IP firewall rules.
> [!NOTE]
> For information about portable databases in the context of business continuity, see [Authentication requirements for disaster recovery](sql-database-geo-replication-security-config.md).

## Server-level versus database-level IP firewall rules

Q. Should users of one database be fully isolated from another database?
  If yes, grant access using database-level IP firewall rules. This avoids using server-level IP firewall rules, which permit access through the firewall to all databases, reducing the depth of your defenses.

Q. Do users at the IP address’s need access to all databases?
  Use server-level IP firewall rules to reduce the number of times you must configure IP firewall rules.

Q. Does the person or team configuring the IP firewall rules only have access through the Azure portal, PowerShell, or the REST API?
  You must use server-level IP firewall rules. Database-level IP firewall rules can only be configured using Transact-SQL.  

Q. Is the person or team configuring the IP firewall rules prohibited from having high-level permission at the database level?
  Use server-level IP firewall rules. Configuring database-level IP firewall rules using Transact-SQL, requires at least `CONTROL DATABASE` permission at the database level.  

Q. Is the person or team configuring or auditing the IP firewall rules, centrally managing IP firewall rules for many (perhaps 100s) of databases?
  This selection depends upon your needs and environment. Server-level IP firewall rules might be easier to configure, but scripting can configure rules at the database-level. And even if you use server-level IP firewall rules, you might need to audit the database-level IP firewall rules, to see if users with `CONTROL` permission on the database have created database-level IP firewall rules.

Q. Can I use a mix of both server-level and database-level IP firewall rules?
  Yes. Some users, such as administrators might need server-level IP firewall rules. Other users, such as users of a database application, might need database-level IP firewall rules.

### Connections from the internet

When a computer attempts to connect to your database server from the internet, the firewall first checks the originating IP address of the request against the database-level IP firewall rules, for the database that the connection is requesting:

- If the IP address of the request is within one of the ranges specified in the database-level IP firewall rules, the connection is granted to the SQL Database that contains the rule.
- If the IP address of the request is not within one of the ranges specified in the database-level IP firewall rule, the server-level IP firewall rules are checked. If the IP address of the request is within one of the ranges specified in the server-level IP firewall rules, the connection is granted. Server-level IP firewall rules apply to all SQL databases on the Azure SQL server.  
- If the IP address of the request is not within the ranges specified in any of the database-level or server-level IP firewall rules, the connection request fails.

> [!NOTE]
> To access Azure SQL Database from your local computer, ensure the firewall on your network and local computer allows outgoing communication on TCP port 1433.

### Connections from inside Azure

To allow applications hosted inside Azure to connect to your Azure SQL server, Azure connections must be enabled. When an application from Azure attempts to connect to your database server, the firewall verifies that Azure connections are allowed. A firewall setting with starting and ending address equal to 0.0.0.0 indicates Azure connections are allowed. If the connection attempt is not allowed, the request does not reach the Azure SQL Database server.

> [!IMPORTANT]
> This option configures the firewall to allow all connections from Azure including connections from the subscriptions of other customers. When selecting this option, make sure your login and user permissions limit access to only authorized users.

## Create and manage IP firewall rules

The first server-level firewall setting can be created using the [Azure portal](https://portal.azure.com/) or programmatically using [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.sql), [Azure CLI](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create), or the [REST API](https://docs.microsoft.com/rest/api/sql/firewallrules/createorupdate). Subsequent server-level IP firewall rules can be created and managed using these methods, and through Transact-SQL.

> [!IMPORTANT]
> Database-level IP firewall rules can only be created and managed using Transact-SQL.

To improve performance, server-level IP firewall rules are temporarily cached at the database level. To refresh the cache, see [DBCC FLUSHAUTHCACHE](https://msdn.microsoft.com/library/mt627793.aspx).

> [!TIP]
> You can use [SQL Database Auditing](sql-database-auditing.md) to audit server-level and database-level firewall changes.

## Manage server-level IP firewall rules using the Azure portal

To set a server-level IP firewall rule in the Azure portal, you can either go to the Overview page for your Azure SQL database or the Overview page for your SQL Database server.

> [!TIP]
> For a tutorial, see [Create a DB using the Azure portal](sql-database-single-database-get-started.md).

### From database overview page

1. To set a server-level IP firewall rule from the database overview page, click **Set server firewall** on the toolbar as shown in the following image: The **Firewall settings** page for the SQL Database server opens.

      ![server IP firewall rule](./media/sql-database-get-started-portal/server-firewall-rule.png)

2. Click **Add client IP** on the toolbar to add the IP address of the computer you are currently using and then click **Save**. A server-level IP firewall rule is created for your current IP address.

      ![set server-level IP firewall rule](./media/sql-database-get-started-portal/server-firewall-rule-set.png)

### From server overview page

The overview page for your server opens, showing you the fully qualified server name (such as **mynewserver20170403.database.windows.net**) and provides options for further configuration.

1. To set a server-level rule from server overview page, click **Firewall** in the left-hand menu under Settings:

2. Click **Add client IP** on the toolbar to add the IP address of the computer you are currently using and then click **Save**. A server-level IP firewall rule is created for your current IP address.

## Manage IP firewall rules using Transact-SQL

| Catalog View or Stored Procedure | Level | Description |
| --- | --- | --- |
| [sys.firewall_rules](https://msdn.microsoft.com/library/dn269980.aspx) |Server |Displays the current server-level IP firewall rules |
| [sp_set_firewall_rule](https://msdn.microsoft.com/library/dn270017.aspx) |Server |Creates or updates server-level IP firewall rules |
| [sp_delete_firewall_rule](https://msdn.microsoft.com/library/dn270024.aspx) |Server |Removes server-level IP firewall rules |
| [sys.database_firewall_rules](https://msdn.microsoft.com/library/dn269982.aspx) |Database |Displays the current database-level IP firewall rules |
| [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx) |Database |Creates or updates the database-level IP firewall rules |
| [sp_delete_database_firewall_rule](https://msdn.microsoft.com/library/dn270030.aspx) |Databases |Removes database-level IP firewall rules |

The following examples review the existing rules, enable a range of IP addresses on the server Contoso, and deletes an IP firewall rule:

```sql
SELECT * FROM sys.firewall_rules ORDER BY name;
```

Next, add a server-level IP firewall rule.

```sql
EXECUTE sp_set_firewall_rule @name = N'ContosoFirewallRule',
   @start_ip_address = '192.168.1.1', @end_ip_address = '192.168.1.10'
```

To delete a server-level IP firewall rule, execute the sp_delete_firewall_rule stored procedure. The following example deletes the rule named ContosoFirewallRule:

```sql
EXECUTE sp_delete_firewall_rule @name = N'ContosoFirewallRule'
```

## Manage server-level IP firewall rules using Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> The PowerShell Azure Resource Manager module is still supported by Azure SQL Database, but all future development is for the Az.Sql module. For these cmdlets, see [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). The arguments for the commands in the Az module and in the AzureRm modules are substantially identical.

| Cmdlet | Level | Description |
| --- | --- | --- |
| [Get-AzSqlServerFirewallRule](/powershell/module/az.sql/get-azsqlserverfirewallrule) |Server |Returns the current server-level firewall rules |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) |Server |Creates a new server-level firewall rule |
| [Set-AzSqlServerFirewallRule](/powershell/module/az.sql/set-azsqlserverfirewallrule) |Server |Updates the properties of an existing server-level firewall rule |
| [Remove-AzSqlServerFirewallRule](/powershell/module/az.sql/remove-azsqlserverfirewallrule) |Server |Removes server-level firewall rules |

The following example sets a server-level IP firewall rule using PowerShell:

```powershell
New-AzSqlServerFirewallRule -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -FirewallRuleName "AllowSome" -StartIpAddress "0.0.0.0" -EndIpAddress "0.0.0.0"
```

> [!TIP]
> For PowerShell examples in the context of a quick start, see [Create DB - PowerShell](sql-database-powershell-samples.md) and [Create a single database and configure a SQL Database server-level IP firewall rule using PowerShell](scripts/sql-database-create-and-configure-database-powershell.md)

## Manage server-level IP firewall rules using Azure CLI

| Cmdlet | Level | Description |
| --- | --- | --- |
|[az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create)|Server|Creates a server IP firewall rule|
|[az sql server firewall-rule list](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-list)|Server|Lists the IP firewall rules on a server|
|[az sql server firewall-rule show](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-show)|Server|Shows the detail of an IP firewall rule|
|[az sql server firewall-rule update](/cli/azure/sql/server/firewall-rule##az-sql-server-firewall-rule-update)|Server|Updates an IP firewall rule|
|[az sql server firewall-rule delete](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-delete)|Server|Deletes an IP firewall rule|

The following example sets a server-level IP firewall rule using the Azure CLI:

```azurecli-interactive
az sql server firewall-rule create --resource-group myResourceGroup --server $servername \
-n AllowYourIp --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP]
> For an Azure CLI example in the context of a quick start, see [Create DB - Azure CLI](sql-database-cli-samples.md) and [Create a single database and configure a SQL Database IP firewall rule using the Azure CLI](scripts/sql-database-create-and-configure-database-cli.md)

## Manage server-level IP firewall rules using REST API

| API | Level | Description |
| --- | --- | --- |
| [List Firewall Rules](https://docs.microsoft.com/rest/api/sql/firewallrules/listbyserver) |Server |Displays the current server-level IP firewall rules |
| [Create or Update Firewall Rule](https://docs.microsoft.com/rest/api/sql/firewallrules/createorupdate) |Server |Creates or updates server-level IP firewall rules |
| [Delete Firewall Rule](https://docs.microsoft.com/rest/api/sql/firewallrules/delete) |Server |Removes server-level IP firewall rules |
| [Get Firewall Rules](https://docs.microsoft.com/rest/api/sql/firewallrules/get) | Server | Gets server-level IP firewall rules |



## Troubleshooting the database firewall

Consider the following points when access to the Microsoft Azure SQL Database service does not behave as you expect:

- **Local firewall configuration:**

  Before your computer can access Azure SQL Database, you may need to create a firewall exception on your computer for TCP port 1433. If you are making connections inside the Azure cloud boundary, you may have to open additional ports. For more information, see the **SQL Database: Outside vs inside** section of [Ports beyond 1433 for ADO.NET 4.5 and SQL Database](sql-database-develop-direct-route-ports-adonet-v12.md).

- **Network address translation (NAT):**

  Due to NAT, the IP address used by your computer to connect to Azure SQL Database may be different than the IP address shown in your computer IP configuration settings. To view the IP address your computer is using to connect to Azure, log in to the portal and navigate to the **Configure** tab on the server that hosts your database. Under the **Allowed IP Addresses** section, the **Current Client IP Address** is displayed. Click **Add** to the **Allowed IP Addresses** to allow this computer to access the server.

- **Changes to the allow list have not taken effect yet:**

  There may be as much as a five-minute delay for changes to the Azure SQL Database firewall configuration to take effect.

- **The login is not authorized or an incorrect password was used:**

  If a login does not have permissions on the Azure SQL Database server or the password used is incorrect, the connection to the Azure SQL Database server is denied. Creating a firewall setting only provides clients with an opportunity to attempt connecting to your server; each client must provide the necessary security credentials. For more information about preparing logins, see [Managing Databases, Logins, and Users in Azure SQL Database](sql-database-manage-logins.md).

- **Dynamic IP address:**

  If you have an internet connection with dynamic IP addressing and you are having trouble getting through the firewall, you could try one of the following solutions:
  
  - Ask your internet Service Provider (ISP) for the IP address range assigned to your client computers that access the Azure SQL Database server, and then add the IP address range as an IP firewall rule.
  - Get static IP addressing instead for your client computers, and then add the IP addresses as IP firewall rules.

## Next steps

- Confirm your corporate network environment allows inbound communication from the Compute IP address ranges (including SQL ranges) used by the Microsoft Azure Datacenters. It may be necessary to whitelist these IP addresses, see [Microsoft Azure Datacenter IP Ranges](https://www.microsoft.com/download/details.aspx?id=41653)  
- For a quick start on creating a server-level IP firewall rule, see [Create an Azure SQL database](sql-database-single-database-get-started.md).
- For help in connecting to an Azure SQL database from open source or third-party applications, see [Client quick-start code samples to SQL Database](https://msdn.microsoft.com/library/azure/ee336282.aspx).
- For information on additional ports that you may need to open, see the **SQL Database: Outside vs inside** section of [Ports beyond 1433 for ADO.NET 4.5 and SQL Database](sql-database-develop-direct-route-ports-adonet-v12.md)
- For an overview of Azure SQL Database security, see [Securing your database](sql-database-security-overview.md)

<!--Image references-->
[1]: ./media/sql-database-firewall-configure/sqldb-firewall-1.png
