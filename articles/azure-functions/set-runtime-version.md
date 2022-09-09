---
title: How to target Azure Functions runtime versions
description: Azure Functions supports multiple versions of the runtime. Learn how to specify the runtime version of a function app hosted in Azure.

ms.topic: conceptual
ms.date: 07/22/2020
---

# How to target Azure Functions runtime versions

A function app runs on a specific version of the Azure Functions runtime. There are three major versions: [1.x, 2.x, and 3.x](functions-versions.md). By default, function apps are created in version 3.x of the runtime. This article explains how to configure a function app in Azure to run on the version you choose. For information about how to configure a local development environment for a specific version, see [Code and test Azure Functions locally](functions-run-local.md).

The way that you manually target a specific version depends on whether you are running Windows or Linux.

## Automatic and manual version updates

_This section doesn't apply when running your function app [on Linux](#manual-version-updates-on-linux)._

Azure Functions lets you target a specific version of the runtime on Windows by using the `FUNCTIONS_EXTENSION_VERSION` application setting in a function app. The function app is kept on the specified major version until you explicitly choose to move to a new version. If you specify only the major version, the function app is automatically updated to new minor versions of the runtime when they become available. New minor versions shouldn't introduce breaking changes. 

If you specify a minor version (for example, "2.0.12345"), the function app is pinned to that specific version until you explicitly change it. Older minor versions are regularly removed from the production environment. After this occurs, your function app runs on the latest version instead of the version set in `FUNCTIONS_EXTENSION_VERSION`. Because of this, you should quickly resolve any issues with your function app that require a specific minor version, so that you can instead target the major version. Minor version removals are announced in [App Service announcements](https://github.com/Azure/app-service-announcements/issues).

> [!NOTE]
> If you pin to a specific major version of Azure Functions, and then try to publish to Azure using Visual Studio, a dialog window will pop up prompting you to update to the latest version or cancel the publish. To avoid this, add the `<DisableFunctionExtensionVersionUpdate>true</DisableFunctionExtensionVersionUpdate>` property in your `.csproj` file.

When a new version is publicly available, a prompt in the portal gives you the chance to move up to that version. After moving to a new version, you can always use the `FUNCTIONS_EXTENSION_VERSION` application setting to move back to a previous version.

The following table shows the `FUNCTIONS_EXTENSION_VERSION` values for each major version to enable automatic updates:

| Major version | `FUNCTIONS_EXTENSION_VERSION` value |
| ------------- | ----------------------------------- |
| 3.x  | `~3` |
| 2.x  | `~2` |
| 1.x  | `~1` |

A change to the runtime version causes a function app to restart.

## View and update the current runtime version

_This section doesn't apply when running your function app [on Linux](#manual-version-updates-on-linux)._

You can change the runtime version used by your function app. Because of the potential of breaking changes, you can only change the runtime version before you have created any functions in your function app. 

> [!IMPORTANT]
> Although the runtime version is determined by the `FUNCTIONS_EXTENSION_VERSION` setting, you should make this change in the Azure portal and not by changing the setting directly. This is because the portal validates your changes and makes other related changes as needed.

# [Portal](#tab/portal)

[!INCLUDE [Set the runtime version in the portal](../../includes/functions-view-update-version-portal.md)]

> [!NOTE]
> Using the Azure portal, you can't change the runtime version for a function app that already contains functions.

# [Azure CLI](#tab/azurecli)

You can also view and set the `FUNCTIONS_EXTENSION_VERSION` from the Azure CLI.  

Using the Azure CLI, view the current runtime version with the [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings) command.

```azurecli-interactive
az functionapp config appsettings list --name <function_app> \
--resource-group <my_resource_group>
```

In this code, replace `<function_app>` with the name of your function app. Also replace `<my_resource_group>` with the name of the resource group for your function app. 

You see the `FUNCTIONS_EXTENSION_VERSION` in the following output, which has been truncated for clarity:

```output
[
  {
    "name": "FUNCTIONS_EXTENSION_VERSION",
    "slotSetting": false,
    "value": "~2"
  },
  {
    "name": "FUNCTIONS_WORKER_RUNTIME",
    "slotSetting": false,
    "value": "dotnet"
  },
  
  ...
  
  {
    "name": "WEBSITE_NODE_DEFAULT_VERSION",
    "slotSetting": false,
    "value": "8.11.1"
  }
]
```

You can update the `FUNCTIONS_EXTENSION_VERSION` setting in the function app with the [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings) command.

```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP> \
--resource-group <RESOURCE_GROUP> \
--settings FUNCTIONS_EXTENSION_VERSION=<VERSION>
```

Replace `<FUNCTION_APP>` with the name of your function app. Also replace `<RESOURCE_GROUP>` with the name of the resource group for your function app. Also, replace `<VERSION>` with either a specific version, or `~3`, `~2`, or `~1`.

You can run this command from the [Azure Cloud Shell](../cloud-shell/overview.md) by choosing **Try it** in the preceding code sample. You can also use the [Azure CLI locally](/cli/azure/install-azure-cli) to execute this command after executing [az login](/cli/azure/reference-index#az-login) to sign in.

# [PowerShell](#tab/powershell)

To check the Azure Functions runtime, use the following cmdlet: 

```powershell
Get-AzFunctionAppSetting -Name "<FUNCTION_APP>" -ResourceGroupName "<RESOURCE_GROUP>"
```

Replace `<FUNCTION_APP>` with the name of your function app and `<RESOURCE_GROUP>`. The current value of the `FUNCTIONS_EXTENSION_VERSION` setting is returned in the hash table.

Use the following script to change the Functions runtime:

```powershell
Update-AzFunctionAppSetting -Name "<FUNCTION_APP>" -ResourceGroupName "<RESOURCE_GROUP>" -AppSetting @{"FUNCTIONS_EXTENSION_VERSION" = "<VERSION>"} -Force
```

As before, replace `<FUNCTION_APP>` with the name of your function app and `<RESOURCE_GROUP>` with the name of the resource group. Also, replace `<VERSION>` with the specific version or major version, such as `~2` or `~3`. You can verify the updated value of the `FUNCTIONS_EXTENSION_VERSION` setting in the returned hash table. 

---

The function app restarts after the change is made to the application setting.

## Manual version updates on Linux

To pin a Linux function app to a specific host version, you specify the image URL in the 'LinuxFxVersion' field in site config. For example: if we want to pin a node 10 function app to say host version 3.0.13142 -

For **linux app service/elastic premium apps** -
Set `LinuxFxVersion` to `DOCKER|mcr.microsoft.com/azure-functions/node:3.0.13142-node10-appservice`.

For **linux consumption apps** -
Set `LinuxFxVersion` to `DOCKER|mcr.microsoft.com/azure-functions/mesh:3.0.13142-node10`.


# [Azure CLI](#tab/azurecli-linux)

You can view and set the `LinuxFxVersion` from the Azure CLI.  

Using the Azure CLI, view the current runtime version with the [az functionapp config show](/cli/azure/functionapp/config) command.

```azurecli-interactive
az functionapp config show --name <function_app> \
--resource-group <my_resource_group>
```

In this code, replace `<function_app>` with the name of your function app. Also replace `<my_resource_group>` with the name of the resource group for your function app. 

You see the `linuxFxVersion` in the following output, which has been truncated for clarity:

```output
{
  ...

  "kind": null,
  "limits": null,
  "linuxFxVersion": <LINUX_FX_VERSION>,
  "loadBalancing": "LeastRequests",
  "localMySqlEnabled": false,
  "location": "West US",
  "logsDirectorySizeLimit": 35,
   ...
}
```

You can update the `linuxFxVersion` setting in the function app with the [az functionapp config set](/cli/azure/functionapp/config) command.

```azurecli-interactive
az functionapp config set --name <FUNCTION_APP> \
--resource-group <RESOURCE_GROUP> \
--linux-fx-version <LINUX_FX_VERSION>
```

Replace `<FUNCTION_APP>` with the name of your function app. Also replace `<RESOURCE_GROUP>` with the name of the resource group for your function app. Also, replace `<LINUX_FX_VERSION>` with the values explained above.

You can run this command from the [Azure Cloud Shell](../cloud-shell/overview.md) by choosing **Try it** in the preceding code sample. You can also use the [Azure CLI locally](/cli/azure/install-azure-cli) to execute this command after executing [az login](/cli/azure/reference-index#az-login) to sign in.


Similarly, the function app restarts after the change is made to the site config.

> [!NOTE]
> Note that setting `LinuxFxVersion` to image url directly for consumption apps will opt them out of placeholders and other cold start optimizations.

---

## Next steps

> [!div class="nextstepaction"]
> [Target the 2.0 runtime in your local development environment](functions-run-local.md)

> [!div class="nextstepaction"]
> [See Release notes for runtime versions](https://github.com/Azure/azure-webjobs-sdk-script/releases)
