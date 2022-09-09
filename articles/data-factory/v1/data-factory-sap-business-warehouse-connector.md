---
title: Move data from SAP Business Warehouse using Azure Data Factory | Microsoft Docs
description: Learn about how to move data from SAP Business Warehouse using Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
editor: 

ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na

ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang

robots: noindex
---
# Move data From SAP Business Warehouse using Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](data-factory-sap-business-warehouse-connector.md)
> * [Version 2 (current version)](../connector-sap-business-warehouse.md)

> [!NOTE]
> This article applies to version 1 of Data Factory. If you are using the current version of the Data Factory service, see [SAP Business Warehouse connector in V2](../connector-sap-business-warehouse.md).


This article explains how to use the Copy Activity in Azure Data Factory to move data from an on-premises SAP Business Warehouse (BW). It builds on the [Data Movement Activities](data-factory-data-movement-activities.md) article, which presents a general overview of data movement with the copy activity.

You can copy data from an on-premises SAP Business Warehouse data store to any supported sink data store. For a list of data stores supported as sinks by the copy activity, see the [Supported data stores](data-factory-data-movement-activities.md#supported-data-stores-and-formats) table. Data factory currently supports only moving data from an SAP Business Warehouse to other data stores, but not for moving data from other data stores to an SAP Business Warehouse. 

## Supported versions and installation
This connector supports SAP Business Warehouse version 7.x. It supports copying data from InfoCubes and QueryCubes (including BEx queries) using MDX queries.

To enable the connectivity to the SAP BW instance, install the following components:
- **Data Management Gateway**: Data Factory service supports connecting to on-premises data stores (including SAP Business Warehouse) using a component called Data Management Gateway. To learn about Data Management Gateway and step-by-step instructions for setting up the gateway, see [Moving data between on-premises data store to cloud data store](data-factory-move-data-between-onprem-and-cloud.md) article. Gateway is required even if the SAP Business Warehouse is hosted in an Azure IaaS virtual machine (VM). You can install the gateway on the same VM as the data store or on a different VM as long as the gateway can connect to the database.
- **SAP NetWeaver library** on the gateway machine. You can get the SAP Netweaver library from your SAP administrator, or directly from the [SAP Software Download Center](https://support.sap.com/swdc). Search for the **SAP Note #1025361** to get the download location for the most recent version. Make sure that the architecture for the SAP NetWeaver library (32-bit or 64-bit) matches your gateway installation. Then install all files included in the SAP NetWeaver RFC SDK according to the SAP Note. The SAP NetWeaver library is also included in the SAP Client Tools installation.

> [!TIP]
> Put the dlls extracted from the NetWeaver RFC SDK into system32 folder.

## Getting started
You can create a pipeline with a copy activity that moves data from an on-premises Cassandra data store by using different tools/APIs. 

- The easiest way to create a pipeline is to use the **Copy Wizard**. See [Tutorial: Create a pipeline using Copy Wizard](data-factory-copy-data-wizard-tutorial.md) for a quick walkthrough on creating a pipeline using the Copy data wizard. 
- You can also use the following tools to create a pipeline: **Azure portal**, **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager template**, **.NET API**, and **REST API**. See [Copy activity tutorial](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) for step-by-step instructions to create a pipeline with a copy activity. 

Whether you use the tools or APIs, you perform the following steps to create a pipeline that moves data from a source data store to a sink data store:

1. Create **linked services** to link input and output data stores to your data factory.
2. Create **datasets** to represent input and output data for the copy operation. 
3. Create a **pipeline** with a copy activity that takes a dataset as an input and a dataset as an output. 

When you use the wizard, JSON definitions for these Data Factory entities (linked services, datasets, and the pipeline) are automatically created for you. When you use tools/APIs (except .NET API), you define these Data Factory entities by using the JSON format.  For a sample with JSON definitions for Data Factory entities that are used to copy data from an on-premises SAP Business Warehouse, see [JSON example: Copy data from SAP Business Warehouse to Azure Blob](#json-example-copy-data-from-sap-business-warehouse-to-azure-blob) section of this article. 

The following sections provide details about JSON properties that are used to define Data Factory entities specific to an SAP BW data store:

## Linked service properties
The following table provides description for JSON elements specific to SAP Business Warehouse (BW) linked service.

Property | Description | Allowed values | Required
-------- | ----------- | -------------- | --------
server | Name of the server on which the SAP BW instance resides. | string | Yes
systemNumber | System number of the SAP BW system. | Two-digit decimal number represented as a string. | Yes
clientId | Client ID of the client in the SAP W system. | Three-digit decimal number represented as a string. | Yes
username | Name of the user who has access to the SAP server | string | Yes
password | Password for the user. | string | Yes
gatewayName | Name of the gateway that the Data Factory service should use to connect to the on-premises SAP BW instance. | string | Yes
encryptedCredential | The encrypted credential string. | string | No

## Dataset properties
For a full list of sections & properties available for defining datasets, see the [Creating datasets](data-factory-create-datasets.md) article. Sections such as structure, availability, and policy of a dataset JSON are similar for all dataset types (Azure SQL, Azure blob, Azure table, etc.).

The **typeProperties** section is different for each type of dataset and provides information about the location of the data in the data store. There are no type-specific properties supported for the SAP BW dataset of type **RelationalTable**. 


## Copy activity properties
For a full list of sections & properties available for defining activities, see the [Creating Pipelines](data-factory-create-pipelines.md) article. Properties such as name, description, input and output tables, are policies are available for all types of activities.

Whereas, properties available in the **typeProperties** section of the activity vary with each activity type. For Copy activity, they vary depending on the types of sources and sinks.

When source in copy activity is of type **RelationalSource** (which includes SAP BW), the following properties are available in typeProperties section:

| Property | Description | Allowed values | Required |
| --- | --- | --- | --- |
| query | Specifies the MDX query to read data from the SAP BW instance. | MDX query. | Yes |


## JSON example: Copy data from SAP Business Warehouse to Azure Blob
The following example provides sample JSON definitions that you can use to create a pipeline by using [Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md) or [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) or [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). This sample shows how to copy data from an on-premises SAP Business Warehouse to an Azure Blob Storage. However, data can be copied **directly** to any of the sinks stated [here](data-factory-data-movement-activities.md#supported-data-stores-and-formats) using the Copy Activity in Azure Data Factory.  

> [!IMPORTANT]
> This sample provides JSON snippets. It does not include step-by-step instructions for creating the data factory. See [moving data between on-premises locations and cloud](data-factory-move-data-between-onprem-and-cloud.md) article for step-by-step instructions.

The sample has the following data factory entities:

1. A linked service of type [SapBw](#linked-service-properties).
2. A linked service of type [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. An input [dataset](data-factory-create-datasets.md) of type [RelationalTable](#dataset-properties).
4. An output [dataset](data-factory-create-datasets.md) of type [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. A [pipeline](data-factory-create-pipelines.md) with Copy Activity that uses [RelationalSource](#copy-activity-properties) and [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

The sample copies data from an SAP Business Warehouse instance to an Azure blob hourly. The JSON properties used in these samples are described in sections following the samples.

As a first step, setup the data management gateway. The instructions are in the [moving data between on-premises locations and cloud](data-factory-move-data-between-onprem-and-cloud.md) article.

### SAP Business Warehouse linked service
This linked service links your SAP BW instance to the data factory. The type property is set to **SapBw**. The typeProperties section provides connection information for the SAP BW instance. 

```json
{
    "name": "SapBwLinkedService",
    "properties":
    {
        "type": "SapBw",
        "typeProperties":
        {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client id>",
            "username": "<SAP user>",
            "password": "<Password for SAP user>",
            "gatewayName": "<gateway name>"
        }
    }
}
```

### Azure Storage linked service
This linked service links your Azure Storage account to the data factory. The type property is set to **AzureStorage**. The typeProperties section provides connection information for the Azure Storage account.

```json
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```

### SAP BW input dataset
This dataset defines the SAP Business Warehouse dataset. You set the type of the Data Factory dataset to **RelationalTable**. Currently, you do not specify any type-specific properties for an SAP BW dataset. The query in the Copy Activity definition specifies what data to read from the SAP BW instance. 

Setting external property to true informs the Data Factory service that the table is external to the data factory and is not produced by an activity in the data factory.

Frequency and interval properties defines the schedule. In this case, the data is read from the SAP BW instance hourly. 

```json
{
    "name": "SapBwDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "SapBwLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```



### Azure Blob output dataset
This dataset defines the output Azure Blob dataset. The type property is set to AzureBlob. The typeProperties section provides where the data copied from the SAP BW instance is stored. The data is written to a new blob every hour (frequency: hour, interval: 1). The folder path for the blob is dynamically evaluated based on the start time of the slice that is being processed. The folder path uses year, month, day, and hours parts of the start time.

```json
{
    "name": "AzureBlobDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/sapbw/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            },
            "partitionedBy": [
                {
                    "name": "Year",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "yyyy"
                    }
                },
                {
                    "name": "Month",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "MM"
                    }
                },
                {
                    "name": "Day",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "dd"
                    }
                },
                {
                    "name": "Hour",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "HH"
                    }
                }
            ]
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```


### Pipeline with Copy activity
The pipeline contains a Copy Activity that is configured to use the input and output datasets and is scheduled to run every hour. In the pipeline JSON definition, the **source** type is set to **RelationalSource** (for SAP BW source) and **sink** type is set to **BlobSink**. The query specified for the **query** property selects the data in the past hour to copy.

```json
{
    "name": "CopySapBwToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
        				"query": "<MDX query for SAP BW>"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "SapBwDataset"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobDataSet"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "SapBwToBlob"
            }
        ],
        "start": "2017-03-01T18:00:00Z",
        "end": "2017-03-01T19:00:00Z"
    }
}
```



### Type mapping for SAP BW
As mentioned in the [data movement activities](data-factory-data-movement-activities.md) article, Copy activity performs automatic type conversions from source types to sink types with the following two-step approach:

1. Convert from native source types to .NET type
2. Convert from .NET type to native sink type

When moving data from SAP BW, the following mappings are used from SAP BW types to .NET types.

Data type in the ABAP Dictionary | .NET Data Type
-------------------------------- | --------------
ACCP |	Int
CHAR | String
CLNT | String
CURR | Decimal
CUKY | String
DEC | Decimal
FLTP | Double
INT1 | Byte
INT2 | Int16
INT4 | Int
LANG | String
LCHR | String
LRAW | Byte[]
PREC | Int16
QUAN | Decimal
RAW | Byte[]
RAWSTRING | Byte[]
STRING | String
UNIT | String
DATS | String
NUMC | String
TIMS | String

> [!NOTE]
> To map columns from source dataset to columns from sink dataset, see [Mapping dataset columns in Azure Data Factory](data-factory-map-columns.md).


## Map source to sink columns
To learn about mapping columns in source dataset to columns in sink dataset, see [Mapping dataset columns in Azure Data Factory](data-factory-map-columns.md).

## Repeatable read from relational sources
When copying data from relational data stores, keep repeatability in mind to avoid unintended outcomes. In Azure Data Factory, you can rerun a slice manually. You can also configure retry policy for a dataset so that a slice is rerun when a failure occurs. When a slice is rerun in either way, you need to make sure that the same data is read no matter how many times a slice is run. See [Repeatable read from relational sources](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources)

## Performance and Tuning
See [Copy Activity Performance & Tuning Guide](data-factory-copy-activity-performance.md) to learn about key factors that impact performance of data movement (Copy Activity) in Azure Data Factory and various ways to optimize it.
