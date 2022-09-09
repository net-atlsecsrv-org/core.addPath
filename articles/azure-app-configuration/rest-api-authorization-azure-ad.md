---
title: Azure App Configuration REST API - Azure Active Directory authorization
description: Use Azure Active Directory for authorization against Azure App Configuration by using the REST API
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
---

# Azure Active Directory authorization - REST API reference

When you use Azure Active Directory (Azure AD) authentication, authorization is handled by role-based access control (RBAC). RBAC requires users to be assigned to roles in order to grant access to resources. Each role contains a set of actions that users assigned to the role are able to perform.

## Roles

The following roles are available in Azure subscriptions by default:

- **Azure App Configuration Data Owner**: This role provides full access to all operations.
- **Azure App Configuration Data Reader**: This role enables read operations.

## Actions

Roles contain a list of actions that users assigned to that role can perform. Azure App Configuration supports the following actions:

- `Microsoft.AppConfiguration/configurationStores/keyValues/read`: This action allows read access to App Configuration key-value resources, such as /kv and /labels.
- `Microsoft.AppConfiguration/configurationStores/keyValues/write`: This action allows write access to App Configuration key-value resources.
- `Microsoft.AppConfiguration/configurationStores/keyValues/delete`: This action allows App Configuration key-value resources to be deleted. Note that deleting a resource returns the key-value that was deleted.

## Error

```http
HTTP/1.1 403 Forbidden
```

**Reason:** The principal making the request doesn't have the required permissions to perform the requested operation.
**Solution:** Assign the role required to perform the requested operation to the principal making the request.

## Managing role assignments

You can manage role assignments by using [RBAC procedures](https://docs.microsoft.com/azure/role-based-access-control/overview) that are standard across all Azure services. You can do this through the Azure CLI, PowerShell, and the Azure portal. For more information, see [Add or remove Azure role assignments by using the Azure portal](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).
