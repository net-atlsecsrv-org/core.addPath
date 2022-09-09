---
title: Copy data from Presto using Azure Data Factory (Preview) 
description: Learn how to copy data from Presto to supported sink data stores by using a copy activity in an Azure Data Factory pipeline.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl

ms.service: data-factory
ms.workload: data-services


ms.topic: conceptual
ms.date: 09/04/2019
ms.author: jingwang

---
# Copy data from Presto using Azure Data Factory (Preview)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

This article outlines how to use the Copy Activity in Azure Data Factory to copy data from Presto. It builds on the [copy activity overview](copy-activity-overview.md) article that presents a general overview of copy activity.

> [!IMPORTANT]
> This connector is currently in preview. You can try it out and give us feedback. If you want to take a dependency on preview connectors in your solution, please contact [Azure support](https://azure.microsoft.com/support/).

## Supported capabilities

This Presto connector is supported for the following activities:

- [Copy activity](copy-activity-overview.md) with [supported source/sink matrix](copy-activity-overview.md)
- [Lookup activity](control-flow-lookup-activity.md)

You can copy data from Presto to any supported sink data store. For a list of data stores that are supported as sources/sinks by the copy activity, see the [Supported data stores](copy-activity-overview.md#supported-data-stores-and-formats) table.

Azure Data Factory provides a built-in driver to enable connectivity, therefore you don't need to manually install any driver using this connector.

## Getting started

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

The following sections provide details about properties that are used to define Data Factory entities specific to Presto connector.

## Linked service properties

The following properties are supported for Presto linked service:

| Property | Description | Required |
|:--- |:--- |:--- |
| type | The type property must be set to: **Presto** | Yes |
| host | The IP address or host name of the Presto server. (e.g. 192.168.222.160)  | Yes |
| serverVersion | The version of the Presto server. (e.g. 0.148-t)  | Yes |
| catalog | The catalog context for all request against the server.  | Yes |
| port | The TCP port that the Presto server uses to listen for client connections. The default value is 8080.  | No |
| authenticationType | The authentication mechanism used to connect to the Presto server. <br/>Allowed values are: **Anonymous**, **LDAP** | Yes |
| username | The user name used to connect to the Presto server.  | No |
| password | The password corresponding to the user name. Mark this field as a SecureString to store it securely in Data Factory, or [reference a secret stored in Azure Key Vault](store-credentials-in-key-vault.md). | No |
| enableSsl | Specifies whether the connections to the server are encrypted using TLS. The default value is false.  | No |
| trustedCertPath | The full path of the .pem file containing trusted CA certificates for verifying the server when connecting over TLS. This property can only be set when using TLS on self-hosted IR. The default value is the cacerts.pem file installed with the IR.  | No |
| useSystemTrustStore | Specifies whether to use a CA certificate from the system trust store or from a specified PEM file. The default value is false.  | No |
| allowHostNameCNMismatch | Specifies whether to require a CA-issued TLS/SSL certificate name to match the host name of the server when connecting over TLS. The default value is false.  | No |
| allowSelfSignedServerCert | Specifies whether to allow self-signed certificates from the server. The default value is false.  | No |
| timeZoneID | The local time zone used by the connection. Valid values for this option are specified in the IANA Time Zone Database. The default value is the system time zone.  | No |

**Example:**

```json
{
    "name": "PrestoLinkedService",
    "properties": {
        "type": "Presto",
        "typeProperties": {
            "host" : "<host>",
            "serverVersion" : "0.148-t",
            "catalog" : "<catalog>",
            "port" : "<port>",
            "authenticationType" : "LDAP",
            "username" : "<username>",
            "password": {
                 "type": "SecureString",
                 "value": "<password>"
            },
            "timeZoneID" : "Europe/Berlin"
        }
    }
}
```

## Dataset properties

For a full list of sections and properties available for defining datasets, see the [datasets](concepts-datasets-linked-services.md) article. This section provides a list of properties supported by Presto dataset.

To copy data from Presto, set the type property of the dataset to **PrestoObject**. The following properties are supported:

| Property | Description | Required |
|:--- |:--- |:--- |
| type | The type property of the dataset must be set to: **PrestoObject** | Yes |
| schema | Name of the schema. |No (if "query" in activity source is specified)  |
| table | Name of the table. |No (if "query" in activity source is specified)  |
| tableName | Name of the table with schema. This property is supported for backward compatibility. Use `schema` and `table` for new workload. | No (if "query" in activity source is specified) |

**Example**

```json
{
    "name": "PrestoDataset",
    "properties": {
        "type": "PrestoObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Presto linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## Copy activity properties

For a full list of sections and properties available for defining activities, see the [Pipelines](concepts-pipelines-activities.md) article. This section provides a list of properties supported by Presto source.

### Presto as source

To copy data from Presto, set the source type in the copy activity to **PrestoSource**. The following properties are supported in the copy activity **source** section:

| Property | Description | Required |
|:--- |:--- |:--- |
| type | The type property of the copy activity source must be set to: **PrestoSource** | Yes |
| query | Use the custom SQL query to read data. For example: `"SELECT * FROM MyTable"`. | No (if "tableName" in dataset is specified) |

**Example:**

```json
"activities":[
    {
        "name": "CopyFromPresto",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Presto input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "PrestoSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## Lookup activity properties

To learn details about the properties, check [Lookup activity](control-flow-lookup-activity.md).


## Next steps
For a list of data stores supported as sources and sinks by the copy activity in Azure Data Factory, see [supported data stores](copy-activity-overview.md#supported-data-stores-and-formats).
