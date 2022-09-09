---
title: Use JSON content-type for key-values
titleSuffix: Azure App Configuration
description: Learn how to use JSON content-type for key-values
services: azure-app-configuration
author: avanigupta
ms.assetid: 
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: how-to
ms.date: 08/03/2020
ms.author: avgupta


#Customer intent: I want to store JSON key-values in App Configuration store without losing the data type of each setting.
---

# Leverage content-type to store JSON key-values in App Configuration

Data is stored in App Configuration as key-values, where values are treated as the string type by default. However, you can specify a custom type by leveraging the content-type property associated with each key-value, so that you can preserve the original type of your data or have your application behave differently based on content-type.


## Overview

In App Configuration, you can use the JSON media type as the content-type of your key-values to avail benefits like:
- **Simpler data management**: Managing key-values, like arrays, will become a lot easier in the Azure portal.
- **Enhanced data export**: Primitive types, arrays, and JSON objects will be preserved during data export.
- **Native support with App Configuration provider**: Key-values with JSON content-type will work fine when consumed by App Configuration provider libraries in your applications.

#### Valid JSON content-type

Media types, as defined [here](https://www.iana.org/assignments/media-types/media-types.xhtml), can be assigned to the content-type associated with each key-value.
A media type consists of a type and a subtype. If the type is `application` and the subtype (or suffix) is `json`, the media type will be considered a valid JSON content-type.
Some examples of valid JSON content-types are:

- application/json
- application/activity+json
- application/vnd.foobar+json;charset=utf-8

#### Valid JSON values

When a key-value has JSON content-type, its value must be in valid JSON format for clients to process it correctly. Otherwise, clients may fail or fall back and treat it as string format.
Some examples of valid JSON values are:

- "John Doe"
- 723
- false
- null
- "2020-01-01T12:34:56.789Z"
- [1, 2, 3, 4]
- {"ObjectSetting":{"Targeting":{"Default":true,"Level":"Information"}}}

> [!NOTE]
> For the rest of this article, any key-value in App Configuration that has a valid JSON content-type and a valid JSON value will be referred to as **JSON key-value**. 

In this tutorial, you'll learn how to:
> [!div class="checklist"]
> * Create JSON key-values in App Configuration.
> * Import JSON key-values from a JSON file.
> * Export JSON key-values to a JSON file.
> * Consume JSON key-values in your applications.


## Prerequisites

- Azure subscription - [create one for free](https://azure.microsoft.com/free/).
- Latest version of Azure CLI (2.10.0 or later). To find the version, run `az --version`. If you need to install or upgrade, see [Install Azure CLI](/cli/azure/install-azure-cli). If you are using Azure CLI, you must first sign in using `az login`. You can optionally use the Azure Cloud Shell.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]


## Create an App Configuration store

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]


## Create JSON key-values in App Configuration

JSON key-values can be created using Azure portal, Azure CLI or by importing from a JSON file. In this section, you will find instructions on creating the same JSON key-values using all three methods.

### Create JSON key-values using Azure portal

Browse to your App Configuration store, and select **Configuration Explorer** > **Create** > **Key-value** to add the following key-value pairs:

| Key | Value | Content Type |
|---|---|---|
| Settings:BackgroundColor | "Green" | application/json |
| Settings:FontSize | 24 | application/json |
| Settings:UseDefaultRouting | false | application/json |
| Settings:BlockedUsers | null | application/json |
| Settings:ReleaseDate | "2020-08-04T12:34:56.789Z" | application/json |
| Settings:RolloutPercentage | [25,50,75,100] | application/json |
| Settings:Logging | {"Test":{"Level":"Debug"},"Prod":{"Level":"Warning"}} | application/json |

Leave **Label** empty and select **Apply**.

### Create JSON key-values using Azure CLI

The following commands will create JSON key-values in your App Configuration store. Replace `<appconfig_name>` with the name of your App Configuration store.

