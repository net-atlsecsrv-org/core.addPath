---
title: "Tutorial: Add a single database to a failover group"
description: Add an Azure SQL Database single database to a failover group using the Azure portal, PowerShell, or Azure CLI.  
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: 
ms.devlang: 
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sstein, carlrab
ms.date: 06/19/2019
---
# Tutorial: Add an Azure SQL Database single database to a failover group

A [failover group](sql-database-auto-failover-group.md) is a declarative abstraction layer that allows you to group mulitple geo-replicated databases. Learn to configure a failover group for an Azure SQL Database single database and test failover using either the Azure portal, PowerShell, or Azure CLI.  In this tutorial, you will learn how to:

> [!div class="checklist"]
> - Create an Azure SQL Database single database.
> - Create a failover group for a single database between two logical SQL servers.
> - Test failover.

## Prerequisites

# [Portal](#tab/azure-portal)
To complete this tutorial, make sure you have: 

- An Azure subscription. [Create a free account](https://azure.microsoft.com/free/) if you don't already have one.


# [PowerShell](#tab/azure-powershell)
To complete the tutorial, make sure you have the following items:

- An Azure subscription. [Create a free account](https://azure.microsoft.com/free/) if you don't already have one.
- [Azure PowerShell](/powershell/azureps-cmdlets-docs)


# [Azure CLI](#tab/azure-cli)
To complete the tutorial, make sure you have the following items:

- An Azure subscription. [Create a free account](https://azure.microsoft.com/free/) if you don't already have one.
- The latest version of [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest). 

---

## 1 - Create a single database 

[!INCLUDE [sql-database-create-single-database](includes/sql-database-create-single-database.md)]

## 2 - Create the failover group 
In this step, you will create a [failover group](sql-database-auto-failover-group.md) between an existing Azure SQL server and a new Azure SQL server in another region. Then add the sample database to the failover group. 

# [Portal](#tab/azure-portal)
Create your failover group and add your single database to it using the Azure portal. 

1. Select **Azure SQL** in the left-hand menu of the [Azure portal](https://portal.azure.com). If **Azure SQL** is not in the list, select **All services**, then type Azure SQL in the search box. (Optional) Select the star next to **Azure SQL** to favorite it and add it as an item in the left-hand navigation. 
1. Select the single database created in section 1, such as `mySampleDatabase`. 
1. Failover groups can be configurd at the server level. Select the name of the server under **Server name** to open the settings for the server.

   ![Open server for single db](media/sql-database-single-database-failover-group-tutorial/open-sql-db-server.png)

1. Select **Failover groups** under the **Settings** pane, and then select **Add group** to create a new failover group. 

    ![Add new failover group](media/sql-database-single-database-failover-group-tutorial/sqldb-add-new-failover-group.png)

1. On the **Failover Group** page, enter or select the following values, and then select **Create**:
    - **Failover group name**: Type in a unique failover group name, such as `failovergrouptutorial`. 
    - **Secondary server**: Select the option to *configure required settings* and then choose to **Create a new server**. Alternatively, you can choose an already-existing server as the secondary server. After entering the following values, select **Select**. 
        - **Server name**: Type in a unique name for the secondary server, such as `mysqlsecondary`. 
        - **Server admin login**: Type `azureuser`
        - **Password**: Type a complex password that meets password requirements.
        - **Location**: Choose a location from the drop-down, such as `East US`. This location cannot be the same location as your primary server.

    > [!NOTE]
    > The server login and firewall settings must match that of your primary server. 
    
      ![Create a secondary server for the failover group](media/sql-database-single-database-failover-group-tutorial/create-secondary-failover-server.png)

   - **Databases within the group**: Once a secondary server is selected, this option becomes unlocked. Select it to **Select databases to add** and then choose the database you created in section 1. Adding the database to the failover group will automatically start the geo-replication process. 
        
    ![Add SQL DB to failover group](media/sql-database-single-database-failover-group-tutorial/add-sqldb-to-failover-group.png)
        

# [PowerShell](#tab/azure-powershell)
Create your failover group and add your single database to it using PowerShell. 

   > [!NOTE]
   > The server login and firewall settings must match that of your primary server. 

   ```powershell-interactive
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "West US"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   # $databaseName = "mySampleDatabase"
   $drLocation = "East US"
   $drServerName = "mysqlsecondary-$(Get-Random)"
   $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

   # The ip address range that you want to allow to access your server 
   # (leaving at 0.0.0.0 will prevent outside-of-azure connections to your DB)
   $startIp = "0.0.0.0"
   $endIp = "0.0.0.0"

   # Show randomized variables
   Write-host "DR Server name is" $drServerName 
   Write-host "Failover group name is" $failoverGroupName
   
   # Create a secondary server in the failover region
   Write-host "Creating a secondary logical server in the failover region..."
   $drServer = New-AzSqlServer -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -Location $drLocation `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
         -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   $drServer

   # Create a server firewall rule that allows access from the specified IP range
   Write-host "Configuring firewall for secondary logical server..."
   $serverFirewallRule = New-AzSqlServerFirewallRule -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FirewallRuleName "AllowedIPs" -StartIpAddress $startIp -EndIpAddress $endIp
   $serverFirewallRule   
   
   # Create a failover group between the servers
   $failovergroup = Write-host "Creating a failover group between the primary and secondary server..."
   New-AzSqlDatabaseFailoverGroup `
      –ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -PartnerServerName $drServerName  `
      –FailoverGroupName $failoverGroupName `
      –FailoverPolicy Automatic `
      -GracePeriodWithDataLossHours 2
   $failovergroup
   
   # Add the database to the failover group
   Write-host "Adding the database to the failover group..." 
   Get-AzSqlDatabase `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -DatabaseName $databaseName | `
   Add-AzSqlDatabaseToFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FailoverGroupName $failoverGroupName
   Write-host "Successfully added the database to the failover group..." 
   ```

This portion of the tutorial uses the following PowerShell cmdlets:

| Command | Notes |
|---|---|
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Creates a SQL Database server that hosts single databases and elastic pools. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Creates a firewall rule for a logical server. | 
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Creates a new Azure SQL Database single database. | 
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Creates a new failover group. |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) | Gets one or more SQL databases. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Adds one or more Azure SQL Databases to a failover group. |

# [Azure CLI](#tab/azure-cli)
Create your failover group and add your single database to it using AZ CLI. 

   > [!NOTE]
   > The server login and firewall settings must match that of your primary server. 

   ```azurecli-interactive
   #!/bin/bash
   # set variables
   $failoverLocation = "West US"
   $failoverServer = "failoverServer-$randomIdentifier"
   $failoverGroup = "failoverGroup-$randomIdentifier"

   echo "Creating a secondary logical server in the DR region..."
   az sql server create --name $failoverServer --resource-group $resourceGroup --location $failoverLocation --admin-user $login --admin-password $password
   
   echo "Creating a failover group between the two servers..."
   az sql failover-group create --name $failoverGroup --partner-server $failoverServer --resource-group $resourceGroup --server $server --add-db $database --failover-policy Automatic
   ```

This portion of the tutorial uses the following Az CLI cmdlets:

| Command | Notes |
|---|---|
| [az sql server create](/cli/azure/sql/server#az-sql-server-create) | Creates a SQL Database server that hosts single databases and elastic pools. |
| [az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule) | Creates a server's firewall rules. | 
| [az sql failover-group create](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-create) | Creates a failover group. | 

---

## 3 - Test failover 
In this step, you will fail your failover group over to the secondary server, and then fail back using the Azure portal. 

# [Portal](#tab/azure-portal)
Test failover using the Azure portal. 

1. Select **Azure SQL** in the left-hand menu of the [Azure portal](https://portal.azure.com). If **Azure SQL** is not in the list, select **All services**, then type Azure SQL in the search box. (Optional) Select the star next to **Azure SQL** to favorite it and add it as an item in the left-hand navigation. 
1. Select the single database created in the section 2, such as `mySampleDatbase`. 
1. Select the name of the server under **Server name** to open the settings for the server.

   ![Open server for single db](media/sql-database-single-database-failover-group-tutorial/open-sql-db-server.png)

1. Select **Failover groups** under the **Settings** pane and then choose the failover group you created in section 2. 
  
   ![Select the failover group from the portal](media/sql-database-single-database-failover-group-tutorial/select-failover-group.png)

1. Review which server is primary and which server is secondary. 
1. Select **Failover** from the task pane to failover your failover group containing your sample single database. 
1. Select **Yes** on the warning that notifies you that TDS sessions will be disconnected. 

   ![Fail over your failover group containing your SQL database](media/sql-database-single-database-failover-group-tutorial/failover-sql-db.png)

1. Review which server is now primary and which server is secondary. If fail over succeeded, the two servers should have swapped roles. 
1. Select **Failover** again to fail the servers back to their originally roles. 

# [PowerShell](#tab/azure-powershell)
Test failover using PowerShell. 


Check the role of the secondary replica: 

   ```powershell-interactive
   # Set variables
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $serverName = "mysqlserver-$(Get-Random)"
   # $failoverGroupName = "failovergrouptutorial-$(Get-Random)"
   
   # Check role of secondary replica
   Write-host "Confirming the secondary replica is secondary...." 
   (Get-AzSqlDatabaseFailoverGroup `
      -FailoverGroupName $failoverGroupName `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName).ReplicationRole
   ```

Fail over to the secondary server: 

   ```powershell-interactive
   # Set variables
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $serverName = "mysqlserver-$(Get-Random)"
   # $failoverGroupName = "failovergrouptutorial-$(Get-Random)"
   
   # Failover to secondary server
   Write-host "Failing over failover group to the secondary..." 
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group successfully to" $drServerName 
   ```

Revert failover group back to the primary server:

   ```powershell-interactive
   # Set variables
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $serverName = "mysqlserver-$(Get-Random)"
   # $failoverGroupName = "failovergrouptutorial-$(Get-Random)"
   
   # Revert failover to primary server
   Write-host "Failing over failover group to the primary...." 
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group successfully back to" $serverName
   ```

This portion of the tutorial uses the following PowerShell cmdlets:

| Command | Notes |
|---|---|
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Gets or lists Azure SQL Database failover groups. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Executes a failover of an Azure SQL Database failover group. |



# [Azure CLI](#tab/azure-cli)
Test failover using the AZ CLI. 

Verify which server is the secondary:

   
   ```azurecli-interactive
   echo "Verifying which server is in the secondary role..."
   az sql failover-group list --server $server --resource-group $resourceGroup
   ```

Fail over to the secondary server: 

   ```azurecli-interactive
   echo "Failing over group to the secondary server..."
   az sql failover-group set-primary --name $failoverGroup --resource-group $resourceGroup --server $failoverServer
   echo "Successfully failed failover group over to" $failoverServer
   ```

Revert failover group back to the primary server:

   ```azurecli-interactive
   echo "Failing over group back to the primary server..."
   az sql failover-group set-primary --name $failoverGroup --resource-group $resourceGroup --server $server
   echo "Successfully failed failover group back to" $server
   ```

This portion of the tutorial uses the following Az CLI cmdlets:

| Command | Notes |
|---|---|
| [az sql failover-group list](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-list) | Lists the failover groups in a server. |
| [az sql failover-group set-primary](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-set-primary) | Set the primary of the failover group by failing over all databases from the current primary server. | 

---

## Clean up resources 
Clean up resources by deleting the resource group. 

# [Portal](#tab/azure-portal)
Delete the resource group using the Azure portal. 

1. Navigate to your resource group in the [Azure portal](https://portal.azure.com).
1. Select  **Delete resource group** to delete all the resources in the group, as well as the resource group itself. 
1. Type the name of the resource group, `myResourceGroup`, in the textbox, and then select **Delete** to delete the resource group.  

# [PowerShell](#tab/azure-powershell)

Delete the resource group using PowerShell. 

   ```powershell-interactive
   # Set variables
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"

   # Remove the resource group
   Write-host "Removing resource group..."
   Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
   Write-host "Resource group removed =" $resourceGroupName
   ```

This portion of the tutorial uses the following PowerShell cmdlets:

| Command | Notes |
|---|---|
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Removes a resource group | 

# [Azure CLI](#tab/azure-cli)

Delete the resource group by using AZ CLI. 


   ```azurecli-interactive
   echo "Cleaning up resources by removing the resource group..."
   az group delete --name $resourceGroup
   echo "Successfully removed resource group" $resourceGroup
   ```

This portion of the tutorial uses the following Az CLI cmdlets:

| Command | Notes |
|---|---|
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az-vm-extension-set) | Deletes a resource group including all nested resources. |

---


> [!IMPORTANT]
> If you want to keep the resource group but delete the secondary database, remove it from the failover group before deleting it. Deleting a secondary database before it is removed from the failover group can cause unpredictable behavior. 


## Full scripts

# [PowerShell](#tab/azure-powershell)

[!code-powershell-interactive[main](../../powershell_scripts/sql-database/failover-groups/add-single-db-to-failover-group-az-ps.ps1 "Add single database to a failover group")]

This script uses the following commands. Each command in the table links to command specific documentation.

| Command | Notes |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Creates a resource group in which all resources are stored. |
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Creates a SQL Database server that hosts single databases and elastic pools. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Creates a firewall rule for a logical server. | 
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Creates a new Azure SQL Database single database. | 
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Creates a new failover group. |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) | Gets one or more SQL databases. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Adds one or more Azure SQL Databases to a failover group. |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Gets or lists Azure SQL Database failover groups. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Executes a failover of an Azure SQL Database failover group. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Removes a resource group | 

# [Azure CLI](#tab/azure-cli)

[!code-azurecli-interactive[main](../../cli_scripts/sql-database/failover-groups/add-single-db-to-failover-group-az-cli.sh "Add single database to a failover group")]

This script uses the following commands. Each command in the table links to command specific documentation.

| Command | Notes |
|---|---|
| [az account set](/cli/azure/account?view=azure-cli-latest#az-account-set) | Sets a subscription to be the current active subscription. | 
| [az group create](/cli/azure/group#az-group-create) | Creates a resource group in which all resources are stored. |
| [az sql server create](/cli/azure/sql/server#az-sql-server-create) | Creates a SQL Database server that hosts single databases and elastic pools. |
| [az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule) | Creates a server's firewall rules. | 
| [az sql db create](/cli/azure/sql/db?view=azure-cli-latest) | Creates a database. | 
| [az sql failover-group create](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-create) | Creates a failover group. | 
| [az sql failover-group list](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-list) | Lists the failover groups in a server. |
| [az sql failover-group set-primary](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-set-primary) | Set the primary of the failover group by failing over all databases from the current primary server. | 
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az-vm-extension-set) | Deletes a resource group including all nested resources. |

# [Portal](#tab/azure-portal)
There are no scripts available for the Azure portal. 
 
---

You can find other Azure SQL Database scripts here: [Azure PowerShell](sql-database-powershell-samples.md) and [Azure CLI](sql-database-cli-samples.md). 

## Next steps

In this tutorial, you added an Azure SQL Database single database to a failover group, and tested failover. You learned how to: 

> [!div class="checklist"]
> - Create an Azure SQL Database single database. 
> - Create a [failover group](sql-database-auto-failover-group.md) for a single database between two logical SQL servers.
> - Test failover.

Advance to the next tutorial on how to add your elastic pool to a failover group. 

> [!div class="nextstepaction"]
> [Tutorial: Add an Azure SQL Database elastic pool to a failover group](sql-database-elastic-pool-failover-group-tutorial.md)
