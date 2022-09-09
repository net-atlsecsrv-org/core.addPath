---
title: Enable VM extension using Azure CLI
description: This article describes how to deploy virtual machine extensions to Azure Arc enabled servers running in hybrid cloud environments using the Azure CLI.
ms.date: 11/20/2020
ms.topic: conceptual
ms.custom: devx-track-azurecli
---

# Enable Azure VM extensions using the Azure CLI

This article shows you how to deploy and uninstall VM extensions, supported by Azure Arc enabled servers, to a Linux or Windows hybrid machine using the Azure CLI.

[!INCLUDE [Azure CLI Prepare your environment](../../../includes/azure-cli-prepare-your-environment.md)]

## Install the Azure CLI extension

The ConnectedMachine commands aren't shipped as part of the Azure CLI. Before using the Azure CLI to manage VM extensions on your hybrid server managed by Arc enabled servers, you need to load the ConnectedMachine extension. Run the following command to get it:

```azurecli
az extension add --name connectedmachine
```

## Enable extension

To enable a VM extension on your Arc enabled server, use [az connectedmachine extension create](/cli/azure/ext/connectedmachine/connectedmachine/extension#ext_connectedmachine_az_connectedmachine_extension_create) with the `--machine-name`, `--extension-name`, `--location`, `--type`, `settings`, and `--publisher` parameters.

The following example enables the Log Analytics VM extension on an Arc enabled Linux server:

```azurecli
az connectedmachine extension create --machine-name "myMachineName" --name "OmsAgentforLinux" --location "eastus" --type "CustomScriptExtension" --publisher "Microsoft.EnterpriseCloud.Monitoring" --settings "{\"workspaceId\":\"workspaceId"}" --protected-settings "{\workspaceKey\":"\workspaceKey"} --type-handler-version "1.10" --resource-group "myResourceGroup"
```

The following example enables the Custom Script Extension on an Arc enabled server:

```azurecli
az connectedmachine extension create --machine-name "myMachineName" --name "CustomScriptExtension" --location "eastus" --type "CustomScriptExtension" --publisher "Microsoft.Compute" --settings "{\"commandToExecute\":\"powershell.exe -c \\\"Get-Process | Where-Object { $_.CPU -gt 10000 }\\\"\"}" --type-handler-version "1.10" --resource-group "myResourceGroup"
```

The following example enables the Key Vault VM extension (preview) on an Arc enabled server:

```azurecli
az connectedmachine extension create --resource-group "resourceGroupName" --machine-name "myMachineName" --location "regionName" --publisher "Microsoft.Azure.KeyVault" --type "KeyVaultForLinux or KeyVaultForWindows" --name "KeyVaultForLinux or KeyVaultForWindows" --settings '{"secretsManagementSettings": { "pollingIntervalInS": "60", "observedCertificates": ["observedCert1"] }, "authenticationSettings": { "msiEndpoint": "http://localhost:40342/metadata/identity" }}'
```

## List extensions installed

To get a list of the VM extensions on your Arc enabled server, use [az connectedmachine extension list](/cli/azure/ext/connectedmachine/connectedmachine/extension#ext_connectedmachine_az_connectedmachine_extension_list) with the `--machine-name` and `--resource-group` parameters.

Example:

```azurecli
az connectedmachine extension list --machine-name "myMachineName" --resource-group "myResourceGroup"
```

By default, the output of Azure CLI commands is in JSON (JavaScript Object Notation). To change the default output to a list or table, for example, use [az configure --output](/cli/azure/reference-index). You can also add `--output` to any command for a one time change in output format.

The following example shows the partial JSON output from the `az connectedmachine extension -list` command:

```json
[
  {
    "autoUpgradingMinorVersion": "false",
    "forceUpdateTag": null,
    "id": "/subscriptions/subscriptionId/resourceGroups/resourceGroupName/providers/Microsoft.HybridCompute/machines/SVR01/extensions/DependencyAgentWindows",
    "location": "eastus",
    "name": "DependencyAgentWindows",
    "namePropertiesInstanceViewName": "DependencyAgentWindows",
```

## Remove an installed extension

To remove an installed VM extension on your Arc enabled server, use [az connectedmachine extension delete](/cli/azure/ext/connectedmachine/connectedmachine/extension#ext_connectedmachine_az_connectedmachine_extension_delete) with the `--extension-name`, `--machine-name` and `--resource-group` parameters.

For example, to remove the Log Analytics VM extension for Linux, run the following command:

```azurecli
az connectedmachine extension delete --machine-name "myMachineName" --name "OmsAgentforLinux" --resource-group "myResourceGroup"
```

## Next steps

- You can deploy, manage, and remove VM extensions using the [Azure PowerShell](manage-vm-extensions-powershell.md), from the [Azure portal](manage-vm-extensions-portal.md), or [Azure Resource Manager templates](manage-vm-extensions-template.md).

- Troubleshooting information can be found in the [Troubleshoot VM extensions guide](troubleshoot-vm-extensions.md).

- Review the Azure CLI VM extension [Overview](/cli/azure/ext/connectedmachine/connectedmachine/extension) article for more information about the commands.
