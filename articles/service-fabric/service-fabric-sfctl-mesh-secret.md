---
title: Azure Service Fabric CLI- sfctl mesh secret 
description: Learn about sfctl, the Azure Service Fabric command line interface. Includes a list of commands for getting and deleting Service Fabric Mesh secret resources.
author: jeffj6123

ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
---

# sfctl mesh secret
Get and delete mesh secret resources.

## Commands

|Command|Description|
| --- | --- |
| delete | Deletes the Secret resource. |
| list | Lists all the secret resources. |
| show | Gets the Secret resource with the given name. |

## sfctl mesh secret delete
Deletes the Secret resource.

Deletes the specified Secret resource and all of its named values.

### Arguments

|Argument|Description|
| --- | --- |
| --name -n [Required] | The name of the secret resource. |

### Global Arguments

|Argument|Description|
| --- | --- |
| --debug | Increase logging verbosity to show all debug logs. |
| --help -h | Show this help message and exit. |
| --output -o | Output format.  Allowed values\: json, jsonc, table, tsv.  Default\: json. |
| --query | JMESPath query string. See http\://jmespath.org/ for more information and examples. |
| --verbose | Increase logging verbosity. Use --debug for full debug logs. |

## sfctl mesh secret list
Lists all the secret resources.

Gets the information about all secret resources in a given resource group. The information include the description and other properties of the Secret.

### Global Arguments

|Argument|Description|
| --- | --- |
| --debug | Increase logging verbosity to show all debug logs. |
| --help -h | Show this help message and exit. |
| --output -o | Output format.  Allowed values\: json, jsonc, table, tsv.  Default\: json. |
| --query | JMESPath query string. See http\://jmespath.org/ for more information and examples. |
| --verbose | Increase logging verbosity. Use --debug for full debug logs. |

## sfctl mesh secret show
Gets the Secret resource with the given name.

Gets the information about the Secret resource with the given name. The information include the description and other properties of the Secret.

### Arguments

|Argument|Description|
| --- | --- |
| --name -n [Required] | The name of the secret resource. |

### Global Arguments

|Argument|Description|
| --- | --- |
| --debug | Increase logging verbosity to show all debug logs. |
| --help -h | Show this help message and exit. |
| --output -o | Output format.  Allowed values\: json, jsonc, table, tsv.  Default\: json. |
| --query | JMESPath query string. See http\://jmespath.org/ for more information and examples. |
| --verbose | Increase logging verbosity. Use --debug for full debug logs. |


## Next steps
- [Set up](service-fabric-cli.md) the Service Fabric CLI.
- Learn how to use the Service Fabric CLI using the [sample scripts](./scripts/sfctl-upgrade-application.md).
