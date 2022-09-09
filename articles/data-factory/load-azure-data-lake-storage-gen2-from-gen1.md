---
title: Copy data from Azure Data Lake Storage Gen1 to Gen2 with Azure Data Factory
description: 'Use Azure Data Factory to copy data from Azure Data Lake Storage Gen1 to Gen2'
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl

ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: jingwang
---

# Copy data from Azure Data Lake Storage Gen1 to Gen2 with Azure Data Factory

Azure Data Lake Storage Gen2 is a set of capabilities dedicated to big data analytics, built into [Azure Blob storage](../storage/blobs/storage-blobs-introduction.md). It allows you to interface with your data using both file system and object storage paradigms.

If you are currently using Azure Data Lake Storage Gen1, you can evaluate the Gen2 new capability by copying data from Data Lake Storage Gen1 to Gen2 using Azure Data Factory.

Azure Data Factory is a fully managed cloud-based data integration service. You can use the service to populate the lake with data from a rich set of on-premises and cloud-based data stores and save time when building your analytics solutions. For a detailed list of supported connectors, see the table of [Supported data stores](copy-activity-overview.md#supported-data-stores-and-formats).

Azure Data Factory offers a scale-out, managed data movement solution. Due to the scale-out architecture of ADF, it can ingest data at a high throughput. For details, see [Copy activity performance](copy-activity-performance.md).

This article shows you how to use the Data Factory Copy Data tool to copy data from _Azure Data Lake Storage Gen1_ into _Azure Data Lake Storage Gen2_. You can follow similar steps to copy data from other types of data stores.

## Prerequisites

* Azure subscription: If you don't have an Azure subscription, create a [free account](https://azure.microsoft.com/free/) before you begin.
* Azure Data Lake Storage Gen1 account with data in it.
* Azure Storage account with Data Lake Storage Gen2 enabled: If you don't have a Storage account, [create an account](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM).

## Create a data factory

1. On the left menu, select **Create a resource** > **Data + Analytics** > **Data Factory**:
   
   ![Data Factory selection in the "New" pane](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. In the **New data factory** page, provide values for the fields that are shown in the following image: 
      
   ![New data factory page](./media/load-azure-data-lake-storage-gen2-from-gen1/new-azure-data-factory.png)
 
    * **Name**: Enter a globally unique name for your Azure data factory. If you receive the error "Data factory name \"LoadADLSDemo\" is not available," enter a different name for the data factory. For example, you could use the name _**yourname**_**ADFTutorialDataFactory**. Try creating the data factory again. For the naming rules for Data Factory artifacts, see [Data Factory naming rules](naming-rules.md).
    * **Subscription**: Select your Azure subscription in which to create the data factory. 
    * **Resource Group**: Select an existing resource group from the drop-down list, or select the **Create new** option and enter the name of a resource group. To learn about resource groups, see [Using resource groups to manage your Azure resources](../azure-resource-manager/resource-group-overview.md).  
    * **Version**: Select **V2**.
    * **Location**: Select the location for the data factory. Only supported locations are displayed in the drop-down list. The data stores that are used by data factory can be in other locations and regions. 

3. Select **Create**.
4. After creation is complete, go to your data factory. You see the **Data Factory** home page as shown in the following image: 
   
   ![Data factory home page](./media/load-azure-data-lake-storage-gen2-from-gen1/data-factory-home-page.png)

   Select the **Author & Monitor** tile to launch the Data Integration Application in a separate tab.

## Load data into Azure Data Lake Storage Gen2

1. In the **Get started** page, select the **Copy Data** tile to launch the Copy Data tool: 

   ![Copy Data tool tile](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-data-tool-tile.png)
2. In the **Properties** page, specify **CopyFromADLSGen1ToGen2** for the **Task name** field, and select **Next**:

    ![Properties page](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-data-tool-properties-page.png)
3. In the **Source data store** page, click **+ Create new connection**:

    ![Source data store page](./media/load-azure-data-lake-storage-gen2-from-gen1/source-data-store-page.png)
	
	Select **Azure Data Lake Storage Gen1** from the connector gallery, and select **Continue**
	
	![Source data store Azure Data Lake Storage Gen1 page](./media/load-azure-data-lake-storage-gen2-from-gen1/source-data-store-page-adls-gen1.png)
	
4. In the **Specify Azure Data Lake Storage Gen1 connection** page, do the following steps:
   1. Select your Data Lake Storage Gen1 for the account name, and specify or validate the **Tenant**.
   2. Click **Test connection** to validate the settings, then select **Finish**.
   3. You will see a new connection gets created. Select **Next**.
   
   > [!IMPORTANT]
   > In this walkthrough, you use a managed identity for Azure resources to authenticate your Data Lake Storage Gen1. Be sure to grant the MSI the proper permissions in Azure Data Lake Storage Gen1 by following [these instructions](connector-azure-data-lake-store.md#managed-identity).
   
   ![Specify Azure Data Lake Storage Gen1 account](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen1-account.png)
      
5. In the **Choose the input file or folder** page, browse to the folder and file that you want to copy over. Select the folder/file, select **Choose**:

    ![Choose input file or folder](./media/load-azure-data-lake-storage-gen2-from-gen1/choose-input-folder.png)

6. Specify the copy behavior by checking the **Copy files recursively** and **Binary copy** options. Select **Next**:

    ![Specify output folder](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-binary-copy.png)
	
7. In the **Destination data store** page, click **+ Create new connection**, and then select **Azure Data Lake Storage Gen2**, and select **Continue**:

    ![Destination data store page](./media/load-azure-data-lake-storage-gen2-from-gen1/destination-data-storage-page.png)

8. In the **Specify Azure Data Lake Storage Gen2 connection** page, do the following steps:

   1. Select your Data Lake Storage Gen2 capable account from the "Storage account name" drop down list.
   2. Select **Finish** to create the connection. Then select **Next**.
   
   ![Specify Azure Data Lake Storage Gen2 account](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen2-account.png)

9. In the **Choose the output file or folder** page, enter **copyfromadlsgen1** as the output folder name, and select **Next**. ADF will create the corresponding ADLS Gen2 file system and sub-folders during copy if it doesn't exist.

    ![Specify output folder](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen2-path.png)

10. In the **Settings** page, select **Next** to use the default settings.

11. In the **Summary** page, review the settings, and select **Next**:

    ![Summary page](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-summary.png)
12. In the **Deployment page**, select **Monitor** to monitor the pipeline:

    ![Deployment page](./media/load-azure-data-lake-storage-gen2-from-gen1/deployment-page.png)
13. Notice that the **Monitor** tab on the left is automatically selected. The **Actions** column includes links to view activity run details and to rerun the pipeline:

    ![Monitor pipeline runs](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-pipeline-runs.png)

14. To view activity runs that are associated with the pipeline run, select the **View Activity Runs** link in the **Actions** column. There's only one activity (copy activity) in the pipeline, so you see only one entry. To switch back to the pipeline runs view, select the **Pipelines** link at the top. Select **Refresh** to refresh the list. 

    ![Monitor activity runs](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-activity-runs.png)

15. To monitor the execution details for each copy activity, select the **Details** link (eyeglasses image) under **Actions** in the activity monitoring view. You can monitor details like the volume of data copied from the source to the sink, data throughput, execution steps with corresponding duration, and used configurations:

    ![Monitor activity run details](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-activity-run-details.png)

16. Verify that the data is copied into your Data Lake Storage Gen2 account.

## Best practices

To assess upgrading from Azure Data Lake Storage (ADLS) Gen1 to Gen2 in general, refer to [Upgrade your big data analytics solutions from Azure Data Lake Storage Gen1 to Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-upgrade.md). The following sections introduce best practices of using ADF for data upgrade from Gen1 to Gen2.

### Data partition for historical data copy

- If your total data size in ADLS Gen1 is less than **30TB** and the number of files is less than **1 million**, you can copy all data in single Copy activity run.
- If you have larger size of data to copy, or you want the flexibility to manage data migration in batches and make each of them completed within a specific timing windows, you are suggested to partition the data, in which case it can also reduce the risk of any unexpected issue.

A PoC (Proof of Concept) is highly recommended in order to verify the end to end solution and test the copy throughput in your environment. Major steps of doing PoC: 

1. Create one ADF pipeline with single copy activity to copy several TBs of data from ADLS Gen1 to ADLS Gen2 to get a copy performance baseline, starting with [Data Integration Units (DIUs)](copy-activity-performance.md#data-integration-units) as 128 . 
2. Based on the copy throughput you get in step #1, calculate the estimated time required for the entire data migration. 
3. (Optional) Create a control table and define the file filter to partition the files to be migrated. The way to partition the files as followings: 

    - Partitioned by folder name or folder name with wildcard filter (suggested) 
    - Partitioned by file’s last modified time 

### Network bandwidth and storage I/O 

You can control the concurrency of ADF copy jobs which read data from ADLS Gen1 and write data to ADLS Gen2, so that you can manage the usage on storage I/O in order to not impact the normal business work on ADLS Gen1 during the migration.

### Permissions 

In Data Factory, [ADLS Gen1 connector](connector-azure-data-lake-store.md) supports Service Principal and Managed Identity for Azure resource authentications; [ADLS Gen2 connector](connector-azure-data-lake-storage.md) supports account key, Service Principal and Managed Identity for Azure resource authentications. To make Data Factory able to navigate and copy all the files/ACLs as you need, make sure you grant high enough permissions for the account you provide to access/read/write all files and set ACLs if you choose to. Suggest to grant it as super-user/owner role during the migration period. 

### Preserve ACLs from Data Lake Storage Gen1

If you want to replicate the ACLs along with data files when upgrading from Data Lake Storage Gen1 to Gen2, refer to [Preserve ACLs from Data Lake Storage Gen1](connector-azure-data-lake-storage.md#preserve-acls-from-data-lake-storage-gen1). 

### Incremental copy 

Several approaches can be used to load only the new or updated files from ADLS Gen1:

- Load new or updated files by time partitioned folder or file name, e.g. /2019/05/13/*;
- Load new or updated files by LastModifiedDate;
- Identify new or updated files by any 3rd party tool/solution, then pass the file or folder name to ADF pipeline via parameter or a table/file.  

The proper frequency to do incremental load depends on the total number of files in ADLS Gen1 and the volume of new or updated file to be loaded every time.  

## Next steps

> [!div class="nextstepaction"]
> [Copy activity overview](copy-activity-overview.md)
> [Azure Data Lake Storage Gen1 connector](connector-azure-data-lake-store.md)
> [Azure Data Lake Storage Gen2 connector](connector-azure-data-lake-storage.md)