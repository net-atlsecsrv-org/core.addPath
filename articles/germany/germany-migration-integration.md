---
title: Migrate Azure integration resource, Azure Germany to global Azure
description: This article provides information about migrating your Azure integration resources from Azure Germany to global Azure.
ms.topic: article
ms.date: 10/16/2020
author: gitralf
ms.author: ralfwi 
ms.service: germany
ms.custom: bfmigrate
---

# Migrate integration resources to global Azure

[!INCLUDE [closureinfo](../../includes/germany-closure-info.md)]

This article has information that can help you migrate Azure integration resources from Azure Germany to global Azure.

## Service Bus

Azure Service Bus services don't have data export or import capabilities. To migrate Service Bus resources from Azure Germany to global Azure, you can export the resources [as an Azure Resource Manager template](../azure-resource-manager/templates/export-template-portal.md). Then, adapt the exported template for global Azure and re-create the resources.

> [!NOTE]
> Exporting a Resource Manager template doesn't copy the data (for example, messages). Exporting a template only re-creates the metadata.

> [!IMPORTANT]
> Change location, Azure Key Vault secrets, certificates, and other GUIDs to be consistent with the new region.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### Service Bus metadata

The following Service Bus metadata elements are re-created when you export a Resource Manager template:

- Namespaces
- Queues
- Topics
- Subscriptions
- Rules
- Authorization rules

### Keys

The preceding steps to export and re-create don't copy the shared access signature keys that are associated with authorization rules. If you need to preserve the shared access signature keys, use the `New-AzServiceBuskey` cmdlet with the optional parameter `-Keyvalue` to accept the key as a string. The updated cmdlet is available in [Azure PowerShell Az module](/powershell/azure/install-az-ps).

### Usage example

```powershell
New-AzServiceBuskey -ResourceGroupName <resourcegroupname> -Namespace <namespace> -Name <name of Authorization rule> -RegenerateKey <PrimaryKey/SecondaryKey> -KeyValue <string - key value>
```

```powershell
New-AzServiceBuskey -ResourceGroupName <resourcegroupname> -Namespace <namespace> -Queue <queuename> -Name <name of Authorization rule> -RegenerateKey <PrimaryKey/SecondaryKey> -KeyValue <string - key value>
```

```powershell
New-AzServiceBuskey -ResourceGroupName <resourcegroupname> -Namespace <namespace> -Topic <topicname> -Name <name of Authorization rule> -RegenerateKey <PrimaryKey/SecondaryKey> -KeyValue <string - key value>
```

> [!NOTE]
> You must update your applications to use a new connection string even if you preserve the keys. DNS host names are different in Azure Germany and global Azure.

### Sample connection strings

**Azure Germany**

```cmd
Endpoint=sb://myBFProdnamespaceName.**servicebus.cloudapi.de**/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXx=
```

**Global Azure**

```cmd
Endpoint=sb://myProdnamespaceName.**servicebus.windows.net**/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXx=
```

For more information:

- Refresh your knowledge by completing the [Service Bus tutorials](../service-bus-messaging/index.yml).
- Become familiar with how to [export Resource Manager templates](../azure-resource-manager/templates/export-template-portal.md) or read the overview of [Azure Resource Manager](../azure-resource-manager/management/overview.md).
- Review the [Service Bus overview](../service-bus-messaging/service-bus-messaging-overview.md).

## Logic Apps

Azure Logic Apps isn't available in Azure Germany, but you can create scheduling jobs by using Logic Apps in global Azure instead. Although previously available in Azure Germany, Azure Scheduler is being retired.

For more information:

- Learn more by completing the [Azure Logic Apps tutorials](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md).
- Review the [Azure Logic Apps overview](../logic-apps/logic-apps-overview.md).

## Next steps

Learn about tools, techniques, and recommendations for migrating resources in the following service categories:

- [Compute](./germany-migration-compute.md)
- [Networking](./germany-migration-networking.md)
- [Storage](./germany-migration-storage.md)
- [Web](./germany-migration-web.md)
- [Databases](./germany-migration-databases.md)
- [Analytics](./germany-migration-analytics.md)
- [IoT](./germany-migration-iot.md)
- [Identity](./germany-migration-identity.md)
- [Security](./germany-migration-security.md)
- [Management tools](./germany-migration-management-tools.md)
- [Media](./germany-migration-media.md)