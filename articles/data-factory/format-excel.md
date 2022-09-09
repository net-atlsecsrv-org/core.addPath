---
title: Excel format in Azure Data Factory 
description: 'This topic describes how to deal with Excel format in Azure Data Factory.'
author: linda33wj
manager: shwang
ms.reviewer: craigg

ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 08/21/2020
ms.author: jingwang

---

# Excel format in Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Follow this article when you want to **parse the Excel files**. Azure Data Factory supports both ".xls" and ".xlsx".

Excel format is supported for the following connectors: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [File System](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md), and [SFTP](connector-sftp.md). It is supported as source but not sink.

## Dataset properties

For a full list of sections and properties available for defining datasets, see the [Datasets](concepts-datasets-linked-services.md) article. This section provides a list of properties supported by the Excel dataset.

| Property         | Description                                                  | Required |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | The type property of the dataset must be set to **Excel**.   | Yes      |
| location         | Location settings of the file(s). Each file-based connector has its own location type and supported properties under `location`. | Yes      |
| sheetName        | The Excel worksheet name to read data.                       | Yes      |
| range            | The cell range in the given worksheet to locate the selective data, e.g.:<br>- Not specified: reads the whole worksheet as a table from the first non-empty row and column<br>- `A3`: reads a table starting from the given cell, dynamically detects all the rows below and all the columns to the right<br>- `A3:H5`: reads this fixed range as a table<br>- `A3:A3`: reads this single cell | No       |
| firstRowAsHeader | Specifies whether to treat the first row in the given worksheet/range as a header line with names of columns.<br>Allowed values are **true** and **false** (default). | No       |
| nullValue        | Specifies the string representation of null value. <br>The default value is **empty string**. | No       |
| compression | Group of properties to configure file compression. Configure this section when you want to do compression/decompression during activity execution. | No |
| type<br/>(*under `compression`*) | The compression codec used to read/write JSON files. <br>Allowed values are **bzip2**, **gzip**, **deflate**, **ZipDeflate**, **snappy**, or **lz4**. to use when saving the file. Default is not compressed.<br>**Note** currently Copy activity doesn’t support "snappy" & "lz4", and mapping data flow doesn’t support "ZipDeflate".<br>**Note** when using copy activity to decompress **ZipDeflate** file(s) and write to file-based sink data store, files are extracted to the folder: `<path specified in dataset>/<folder named as source zip file>/`. | No.  |
| level<br/>(*under `compression`*) | The compression ratio. <br>Allowed values are **Optimal** or **Fastest**.<br>- **Fastest:** The compression operation should complete as quickly as possible, even if the resulting file is not optimally compressed.<br>- **Optimal**: The compression operation should be optimally compressed, even if the operation takes a longer time to complete. For more information, see [Compression Level](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) topic. | No       |

Below is an example of Excel dataset on Azure Blob Storage:

```json
{
    "name": "ExcelDataset",
    "properties": {
        "type": "Excel",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
                "folderPath": "folder/subfolder",
            },
            "sheetName": "MyWorksheet",
            "range": "A3:H5",
            "firstRowAsHeader": true
        }
    }
}
```

## Copy activity properties

For a full list of sections and properties available for defining activities, see the [Pipelines](concepts-pipelines-activities.md) article. This section provides a list of properties supported by the Excel source.

### Excel as source 

The following properties are supported in the copy activity ***\*source\**** section.

| Property      | Description                                                  | Required |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | The type property of the copy activity source must be set to **ExcelSource**. | Yes      |
| storeSettings | A group of properties on how to read data from a data store. Each file-based connector has its own supported read settings under `storeSettings`. | No       |

```json
"activities": [
    {
        "name": "CopyFromExcel",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "ExcelSource",
                "storeSettings": {
                    "type": "AzureBlobStorageReadSettings",
                    "recursive": true
                }
            },
            ...
        }
        ...
    }
]
```

## Mapping data flow properties

In mapping data flows, you can read Excel format in the following data stores: [Azure Blob Storage](connector-azure-blob-storage.md#mapping-data-flow-properties), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties), and [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties). You can point to Excel files either using Excel dataset or using an [inline dataset](data-flow-source.md#inline-datasets).

### Source properties

The below table lists the properties supported by an Excel source. You can edit these properties in the **Source options** tab. When using inline dataset, you will see additional file settings which are the same as the properties described in [dataset properties](#dataset-properties) section.

| Name                      | Description                                                  | Required | Allowed values                                            | Data flow script property         |
| ------------------------- | ------------------------------------------------------------ | -------- | --------------------------------------------------------- | --------------------------------- |
| Wild card paths           | All files matching the wildcard path will be processed. Overrides the folder and file path set in the dataset. | no       | String[]                                                  | wildcardPaths                     |
| Partition root path       | For file data that is partitioned, you can enter a partition root path in order to read partitioned folders as columns | no       | String                                                    | partitionRootPath                 |
| List of files             | Whether your source is pointing to a text file that lists files to process | no       | `true` or `false`                                         | fileList                          |
| Column to store file name | Create a new column with the source file name and path       | no       | String                                                    | rowUrlColumn                      |
| After completion          | Delete or move the files after processing. File path starts from the container root | no       | Delete: `true` or `false` <br> Move: `['<from>', '<to>']` | purgeFiles <br> moveFiles         |
| Filter by last modified   | Choose to filter files based upon when they were last altered | no       | Timestamp                                                 | modifiedAfter <br> modifiedBefore |

### Source example

The below image is an example of an Excel source configuration in mapping data flows using dataset mode.

![Excel source](media/data-flow/excel-source.png)

The associated data flow script is:

```
source(allowSchemaDrift: true,
	validateSchema: false,
	wildcardPaths:['*.xls']) ~> ExcelSource
```

If you use inline dataset, you see the following source options in mapping data flow.

![Excel source inline dataset](media/data-flow/excel-source-inline-dataset.png)

The associated data flow script is:

```
source(allowSchemaDrift: true,
	validateSchema: false,
	format: 'excel',
	fileSystem: 'container',
	folderPath: 'path',
	fileName: 'sample.xls',
	sheetName: 'worksheet',
	firstRowAsHeader: true) ~> ExcelSourceInlineDataset
```

## Next steps

- [Copy activity overview](copy-activity-overview.md)
- [Lookup activity](control-flow-lookup-activity.md)
- [GetMetadata activity](control-flow-get-metadata-activity.md)
