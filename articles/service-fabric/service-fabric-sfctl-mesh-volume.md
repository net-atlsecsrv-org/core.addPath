---
title: Azure Service Fabric CLI- sfctl mesh volume 
description: Describes the Service Fabric CLI sfctl mesh volume commands.
author: jeffj6123

ms.topic: reference
ms.date: 9/17/2019
ms.author: jejarry
---

# sfctl mesh volume
Get and delete volume resources.

## Commands

|Command|Description|
| --- | --- |
| delete | Deletes the Volume resource. |
| list | Lists all the volume resources. |
| show | Gets the Volume resource with the given name. |

## sfctl mesh volume delete
Deletes the Volume resource.

Deletes the Volume resource identified by the name.

### Arguments

|Argument|Description|
| --- | --- |
| --name -n [Required] | The name of the volume. |

### Global Arguments

|Argument|Description|
| --- | --- |
| --debug | Increase logging verbosity to show all debug logs. |
| --help -h | Show this help message and exit. |
| --output -o | Output format.  Allowed values\: json, jsonc, table, tsv.  Default\: json. |
| --query | JMESPath query string. See http\://jmespath.org/ for more information and examples. |
| --verbose | Increase logging verbosity. Use --debug for full debug logs. |

## sfctl mesh volume list
Lists all the volume resources.

Gets the information about all volume resources in a given resource group. The information include the description and other properties of the Volume.

### Global Arguments

|Argument|Description|
| --- | --- |
| --debug | Increase logging verbosity to show all debug logs. |
| --help -h | Show this help message and exit. |
| --output -o | Output format.  Allowed values\: json, jsonc, table, tsv.  Default\: json. |
| --query | JMESPath query string. See http\://jmespath.org/ for more information and examples. |
| --verbose | Increase logging verbosity. Use --debug for full debug logs. |

## sfctl mesh volume show
Gets the Volume resource with the given name.

Gets the information about the Volume resource with the given name. The information include the description and other properties of the Volume.

### Arguments

|Argument|Description|
| --- | --- |
| --name -n [Required] | The name of the volume. |

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
- Learn how to use the Service Fabric CLI using the [sample scripts](/azure/service-fabric/scripts/sfctl-upgrade-application).