```azurecli-interactive
appConfigName=<appconfig_name>
az appconfig kv set -n $appConfigName --content-type application/json --key Settings:BackgroundColor --value \"Green\"
az appconfig kv set -n $appConfigName --content-type application/json --key Settings:FontSize --value 24
az appconfig kv set -n $appConfigName --content-type application/json --key Settings:UseDefaultRouting --value false
az appconfig kv set -n $appConfigName --content-type application/json --key Settings:BlockedUsers --value null
az appconfig kv set -n $appConfigName --content-type application/json --key Settings:ReleaseDate --value \"2020-08-04T12:34:56.789Z\"
az appconfig kv set -n $appConfigName --content-type application/json --key Settings:RolloutPercentage --value [25,50,75,100]
az appconfig kv set -n $appConfigName --content-type application/json --key Settings:Logging --value {\"Test\":{\"Level\":\"Debug\"},\"Prod\":{\"Level\":\"Warning\"}}
```

> [!IMPORTANT]
> If you are using Azure CLI or Azure Cloud Shell to create JSON key-values, the value provided must be an escaped JSON string.

### Import JSON key-values from a file

Create a JSON file called `Import.json` with the following content and import as key-values into App Configuration:

```json
{
  "Settings": {
    "BackgroundColor": "Green",
    "BlockedUsers": null,
    "FontSize": 24,
    "Logging": {"Test":{"Level":"Debug"},"Prod":{"Level":"Warning"}},
    "ReleaseDate": "2020-08-04T12:34:56.789Z",
    "RolloutPercentage": [25,50,75,100],
    "UseDefaultRouting": false
  }
}
```

```azurecli-interactive
az appconfig kv import -s file --format json --path "~/Import.json" --content-type "application/json" --separator : --depth 2
```

> [!Note]
> The `--depth` argument is used for flattening hierarchical data from a file into key-value pairs. In this tutorial, depth is specified for demonstrating that you can also store JSON objects as values in App Configuration. If depth is not specified, JSON objects will be flattened to the deepest level by default.

The JSON key-values you created should look like this in App Configuration:

![Config store containing JSON key-values](./media/create-json-settings.png)


## Export JSON key-values to a file

One of the major benefits of using JSON key-values is the ability to preserve the original data type of your values while exporting. If a key-value in App Configuration doesn't have JSON content-type, its value will be treated as string. 

Consider these key-values without JSON content-type:

| Key | Value | Content Type |
|---|---|---|
| Settings:FontSize | 24 | |
| Settings:UseDefaultRouting | false | |

When you export these key-values to a JSON file, the values will be exported as strings:
```json
{
  "Settings": {
    "FontSize": "24",
    "UseDefaultRouting": "false"
  }
}
```

However, when you export JSON key-values to a file, all values will preserve their original data type. To verify this, export key-values from your App Configuration to a JSON file. You'll see that the exported file has the same contents as the `Import.json` file you previously imported.

```azurecli-interactive
az appconfig kv export -d file --format json --path "~/Export.json" --separator :
```

> [!NOTE]
> If your App Configuration store has some key-values without JSON content-type, they will also be exported to the same file in string format. If you want to export only the JSON key-values, assign a unique label or prefix to your JSON key-values and use label or prefix filtering during export.


## Consuming JSON key-values in applications

The easiest way to consume JSON key-values in your application is through App Configuration provider libraries. With the provider libraries, you don't need to implement special handling of JSON key-values in your application. They're always deserialized for your application in the same way that other JSON configuration provider libraries do. 

> [!Important]
> Native support for JSON key-values is available in .NET configuration provider version 4.0.0 (or later). See [*Next steps*](#next-steps) section for more details.

If you are using the SDK or REST API to read key-values from App Configuration, based on the content-type, your application is responsible for deserializing the value of a JSON key-value using any standard JSON deserializer.


## Clean up resources

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## Next steps

Now that you know how to work with JSON key-values in your App Configuration store, create an application for consuming these key-values:

* [ASP.NET Core quickstart](./quickstart-aspnet-core-app.md)
    * Prerequisite: [Microsoft.Azure.AppConfiguration.AspNetCore](https://www.nuget.org/packages/Microsoft.Azure.AppConfiguration.AspNetCore) package v4.0.0 or later.

* [.NET Core quickstart](./quickstart-dotnet-core-app.md)
    * Prerequisite: [Microsoft.Extensions.Configuration.AzureAppConfiguration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureAppConfiguration) package v4.0.0 or later.
