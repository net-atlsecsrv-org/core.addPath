---
title: Managed HSM data plane role management - Azure Key Vault | Microsoft Docs
description: Use this article to manage role assignments for your managed HSM 
services: key-vault
author: amitbapat

ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: ambapat

---
# Managed HSM role management

> [!NOTE]
> Key Vault supports two types of resource: vaults and managed HSMs. This article is about **Managed HSM**. If you want to learn how to manage a vault, please see [Manage Key Vault using the Azure CLI](../general/manage-with-cli2.md).

For an overview of Managed HSM, see [What is Managed HSM?](overview.md). If you don't have an Azure subscription, create a [free account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) before you begin.

This article show you how to manage roles for a Managed HSM data plane. To learn about Managed HSM access control model, see [Managed HSM access control](access-control.md).

To allow a security principal (such as a user, a service principal, group or a managed identity) to perform managed HSM data plane operations, they must be assigned a role that permits performing those operations. For example, if you want to allow an application to perform a sign operation using a key, it must be assigned a role that contains the "Microsoft.KeyVault/managedHSM/keys/sign/action" as one of the data actions. A role can be assigned at a specific scope. Managed HSM local RBAC supports two scopes, HSM-wide (`/` or `/keys`) and per key (`/keys/<keyname>`).

For a list of all Managed HSM built-in roles and the operations they permit, see [Managed HSM built-in roles](built-in-roles.md).

## Prerequisites

To use the Azure CLI commands in this article, you must have the following items:

* A subscription to Microsoft Azure. If you don't have one, you can sign up for a [free trial](https://azure.microsoft.com/pricing/free-trial).
* The Azure CLI version 2.12.0 or later. Run `az --version` to find the version. If you need to install or upgrade, see [Install the Azure CLI]( /cli/azure/install-azure-cli).
* A managed HSM in your subscription. See [Quickstart: Provision and activate a managed HSM using Azure CLI](quick-create-cli.md) to provision and activate a managed HSM.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## Sign in to Azure

To sign in to Azure using the CLI you can type:

```azurecli
az login
```

For more information on login options via the CLI, see [sign in with Azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest&preserve-view=true)

## Create a new role assignment

### Assign roles for all keys

Use `az keyvault role assignment create` command to assign a **Managed HSM Crypto Officer** role to user identified by user principal name **user2\@contoso.com** for all  **keys** (scope `/keys`) in the ContosoHSM.

```azurecli-interactive
az keyvault role assignment create --hsm-name ContosoMHSM --role "Managed HSM Crypto Officer" --assignee user2@contoso.com  --scope /keys
```

### Assign role for a specific key

Use `az keyvault role assignment create` command to assign a **Managed HSM Crypto Officer** role to user identified by user principal name **user2\@contoso.com** for a specific key named **myrsakey**.

```azurecli-interactive
az keyvault role assignment create --hsm-name ContosoMHSM --role "Managed HSM Crypto Officer" --assignee user2@contoso.com  --scope /keys/myrsakey
```

## List existing role assignments

Use `az keyvault role assignment list` to list role assignments.

All role assignments at scope / (default when no --scope is specified) for all users (default when no --assignee is specified)

```azurecli-interactive
az keyvault role assignment list --hsm-name ContosoMHSM
```

All the role assignments at the HSM level for a specific user **user1@contoso.com**.

```azurecli-interactive
az keyvault role assignment list --hsm-name ContosoMHSM --assignee user@contoso.com
```

All role assignments for a specific user **user2@contoso.com** for a specific key **myrsakey**.

```azurecli-interactive
az keyvault role assignment list --hsm-name ContosoMHSM --assignee user2@contoso.com --scope /keys/myrsakey
```

A specific role assignment for role **Managed HSM Crypto Officer** for a specific user **user2@contoso.com** for a specific key **myrsakey**


```azurecli-interactive
az keyvault role assignment list --hsm-name ContosoMHSM --assignee user2@contoso.com --scope /keys/myrsakey --role "Managed HSM Crypto Officer"
```

## Delete a role assignment

Use `az keyvault role assignment delete` command to delete a **Managed HSM Crypto Officer** role assigned to user **user2\@contoso.com** for key **myrsakey2**.

```azurecli-interactive
az keyvault role assignment delete --hsm-name ContosoMHSM --role "Managed HSM Crypto Officer" --assignee user2@contoso.com  --scope /keys/myrsakey2
```

## List all available role definitions

Use `az keyvault role definition list` command to list all the role definitions.

```azurecli-interactive
az keyvault role definition list --hsm-name ContosoMHSM
```

## Next steps

- See an overview of [Azure role-based access control (Azure RBAC)](../../role-based-access-control/overview.md).
- See a tutorial on [Managed HSM role management](role-management.md)
- Learn more about [Managed HSM access control model](access-control.md)
- See all the [built-in roles for Managed HSM local RBAC](built-in-roles.md)
