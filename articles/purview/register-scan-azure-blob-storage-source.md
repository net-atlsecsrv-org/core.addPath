---
title: 'How to scan Azure storage blob'
description: Learn how to scan Azure blob storage in your Azure Purview data catalog. 
author: hophanms
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/25/2020
---

# Register and scan Azure Blob Storage

This article outlines how to register an Azure Blob Storage account in Purview and set up a scan.

## Supported capabilities

Azure Blob Storage supports full and incremental scans to capture the metadata and schema. It also classifies the data automatically based on system and custom classification rules.

## Prerequisites

- Before registering data sources, create an Azure Purview account. For more information on creating a Purview account, see [Quickstart: Create an Azure Purview account](create-catalog-portal.md).
- You need to be an Azure Purview Data Source Admin

## Setting up authentication for a scan

There are three ways to set up authentication for Azure blob storage:

- Managed Identity
- Account Key
- Service Principal

### Managed Identity (Recommended)

When you choose **Managed Identity**, to set up the connection, you must first give your Purview account the permission to scan the data source:

1. Navigate to your storage account.
1. Select **Access Control (IAM)** from the left navigation menu. 
1. Select **+ Add**.
1. Set the **Role** to **Storage Blob Data Reader** and enter your Azure Purview account name under **Select** input box. Then, select **Save** to give this role assignment to your Purview account.

> [!Note]
> For more details, please see steps in [Authorize access to blobs and queues using Azure Active Directory](https://docs.microsoft.com/azure/storage/common/storage-auth-aad)

### Account Key

When authentication method selected is **Account Key**, you need to get your access key and store in the key vault:

1. Navigate to your storage account
1. Select **Settings > Access keys**
1. Copy your *key* and save it somewhere for the next steps
1. Navigate to your key vault
1. Select **Settings > Secrets**
1. Select **+ Generate/Import** and enter the **Name** and **Value** as the *key* from your storage account
1. Select **Create** to complete
1. If your key vault is not connected to Purview yet, you will need to [create a new key vault connection](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)
1. Finally, [create a new credential](manage-credentials.md#create-a-new-credential) using the key to setup your scan

### Service principal

To use a service principal, you can use an existing one or create a new one. 

> [!Note]
> If you have to create a new Service Principal, please follow these steps:
> 1. Navigate to the [Azure portal](https://portal.azure.com).
> 1. Select **Azure Active Directory** from the left-hand side menu.
> 1. Select **App registrations**.
> 1. Select **+ New application registration**.
> 1. Enter a name for the **application** (the service principal name).
> 1. Select **Accounts in this organizational directory only**.
> 1. For Redirect URI select **Web** and enter any URL you want; it doesn't have to be real or work.
> 1. Then select **Register**.

It is required to get the Service Principal's application ID and secret:

1. Navigate to your Service Principal in the [Azure portal](https://portal.azure.com)
1. Copy the values the **Application (client) ID** from **Overview** and **Client secret** from **Certificates & secrets**.
1. Navigate to your key vault
1. Select **Settings > Secrets**
1. Select **+ Generate/Import** and enter the **Name** of your choice and **Value** as the **Client secret** from your Service Principal
1. Select **Create** to complete
1. If your key vault is not connected to Purview yet, you will need to [create a new key vault connection](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)
1. Finally, [create a new credential](manage-credentials.md#create-a-new-credential) using the Service Principal to setup your scan

#### Granting the Service Principal access to your blob storage

1. Navigate to your storage account.
1. Select **Access Control (IAM)** from the left navigation menu. 
1. Select **+ Add**.
1. Set the **Role** to **Storage Blob Data Reader** and enter your service principal name or object ID under **Select** input box. Then, select **Save** to give this role assignment to your service principal.

## Firewall settings

> [!NOTE]
> If you have firewall enabled for the storage account, you must use **Managed Identity** authentication method when setting up a scan.

1. Go into your storage account in [Azure portal](https://portal.azure.com)
1. Navigate to **Settings > Networking** and
1. Choose **Selected Networks** under **Allow access from**
1. In the **Firewall** section, select **Allow trusted Microsoft services to access this storage account** and hit **Save**

:::image type="content" source="./media/register-scan-azure-blob-storage-source/firewall-setting.png" alt-text="Screenshot showing firewall setting":::

## Register an Azure Blob Storage account

To register a new blob account in your data catalog, do the following:

1. Navigate to your Purview account
1. Select **Sources** on the left navigation
1. Select **Register**
1. On **Register sources**, select **Azure Blob Storage**
1. Select **Continue**

On the **Register sources (Azure Blob Storage)** screen, do the following:

1. Enter a **Name** that the data source will be listed with in the Catalog. 
1. Choose your subscription to filter down storage accounts
1. Select a storage account
1. Select a collection or create a new one (Optional)
1. **Finish** to register the data source.

:::image type="content" source="media/register-scan-azure-blob-storage-source/register-sources.png" alt-text="register sources options" border="true":::

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

## Next steps

- [Browse the Azure Purview Data catalog](how-to-browse-catalog.md)
- [Search the Azure Purview Data Catalog](how-to-search-catalog.md)
