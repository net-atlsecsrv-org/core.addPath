---
title: Learn how to manage database accounts in Azure Cosmos DB
description: Learn how to manage Azure Cosmos DB resources by using the Azure portal, PowerShell, CLI, and Azure Resource Manager templates
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
---

# Manage an Azure Cosmos account

This article describes how to manage various tasks on an Azure Cosmos account using the Azure portal, Azure PowerShell, Azure CLI, and Azure Resource Manager templates.

## Create an account

### <a id="create-database-account-via-portal"></a>Azure portal

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

### <a id="create-database-account-via-cli"></a>Azure CLI

Please see [Create an Azure Cosmos DB account with Azure CLI](manage-with-cli.md#create-an-azure-cosmos-db-account)

### <a id="create-database-account-via-ps"></a>Azure PowerShell

Please see [Create an Azure Cosmos DB account with Powershell](manage-with-powershell.md#create-account)

### <a id="create-database-account-via-arm-template"></a>Azure Resource Manager template

This Azure Resource Manager template will create an Azure Cosmos account for SQL API configured with two regions and options to select consistency level, automatic failover, and multi-master. To deploy this template, click on Deploy to Azure on the readme page, [Create Azure Cosmos account](https://github.com/Azure/azure-quickstart-templates/tree/master/101-cosmosdb-sql)

## Add/remove regions from your database account

### <a id="add-remove-regions-via-portal"></a>Azure portal

1. Sign in to [Azure portal](https://portal.azure.com).

1. Go to your Azure Cosmos account, and open the **Replicate data globally** menu.

1. To add regions, select the hexagons on the map with the **+** label that corresponds to your desired region(s). Alternatively, to add a region, select the **+ Add region** option and choose a region from the drop-down menu.

1. To remove regions, clear one or more regions from the map by selecting the blue hexagons with check marks. Or select the "wastebasket" (🗑) icon next to the region on the right side.

1. To save your changes, select **OK**.

   ![Add or remove regions menu](./media/how-to-manage-database-account/add-region.png)

In a single-region write mode, you cannot remove the write region. You must fail over to a different region before you can delete the current write region.

In a multi-region write mode, you can add or remove any region, if you have at least one region.

### <a id="add-remove-regions-via-cli"></a>Azure CLI

Please see [Add or remove regions with Azure CLI](manage-with-cli.md#add-or-remove-regions)

### <a id="add-remove-regions-via-ps"></a>Azure PowerShell

Please see [Add or remove regions with Powershell](manage-with-powershell.md#update-account)

## <a id="configure-multiple-write-regions"></a>Configure multiple write-regions

### <a id="configure-multiple-write-regions-portal"></a>Azure portal

Open the **Replicate Data Globally** tab and select **Enable** to enable multi-region writes. After you enable multi-region writes, all the read regions that you currently have on the account will become read and write regions.

![Azure Cosmos account configures multi-master screenshot](./media/how-to-manage-database-account/single-to-multi-master.png)

### <a id="configure-multiple-write-regions-cli"></a>Azure CLI

Please see [Enable multiple-write regions with Azure CLI](manage-with-cli.md#enable-multiple-write-regions)

### <a id="configure-multiple-write-regions-ps"></a>Azure PowerShell

Please see [Enable multiple-write regions with Powershell](manage-with-powershell.md#multi-master)

### <a id="configure-multiple-write-regions-arm"></a>Resource Manager template

An account can be migrated from single-master to multi-master by deploying the Resource Manager template used to create the account and setting `enableMultipleWriteLocations: true`. The following Azure Resource Manager template is a bare minimum template that will deploy an Azure Cosmos account for SQL API with two regions and multiple write locations enabled.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "String"
        },
        "location": {
            "type": "String",
            "defaultValue": "[resourceGroup().location]"
        },
        "primaryRegion":{
            "type":"string",
            "metadata": {
                "description": "The primary replica region for the Cosmos DB account."
            }
        },
        "secondaryRegion":{
            "type":"string",
            "metadata": {
              "description": "The secondary replica region for the Cosmos DB account."
          }
        }
    },
    "resources": [
        {
            "type": "Microsoft.DocumentDb/databaseAccounts",
            "kind": "GlobalDocumentDB",
            "name": "[parameters('name')]",
            "apiVersion": "2019-08-01",
            "location": "[parameters('location')]",
            "tags": {},
            "properties": {
                "databaseAccountOfferType": "Standard",
                "consistencyPolicy": { "defaultConsistencyLevel": "Session" },
                "locations":
                [
                    {
                        "locationName": "[parameters('primaryRegion')]",
                        "failoverPriority": 0,
                        "isZoneRedundant": false
                    },
                    {
                        "locationName": "[parameters('secondaryRegion')]",
                        "failoverPriority": 1,
                        "isZoneRedundant": false
                    }
                ],
                "enableMultipleWriteLocations": true
            }
        }
    ]
}
```

## <a id="automatic-failover"></a>Enable automatic failover for your Azure Cosmos account

The Automatic failover option allows Azure Cosmos DB to failover to the region with the highest failover priority with no user action should a region become unavailable. When automatic failover is enabled, region priority can be modified. Account must have two or more regions to enable automatic failover.

### <a id="enable-automatic-failover-via-portal"></a>Azure portal

1. From your Azure Cosmos account, open the **Replicate data globally** pane.

2. At the top of the pane, select **Automatic Failover**.

   ![Replicate data globally menu](./media/how-to-manage-database-account/replicate-data-globally.png)

3. On the **Automatic Failover** pane, make sure that **Enable Automatic Failover** is set to **ON**. 

4. Select **Save**.

   ![Automatic failover portal menu](./media/how-to-manage-database-account/automatic-failover.png)

### <a id="enable-automatic-failover-via-cli"></a>Azure CLI

Please see [Enable automatic failover with Azure CLI](manage-with-cli.md#enable-automatic-failover)

### <a id="enable-automatic-failover-via-ps"></a>Azure PowerShell

Please see [Enable automatic failover with Powershell](manage-with-powershell.md#enable-automatic-failover)

## Set failover priorities for your Azure Cosmos account

After a Cosmos account is configured for automatic failover, the failover priority for regions can be changed.

> [!IMPORTANT]
> You cannot modify the write region (failover priority of zero) when the account is configured for automatic failover. To change the write region, you must disable automatic failover and do a manual failover.

### <a id="set-failover-priorities-via-portal"></a>Azure portal

1. From your Azure Cosmos account, open the **Replicate data globally** pane.

2. At the top of the pane, select **Automatic Failover**.

   ![Replicate data globally menu](./media/how-to-manage-database-account/replicate-data-globally.png)

3. On the **Automatic Failover** pane, make sure that **Enable Automatic Failover** is set to **ON**.

4. To modify the failover priority, drag the read regions via the three dots on the left side of the row that appear when you hover over them.

5. Select **Save**.

   ![Automatic failover portal menu](./media/how-to-manage-database-account/automatic-failover.png)

### <a id="set-failover-priorities-via-cli"></a>Azure CLI

Please see [Set failover priority with Azure CLI](manage-with-cli.md#set-failover-priority)

### <a id="set-failover-priorities-via-ps"></a>Azure PowerShell

Please see [Set failover priority with Powershell](manage-with-powershell.md#modify-failover-priority)

## <a id="manual-failover"></a>Perform manual failover on an Azure Cosmos account

> [!IMPORTANT]
> The Azure Cosmos account must be configured for manual failover for this operation to succeed.

The process for performing a manual failover involves changing the account's write region (failover priority = 0) to another region configured for the account.

> [!NOTE]
> Multi-master accounts cannot be manually failed over. For applications using the Azure Cosmos SDK, the SDK will detect when a region becomes unavailable, then redirect automatically to the next closest region if using multi-homing API in the SDK.

### <a id="enable-manual-failover-via-portal"></a>Azure portal

1. Go to your Azure Cosmos account, and open the **Replicate data globally** menu.

2. At the top of the menu, select **Manual Failover**.

   ![Replicate data globally menu](./media/how-to-manage-database-account/replicate-data-globally.png)

3. On the **Manual Failover** menu, select your new write region. Select the check box to indicate that you understand this option changes your write region.

4. To trigger the failover, select **OK**.

   ![Manual failover portal menu](./media/how-to-manage-database-account/manual-failover.png)

### <a id="enable-manual-failover-via-cli"></a>Azure CLI

Please see [Trigger manual failover with Azure CLI](manage-with-cli.md#trigger-manual-failover)

### <a id="enable-manual-failover-via-ps"></a>Azure PowerShell

Please see [Trigger manual failover with Powershell](manage-with-powershell.md#trigger-manual-failover)

## Next steps

For more information and examples on how to manage the Azure Cosmos account as well as database and containers, read the following articles:

* [Manage Azure Cosmos DB using Azure PowerShell](manage-with-powershell.md)
* [Manage Azure Cosmos DB using Azure CLI](manage-with-cli.md)
