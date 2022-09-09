---
title: Known issues with managed identities - Azure Active Directory
description: Known issues with managed identities for Azure resources.
services: active-directory
documentationcenter: 
author: barclayn
manager: daveba
editor: 
ms.assetid: 2097381a-a7ec-4e3b-b4ff-5d2fb17403b6
ms.service: active-directory
ms.subservice: msi
ms.devlang: 
ms.topic: conceptual
ms.tgt_pltfrm: 
ms.workload: identity
ms.date: 04/08/2021
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
---

# Known issues with Managed Identities

This article discusses a couple of issues around managed identities and how to address them. Common questions about managed identities are documented in our [frequently asked questions](managed-identities-faq.md) article.
## VM fails to start after being moved 

If you move a VM in a running state from a resource group or subscription, it continues to run during the move. However, after the move, if the VM is stopped and restarted, it will fail to start. This issue happens because the VM is not updating the reference to the managed identities for Azure resources identity and continues to point to it in the old resource group.

**Workaround** 
 
Trigger an update on the VM so it can get correct values for the managed identities for Azure resources. You can do a VM property change to update the reference to the managed identities for Azure resources identity. For example, you can set a new tag value on the VM with the following command:

```azurecli-interactive
az vm update -n <VM Name> -g <Resource Group> --set tags.fixVM=1
```
 
This command sets a new tag "fixVM" with a value of 1 on the VM. 
 
By setting this property, the VM updates with the correct managed identities for Azure resources resource URI, and then you should be able to start the VM. 
 
Once the VM is started, the tag can be removed by using following command:

```azurecli-interactive
az vm update -n <VM Name> -g <Resource Group> --remove tags.fixVM
```

## Transferring a subscription between Azure AD directories

Managed identities do not get updated when a subscription is moved/transferred to another directory. As a result, any existent system-assigned or user-assigned managed identities will be broken. 

Workaround for managed identities in a subscription that has been moved to another directory:

 - For system assigned managed identities: disable and re-enable. 
 - For user assigned managed identities: delete, re-create, and attach them again to the necessary resources (for example, virtual machines)

For more information, see [Transfer an Azure subscription to a different Azure AD directory](../../role-based-access-control/transfer-subscription.md).


## Next steps

You can review our article listing the [services that support managed identities](services-support-managed-identities.md) and our [frequently asked questions](managed-identities-faq.md)
