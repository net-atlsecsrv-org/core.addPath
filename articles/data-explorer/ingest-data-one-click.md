---
title: Use one-click ingestion to Ingest data into Azure Data Explorer
description: Learn about how to ingest (load) data into Azure Data Explorer simply using one-click ingestion.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/31/2019
---

# Use one-click ingestion to ingest data into Azure Data Explorer

This article shows you how to use one-click ingestion for quick ingestion of a new table in json or csv formats from storage or local file into an existing table or a new table in Azure Data Explorer. Use the intuitive wizard and within a few minutes, your data is ingested, you can edit the table, and run queries using the Web UI.

## Prerequisites

* If you don't have an Azure subscription, create a [free Azure account](https://azure.microsoft.com/free/) before you begin.
* Sign in to [the application](https://dataexplorer.azure.com/).
* Create [an Azure Data Explorer cluster and database](create-cluster-database-portal.md)
* Sign in to the [Web UI](https://dataexplorer.azure.com/) and [add a connection to your cluster](/azure/data-explorer/web-query-data#add-clusters)
* Source of data in Azure Storage.

## Ingest new data

1. Right-click on the *database* or *table* row in left hand menu of the Web UI and select **Ingest new data (Preview)**

    ![select one click ingestion in web UI](media/ingest-data-one-click/one-click-ingestion-in-webui.png)   
 
1. In the **Ingest new data (preview)** window, in **Source** tab, complete the **Project Details**:

    * **Table**: Select existing table name from drop-down or select **Create new** to make a new table.
	* Select **Ingestion type** > **from storage** or **from file**.
	    * If you selected **from storage**, enter **Link to storage** to add the url to storage. Use [Blob SAS URL](/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container) for private storage accounts. 
        * If you selected **from file**, select **Browse** and drag the file into the box.
    * Select **Edit schema** to view and edit your table column configuration.
 
    ![one click ingestion source details](media/ingest-data-one-click/one-click-ingestion-source.png) 

    > [!TIP]
    > If you select **Ingest new data (Preview)** on a *table* row, the selected table name will appear in the **Project Details**.

1. If you selected an existing table, the **Map columns** window opens to map source data columns to target table columns. 
    * Use **Omit column** to remove a target column from the table. 
    * Use **New column** to add a new column to your table. 

    ![Map columns window](media/ingest-data-one-click/one-click-map-columns-window.png)

1. In **Schema** tab:

    * Select **Compression type** from drop-down > **Uncompressed** or **GZip**.
    * Select **Data format** from drop-down > **JSON**, **CSV**, **TSV**, **SCSV**, **SOHSV**, **TSVE**, or **PSV**. 
        * When you select **JSON** format, select **JSON levels**: 1-10. The levels affect the table column data depiction. 
        * If you select a format other than JSON: select checkbox **Include column names** to ignore heading row of file.    
    * **Mapping name** is set automatically but can be edited.
    * If you selected an existing table, you can select **Map columns** button to open the **Map columns** window.

    ![one click ingestion csv format schema.png](media/ingest-data-one-click/one-click-csv-format.png)

1. In **Editor**, select **V** on the right to open the editor. In the editor, you can view and copy the automatic queries generated from your inputs. 

1.	In table: 
    * Right-click on new column headers to **Change data type**, **Rename column**, **Delete column**, **Sort ascending**, or **Sort descending**. On existing columns only data sorting is available. 
    * Double-click on the new column name to edit.

1. Select **Start ingestion** to create table, create mapping, and data ingestion.

    ![one click ingestion json format schema](media/ingest-data-one-click/one-click-json-format.png) 
 
## Query data

1. In the **Data ingestion completed** window, all three steps will be marked with green checkmarks, if data ingestion completed successfully. 
 
    ![one click data ingestion complete](media/ingest-data-one-click/one-click-data-ingestion-complete.png)

1. Select **V** to open query. Copy to Web UI to edit the query.

1. The menu on the right contains **Quick queries** and **Tools**. 

    * **Quick queries** includes links to Web UI with example queries.
    * **Tools** includes link to Web UI with **Drop commands** that allow you to troubleshoot issues by running the relevant `.drop` command.

    > [!TIP]
    > Data may be lost using `.drop` commands. Use them carefully.

## Next steps

* [Query data in Azure Data Explorer Web UI](web-query-data.md)
* [Write queries for Azure Data Explorer using Kusto Query Language](write-queries.md)
