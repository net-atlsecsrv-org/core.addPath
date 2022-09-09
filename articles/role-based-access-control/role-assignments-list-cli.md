---
title: List role assignments using Azure RBAC and Azure CLI
description: Learn how to determine what resources users, groups, service principals, or managed identities have access to using Azure role-based access control (RBAC) and Azure CLI.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman

ms.assetid: 3483ee01-8177-49e7-b337-4d5cb14f5e32
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/02/2019
ms.author: rolyon
ms.reviewer: bagovind
---
# List role assignments using Azure RBAC and Azure CLI

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)] This article describes how to list role assignments using Azure CLI.

## Prerequisites

- [Bash in Azure Cloud Shell](/azure/cloud-shell/overview) or [Azure CLI](/cli/azure)

## List role assignments for a user

To list the role assignments for a specific user, use [az role assignment list](/cli/azure/role/assignment#az-role-assignment-list):

```azurecli
az role assignment list --assignee <assignee>
```

By default, only role assignments for the current subscription will be displayed. To view role assignments for the current subscription and below, add the `--all` parameter. To view inherited role assignments, add the `--include-inherited` parameter.

The following example lists the role assignments that are assigned directly to the *patlong\@contoso.com* user:

```azurecli
az role assignment list --all --assignee patlong@contoso.com --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

```Output
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Backup Operator",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Virtual Machine Contributor",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}
```

## List role assignments for a resource group

To list the role assignments that exist at a resource group scope, use [az role assignment list](/cli/azure/role/assignment#az-role-assignment-list):

```azurecli
az role assignment list --resource-group <resource_group>
```

The following example lists the role assignments for the *pharma-sales* resource group:

```azurecli
az role assignment list --resource-group pharma-sales --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

```Output
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Backup Operator",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Virtual Machine Contributor",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}

...
```

## List role assignments for a subscription

To list all role assignments at a subscription scope, use [az role assignment list](/cli/azure/role/assignment#az-role-assignment-list). To get the subscription ID, you can find it on the **Subscriptions** blade in the Azure portal or you can use [az account list](/cli/azure/account#az-account-list).

```azurecli
az role assignment list --subscription <subscription_name_or_id>
```

```Example
az role assignment list --subscription 00000000-0000-0000-0000-000000000000 --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

## List role assignments for a management group

To list all role assignments at a management group scope, use [az role assignment list](/cli/azure/role/assignment#az-role-assignment-list). To get the management group ID, you can find it on the **Management groups** blade in the Azure portal or you can use [az account management-group list](/cli/azure/ext/managementgroups/account/management-group#ext-managementgroups-az-account-management-group-list).

```azurecli
az role assignment list --scope /providers/Microsoft.Management/managementGroups/<group_id>
```

```Example
az role assignment list --scope /providers/Microsoft.Management/managementGroups/marketing-group --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

## List role assignments for a managed identity

1. Get the the object ID of the system-assigned or user-assigned managed identity. 

    To get the object ID of a user-assigned managed identity, you can use [az ad sp list](/cli/azure/ad/sp#az-ad-sp-list) or [az identity list](/cli/azure/identity#az-identity-list).

    ```azurecli
    az ad sp list --display-name "<name>" --query [].objectId --output tsv
    ```

    To get the object ID of a system-assigned managed identity, you can use [az ad sp list](/cli/azure/ad/sp#az-ad-sp-list).

    ```azurecli
    az ad sp list --display-name "<vmname>" --query [].objectId --output tsv
    ```

1. To list the role assignments, use [az role assignment list](/cli/azure/role/assignment#az-role-assignment-list).

    By default, only role assignments for the current subscription will be displayed. To view role assignments for the current subscription and below, add the `--all` parameter. To view inherited role assignments, add the `--include-inherited` parameter.

    ```azurecli
    az role assignment list --assignee <objectid>
    ```

## Next steps

- [Add or remove role assignments using Azure RBAC and Azure CLI](role-assignments-cli.md)
