---
title: Stages of a blueprint deployment
description: Learn the security and artifact related steps the Azure Blueprints services goes through while creating a blueprint assignment.
ms.date: 08/27/2020
ms.topic: conceptual
---
# Stages of a blueprint deployment

When a blueprint gets deployed, a series of actions is taken by the Azure Blueprints service to
deploy the resources defined in the blueprint. This article provides details about what each step
involves.

Blueprint deployment is triggered by assigning a blueprint to a subscription or [updating an
existing assignment](../how-to/update-existing-assignments.md). During the deployment, Azure
Blueprints takes the following high-level steps:

> [!div class="checklist"]
> - Azure Blueprints granted owner rights
> - The blueprint assignment object is created
> - Optional - Azure Blueprints creates **system-assigned** managed identity
> - The managed identity deploys blueprint artifacts
> - Azure Blueprints service and **system-assigned** managed identity rights are revoked

## Azure Blueprints granted owner rights

The Azure Blueprints service principal is granted owner rights to the assigned subscription or
subscriptions when a [system-assigned managed
identity](../../../active-directory/managed-identities-azure-resources/overview.md) managed identity
is used. The granted role allows Azure Blueprints to create, and later revoke, the
**system-assigned** managed identity. If using a **user-assigned** managed identity, the Azure
Blueprints service principal doesn't get and doesn't need owner rights on the subscription.

The rights are granted automatically if the assignment is done through the portal. However, if the
assignment is done through the REST API, granting the rights needs to be done with a separate API
call. The Azure Blueprints AppId is `f71766dc-90d9-4b7d-bd9d-4499c4331c3f`, but the service
principal varies by tenant. Use
[Azure Active Directory Graph API](../../../active-directory/develop/active-directory-graph-api.md)
and REST endpoint [servicePrincipals](/graph/api/resources/serviceprincipal) to get the service
principal. Then, grant the Azure Blueprints the _Owner_ role through the
[Portal](../../../role-based-access-control/role-assignments-portal.md),
[Azure CLI](../../../role-based-access-control/role-assignments-cli.md),
[Azure PowerShell](../../../role-based-access-control/role-assignments-powershell.md),
[REST API](../../../role-based-access-control/role-assignments-rest.md), or an
[Azure Resource Manager template](../../../role-based-access-control/role-assignments-template.md).

The Azure Blueprints service doesn't directly deploy the resources.

## The blueprint assignment object is created

A user, group, or service principal assigns a blueprint to a subscription. The assignment object
exists at the subscription level where the blueprint was assigned. Resources created by the
deployment aren't done in context of the deploying entity.

While creating the blueprint assignment, the type of [managed
identity](../../../active-directory/managed-identities-azure-resources/overview.md) is selected. The
default is a **system-assigned** managed identity. A **user-assigned** managed identity can be
chosen. When using a **user-assigned** managed identity, it must be defined and granted permissions
before the blueprint assignment is created. Both the
[Owner](../../../role-based-access-control/built-in-roles.md#owner) and
[Blueprint Operator](../../../role-based-access-control/built-in-roles.md#blueprint-operator)
built-in roles have the necessary `blueprintAssignment/write` permission to create an assignment
that uses a **user-assigned** managed identity.

## Optional - Azure Blueprints creates system-assigned managed identity

When [system-assigned managed
identity](../../../active-directory/managed-identities-azure-resources/overview.md) is selected
during assignment, Azure Blueprints creates the identity and grants the managed identity the
[owner](../../../role-based-access-control/built-in-roles.md#owner) role. If an
[existing assignment is upgraded](../how-to/update-existing-assignments.md), Azure Blueprints uses
the previously created managed identity.

The managed identity related to the blueprint assignment is used to deploy or redeploy the resources
defined in the blueprint. This design avoids assignments inadvertently interfering with each other.
This design also supports the [resource locking](./resource-locking.md) feature by controlling the
security of each deployed resource from the blueprint.

## The managed identity deploys blueprint artifacts

The managed identity then triggers the Resource Manager deployments of the artifacts within the
blueprint in the defined [sequencing order](./sequencing-order.md). The order can be adjusted to
ensure artifacts dependent on other artifacts are deployed in the correct order.

An access failure by a deployment is often the result of the level of access granted to the managed
identity. The Azure Blueprints service manages the security lifecycle of the **system-assigned**
managed identity. However, the user is responsible for managing the rights and lifecycle of a
**user-assigned** managed identity.

## Blueprint service and system-assigned managed identity rights are revoked

Once the deployments are completed, Azure Blueprints revokes the rights of the **system-assigned**
managed identity from the subscription. Then, the Azure Blueprints service revokes its rights from
the subscription. Rights removal prevents Azure Blueprints from becoming a permanent owner on a
subscription.

## Next steps

- Understand how to use [static and dynamic parameters](./parameters.md).
- Learn to customize the [blueprint sequencing order](./sequencing-order.md).
- Find out how to make use of [blueprint resource locking](./resource-locking.md).
- Learn how to [update existing assignments](../how-to/update-existing-assignments.md).
- Resolve issues during the assignment of a blueprint with
  [general troubleshooting](../troubleshoot/general.md).
