---
title: App settings reference for Azure Functions
description: Reference documentation for the Azure Functions app settings or environment variables.
ms.topic: conceptual
ms.date: 09/22/2018
---

# App settings reference for Azure Functions

App settings in a function app contain global configuration options that affect all functions for that function app. When you run locally, these settings are accessed as local [environment variables](functions-run-local.md#local-settings-file). This article lists the app settings that are available in function apps.

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

There are other global configuration options in the [host.json](functions-host-json.md) file and in the [local.settings.json](functions-run-local.md#local-settings-file) file.

> [!NOTE]  
> You can use application settings to override host.json setting values without having to change the host.json file itself. This is helpful for scenarios where you need to configure or modify specific host.json settings for a specific environment. This also lets you change host.json settings without having to republish your project. To learn more, see the [host.json reference article](functions-host-json.md#override-hostjson-values).  

## APPINSIGHTS_INSTRUMENTATIONKEY

The instrumentation key for Application Insights. Only use one of `APPINSIGHTS_INSTRUMENTATIONKEY` or `APPLICATIONINSIGHTS_CONNECTION_STRING`. For more information, see [Monitor Azure Functions](functions-monitoring.md). 

|Key|Sample value|
|---|------------|
|APPINSIGHTS_INSTRUMENTATIONKEY|55555555-af77-484b-9032-64f83bb83bb|

## APPLICATIONINSIGHTS_CONNECTION_STRING

The connection string for Application Insights. Use `APPLICATIONINSIGHTS_CONNECTION_STRING` instead of `APPINSIGHTS_INSTRUMENTATIONKEY` when your function app requires the added customizations supported by using the connection string. For more information, see [Connection strings](../azure-monitor/app/sdk-connection-string.md). 

|Key|Sample value|
|---|------------|
|APPLICATIONINSIGHTS_CONNECTION_STRING|InstrumentationKey=[key];IngestionEndpoint=[url];LiveEndpoint=[url];ProfilerEndpoint=[url];SnapshotEndpoint=[url];|

## AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL

By default, [Functions proxies](functions-proxies.md) use a shortcut to send API calls from proxies directly to functions in the same function app. This shortcut is used instead of creating a new HTTP request. This setting allows you to disable that shortcut behavior.

|Key|Value|Description|
|-|-|-|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|true|Calls with a backend URL pointing to a function in the local function app won't be sent directly to the function. Instead, the requests are directed back to the HTTP frontend for the function app.|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|false|Calls with a backend URL pointing to a function in the local function app are forwarded directly to the function. This is the default value. |

## AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES

This setting controls whether the characters `%2F` are decoded as slashes in route parameters when they are inserted into the backend URL. 

|Key|Value|Description|
|-|-|-|
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|true|Route parameters with encoded slashes are decoded. |
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|false|All route parameters are passed along unchanged, which is the default behavior. |

For example, consider the proxies.json file for a function app at the `myfunction.com` domain.

```JSON
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "root": {
            "matchCondition": {
                "route": "/{*all}"
            },
            "backendUri": "example.com/{all}"
        }
    }
}
```

When `AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES` is set to `true`, the URL `example.com/api%2ftest` resolves to `example.com/api/test`. By default, the URL remains unchanged as `example.com/test%2fapi`. For more information, see [Functions proxies](functions-proxies.md).

## AZURE_FUNCTIONS_ENVIRONMENT

In version 2.x and later versions of the Functions runtime, configures app behavior based on the runtime environment. This value is [read during initialization](https://github.com/Azure/azure-functions-host/blob/dev/src/WebJobs.Script.WebHost/Program.cs#L43). You can set `AZURE_FUNCTIONS_ENVIRONMENT` to any value, but [three values](/dotnet/api/microsoft.aspnetcore.hosting.environmentname) are supported: [Development](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.development), [Staging](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.staging), and [Production](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.production). When `AZURE_FUNCTIONS_ENVIRONMENT` isn't set,  it defaults to `Development` on a local environment and `Production` on Azure. This setting should be used instead of `ASPNETCORE_ENVIRONMENT` to set the runtime environment. 

## AzureFunctionsJobHost__\*

In version 2.x and later versions of the Functions runtime, application settings can override [host.json](functions-host-json.md) settings in the current environment. These overrides are expressed as application settings named `AzureFunctionsJobHost__path__to__setting`. For more information, see [Override host.json values](functions-host-json.md#override-hostjson-values).

## AzureWebJobsDashboard

Optional storage account connection string for storing logs and displaying them in the **Monitor** tab in the portal. This setting is only valid for apps that target version 1.x of the Azure Functions runtime. The storage account must be a general-purpose one that supports blobs, queues, and tables. To learn more, see [Storage account requirements](storage-considerations.md#storage-account-requirements).

|Key|Sample value|
|---|------------|
|AzureWebJobsDashboard|DefaultEndpointsProtocol=https;AccountName=<name>;AccountKey=<key>|

> [!NOTE]
> For better performance and experience, runtime version 2.x and later versions use APPINSIGHTS_INSTRUMENTATIONKEY and App Insights for monitoring instead of `AzureWebJobsDashboard`.

## AzureWebJobsDisableHomepage

`true` means disable the default landing page that is shown for the root URL of a function app. Default is `false`.

|Key|Sample value|
|---|------------|
|AzureWebJobsDisableHomepage|true|

When this app setting is omitted or set to `false`, a page similar to the following example is displayed in response to the URL `<functionappname>.azurewebsites.net`.

![Function app landing page](media/functions-app-settings/function-app-landing-page.png)

## AzureWebJobsDotNetReleaseCompilation

`true` means use Release mode when compiling .NET code; `false` means use Debug mode. Default is `true`.

|Key|Sample value|
|---|------------|
|AzureWebJobsDotNetReleaseCompilation|true|

## AzureWebJobsFeatureFlags

A comma-delimited list of beta features to enable. Beta features enabled by these flags are not production ready, but can be enabled for experimental use before they go live.

|Key|Sample value|
|---|------------|
|AzureWebJobsFeatureFlags|feature1,feature2|

## AzureWebJobsSecretStorageType

Specifies the repository or provider to use for key storage. Currently, the supported repositories are blob storage ("Blob") and the local file system ("Files"). The default is blob in version 2 and file system in version 1.

|Key|Sample value|
|---|------------|
|AzureWebJobsSecretStorageType|Files|

## AzureWebJobsStorage

The Azure Functions runtime uses this storage account connection string for all functions except for HTTP triggered functions. The storage account must be a general-purpose one that supports blobs, queues, and tables. See [Storage account](functions-infrastructure-as-code.md#storage-account) and [Storage account requirements](storage-considerations.md#storage-account-requirements).

|Key|Sample value|
|---|------------|
|AzureWebJobsStorage|DefaultEndpointsProtocol=https;AccountName=[name];AccountKey=[key]|

## AzureWebJobs_TypeScriptPath

Path to the compiler used for TypeScript. Allows you to override the default if you need to.

|Key|Sample value|
|---|------------|
|AzureWebJobs_TypeScriptPath|%HOME%\typescript|

## FUNCTION\_APP\_EDIT\_MODE

Dictates whether editing in the Azure portal is enabled. Valid values are "readwrite" and "readonly".

|Key|Sample value|
|---|------------|
|FUNCTION\_APP\_EDIT\_MODE|readonly|

## FUNCTIONS\_EXTENSION\_VERSION

The version of the Functions runtime to use in this function app. A tilde with major version means use the latest version of that major version (for example, "~2"). When new versions for the same major version are available, they are automatically installed in the function app. To pin the app to a specific version, use the full version number (for example, "2.0.12345"). Default is "~2". A value of `~1` pins your app to version 1.x of the runtime.

|Key|Sample value|
|---|------------|
|FUNCTIONS\_EXTENSION\_VERSION|~2|

## FUNCTIONS\_V2\_COMPATIBILITY\_MODE

This setting enables your function app to run in a version 2.x compatible mode on the version 3.x runtime. Use this setting only if encountering issues when [upgrading your function app from version 2.x to 3.x of the runtime](functions-versions.md#migrating-from-2x-to-3x). 

>[!IMPORTANT]
> This setting is intended only as a short-term workaround while you update your app to run correctly on version 3.x. This setting is supported as long as the [2.x runtime is supported](functions-versions.md). If you encounter issues that prevent your app from running on version 3.x without using this setting, please [report your issue](https://github.com/Azure/azure-functions-host/issues/new?template=Bug_report.md).

Requires that [FUNCTIONS\_EXTENSION\_VERSION](functions-app-settings.md#functions_extension_version) be set to `~3`.

|Key|Sample value|
|---|------------|
|FUNCTIONS\_V2\_COMPATIBILITY\_MODE|true|

## FUNCTIONS\_WORKER\_PROCESS\_COUNT

Specifies the maximum number of language worker processes, with a default value of `1`. The maximum value allowed is `10`. Function invocations are evenly distributed among language worker processes. Language worker processes are spawned every 10 seconds until the count set by FUNCTIONS\_WORKER\_PROCESS\_COUNT is reached. Using multiple language worker processes is not the same as [scaling](functions-scale.md). Consider using this setting when your workload has a mix of CPU-bound and I/O-bound invocations. This setting applies to all non-.NET languages.

|Key|Sample value|
|---|------------|
|FUNCTIONS\_WORKER\_PROCESS\_COUNT|2|


## FUNCTIONS\_WORKER\_RUNTIME

The language worker runtime to load in the function app.  This will correspond to the language being used in your application (for example, "dotnet"). For functions in multiple languages you will need to publish them to multiple apps, each with a corresponding worker runtime value.  Valid values are `dotnet` (C#/F#), `node` (JavaScript/TypeScript), `java` (Java), `powershell` (PowerShell), and `python` (Python).

|Key|Sample value|
|---|------------|
|FUNCTIONS\_WORKER\_RUNTIME|dotnet|

## PIP\_EXTRA\_INDEX\_URL

The value for this setting indicates a custom package index URL for Python apps. Use this setting when you need to run a remote build using custom dependencies that are found in an extra package index.   

|Key|Sample value|
|---|------------|
|PIP\_EXTRA\_INDEX\_URL|http://my.custom.package.repo/simple |

To learn more, see [Custom dependencies](functions-reference-python.md#remote-build-with-extra-index-url) in the Python developer reference.

## SCALE\_CONTROLLER\_LOGGING\_ENABLE

_This setting is currently in preview._  

This setting controls logging from the Azure Functions scale controller. For more information, see [Scale controller logs](functions-monitoring.md#scale-controller-logs-preview).

|Key|Sample value|
|-|-|
|SCALE_CONTROLLER_LOGGING_ENABLE|AppInsights:Verbose|

The value for this key is supplied in the format `<DESTINATION>:<VERBOSITY>`, which is defined as follows:

[!INCLUDE [functions-scale-controller-logging](../../includes/functions-scale-controller-logging.md)]

## WEBSITE\_CONTENTAZUREFILECONNECTIONSTRING

For Consumption & Premium plans only. Connection string for storage account where the function app code and configuration are stored. See [Create a function app](functions-infrastructure-as-code.md#create-a-function-app).

|Key|Sample value|
|---|------------|
|WEBSITE_CONTENTAZUREFILECONNECTIONSTRING|DefaultEndpointsProtocol=https;AccountName=[name];AccountKey=[key]|

## WEBSITE\_CONTENTSHARE

For Consumption & Premium plans only. The file path to the function app code and configuration. Used with WEBSITE_CONTENTAZUREFILECONNECTIONSTRING. Default is a unique string that begins with the function app name. See [Create a function app](functions-infrastructure-as-code.md#create-a-function-app).

|Key|Sample value|
|---|------------|
|WEBSITE_CONTENTSHARE|functionapp091999e2|

## WEBSITE\_MAX\_DYNAMIC\_APPLICATION\_SCALE\_OUT

The maximum number of instances that the function app can scale out to. Default is no limit.

> [!IMPORTANT]
> This setting is in preview.  An [app property for function max scale out](./functions-scale.md#limit-scale-out) has been added and is the recommended way to limit scale out.

|Key|Sample value|
|---|------------|
|WEBSITE\_MAX\_DYNAMIC\_APPLICATION\_SCALE\_OUT|5|

## WEBSITE\_NODE\_DEFAULT_VERSION

_Windows only._  
Sets the version of Node.js to use when running your function app on Windows. You should use a tilde (~) to have the runtime use the latest available version of the targeted major version. For example, when set to `~10`, the latest version of Node.js 10 is used. When a major version is targeted with a tilde, you don't have to manually update the minor version. 

|Key|Sample value|
|---|------------|
|WEBSITE\_NODE\_DEFAULT_VERSION|~10|

## WEBSITE\_RUN\_FROM\_PACKAGE

Enables your function app to run from a mounted package file.

|Key|Sample value|
|---|------------|
|WEBSITE\_RUN\_FROM\_PACKAGE|1|

Valid values are either a URL that resolves to the location of a deployment package file, or `1`. When set to `1`, the package must be in the `d:\home\data\SitePackages` folder. When using zip deployment with this setting, the package is automatically uploaded to this location. In preview, this setting was named `WEBSITE_RUN_FROM_ZIP`. For more information, see [Run your functions from a package file](run-functions-from-deployment-package.md).

## WEBSITE\_TIME\_ZONE

Allows you to set the timezone for your function app. 

|Key|OS|Sample value|
|---|--|------------|
|WEBSITE\_TIME\_ZONE|Windows|Eastern Standard Time|
|WEBSITE\_TIME\_ZONE|Linux|America/New_York|

[!INCLUDE [functions-timezone](../../includes/functions-timezone.md)]

## Next steps

[Learn how to update app settings](functions-how-to-use-azure-function-app-settings.md#settings)

[See global settings in the host.json file](functions-host-json.md)

[See other app settings for App Service apps](https://github.com/projectkudu/kudu/wiki/Configurable-settings)
