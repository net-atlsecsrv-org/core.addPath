---
title: Understand how effects work
description: Azure Policy definitions have various effects that determine how compliance is managed and reported.
ms.date: 11/04/2019
ms.topic: conceptual
---
# Understand Azure Policy effects

Each policy definition in Azure Policy has a single effect. That effect determines what happens when
the policy rule is evaluated to match. The effects behave differently if they are for a new
resource, an updated resource, or an existing resource.

These effects are currently supported in a policy definition:

- [Append](#append)
- [Audit](#audit)
- [AuditIfNotExists](#auditifnotexists)
- [Deny](#deny)
- [DeployIfNotExists](#deployifnotexists)
- [Disabled](#disabled)
- [EnforceOPAConstraint](#enforceopaconstraint) (preview)
- [EnforceRegoPolicy](#enforceregopolicy) (preview)
- [Modify](#modify)

## Order of evaluation

Requests to create or update a resource through Azure Resource Manager are evaluated by Azure Policy
first. Azure Policy creates a list of all assignments that apply to the resource and then evaluates
the resource against each definition. Azure Policy processes several of the effects before handing
the request to the appropriate Resource Provider. Doing so prevents unnecessary processing by a
Resource Provider when a resource doesn't meet the designed governance controls of Azure Policy.

- **Disabled** is checked first to determine if the policy rule should be evaluated.
- **Append** and **Modify** are then evaluated. Since either could alter the request, a change made
  may prevent an audit or deny effect from triggering.
- **Deny** is then evaluated. By evaluating deny before audit, double logging of an undesired
  resource is prevented.
- **Audit** is then evaluated before the request going to the Resource Provider.

After the Resource Provider returns a success code, **AuditIfNotExists** and **DeployIfNotExists**
evaluate to determine if additional compliance logging or action is required.

There currently isn't any order of evaluation for the **EnforceOPAConstraint** or
**EnforceRegoPolicy** effects.

## Disabled

This effect is useful for testing situations or for when the policy definition has parameterized the
effect. This flexibility makes it possible to disable a single assignment instead of disabling all
of that policy's assignments.

An alternative to the Disabled effect is **enforcementMode** which is set on the policy assignment.
When **enforcementMode** is _Disabled_, resources are still evaluated. Logging, such as Activity
logs, and the policy effect don't occur. For more information, see
[policy assignment - enforcement mode](./assignment-structure.md#enforcement-mode).

## Append

Append is used to add additional fields to the requested resource during creation or update. A
common example is specifying allowed IPs for a storage resource.

> [!IMPORTANT]
> Append is intended for use with non-tag properties. While Append can add tags to a resource during
> a create or update request, it's recommended to use the [Modify](#modify) effect for tags instead.

### Append evaluation

Append evaluates before the request gets processed by a Resource Provider during the creation or
updating of a resource. Append adds fields to the resource when the **if** condition of the policy
rule is met. If the append effect would override a value in the original request with a different
value, then it acts as a deny effect and rejects the request. To append a new value to an existing
array, use the **[\*]** version of the alias.

When a policy definition using the append effect is run as part of an evaluation cycle, it doesn't
make changes to resources that already exist. Instead, it marks any resource that meets the **if**
condition as non-compliant.

### Append properties

An append effect only has a **details** array, which is required. As **details** is an array, it can
take either a single **field/value** pair or multiples. Refer to [definition structure](definition-structure.md#fields)
for the list of acceptable fields.

### Append examples

Example 1: Single **field/value** pair using a non-**[\*]** [alias](definition-structure.md#aliases)
with an array **value** to set IP rules on a storage account. When the non-**[\*]** alias is an
array, the effect appends the **value** as the entire array. If the array already exists, a deny
event occurs from the conflict.

```json
"then": {
    "effect": "append",
    "details": [{
        "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules",
        "value": [{
            "action": "Allow",
            "value": "134.5.0.0/21"
        }]
    }]
}
```

Example 2: Single **field/value** pair using an **[\*]** [alias](definition-structure.md#aliases)
with an array **value** to set IP rules on a storage account. By using the **[\*]** alias, the
effect appends the **value** to a potentially pre-existing array. If the array doesn't exist yet, it
will be created.

```json
"then": {
    "effect": "append",
    "details": [{
        "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]",
        "value": {
            "value": "40.40.40.40",
            "action": "Allow"
        }
    }]
}
```

## Modify

Modify is used to add, update, or remove tags on a resource during creation or update. A common
example is updating tags on resources such as costCenter. A Modify policy should always have `mode`
set to _Indexed_ unless the target resource is a resource group. Existing non-compliant resources
can be remediated with a [remediation task](../how-to/remediate-resources.md). A single Modify rule
can have any number of operations.

> [!IMPORTANT]
> Modify is currently only for use with tags. If you are managing tags, it's recommended to use
> Modify instead of Append as Modify provides additional operation types and the ability to
> remediate existing resources. However, Append is recommended if you aren't able to create a
> managed identity.

### Modify evaluation

Modify evaluates before the request gets processed by a Resource Provider during the creation or
updating of a resource. Modify adds or updates tags on a resource when the **if** condition of the
policy rule is met.

When a policy definition using the Modify effect is run as part of an evaluation cycle, it doesn't
make changes to resources that already exist. Instead, it marks any resource that meets the **if**
condition as non-compliant.

### Modify properties

The **details** property of the Modify effect has all the subproperties that define the permissions
needed for remediation and the **operations** used to add, update, or remove tag values.

- **roleDefinitionIds** [required]
  - This property must include an array of strings that match role-based access control role ID
    accessible by the subscription. For more information, see
    [remediation - configure policy definition](../how-to/remediate-resources.md#configure-policy-definition).
  - The role defined must include all operations granted to the [Contributor](../../../role-based-access-control/built-in-roles.md#contributor)
    role.
- **operations** [required]
  - An array of all tag operations to be completed on matching resources.
  - Properties:
    - **operation** [required]
      - Defines what action to take on a matching resource. Options are: _addOrReplace_, _Add_,
        _Remove_. _Add_ behaves similar to the [Append](#append) effect.
    - **field** [required]
      - The tag to add, replace, or remove. Tag names must adhere to the same naming convention for
        other [fields](./definition-structure.md#fields).
    - **value** (optional)
      - The value to set the tag to.
      - This property is required if **operation** is _addOrReplace_ or _Add_.

### Modify operations

The **operations** property array makes it possible to alter several tags in different ways from a
single policy definition. Each operation is made up of **operation**, **field**, and **value**
properties. Operation determines what the remediation task does to the tags, field determines which
tag is altered, and value defines the new setting for that tag. The example below makes the
following tag changes:

- Sets the `environment` tag to "Test", even if it already exists with a different value.
- Removes the tag `TempResource`.
- Sets the `Dept` tag to the policy parameter _DeptName_ configured on the policy assignment.

```json
"details": {
    ...
    "operations": [
        {
            "operation": "addOrReplace",
            "field": "tags['environment']",
            "value": "Test"
        },
        {
            "operation": "Remove",
            "field": "tags['TempResource']",
        },
        {
            "operation": "addOrReplace",
            "field": "tags['Dept']",
            "value": "[parameters('DeptName')]"
        }
    ]
}
```

The **operation** property has the following options:

|Operation |Description |
|-|-|
|addOrReplace |Adds the defined tag and value to the resource, even if the tag already exists with a different value. |
|Add |Adds the defined tag and value to the resource. |
|Remove |Removes the defined tag from the resource. |

### Modify examples

Example 1: Add the `environment` tag and replace existing `environment` tags with "Test":

```json
"then": {
    "effect": "modify",
    "details": {
        "roleDefinitionIds": [
            "/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
        ],
        "operations": [
            {
                "operation": "addOrReplace",
                "field": "tags['environment']",
                "value": "Test"
            }
        ]
    }
}
```

Example 2: Remove the `env` tag and add the `environment` tag or replace existing
`environment` tags with a parameterized value:

```json
"then": {
    "effect": "modify",
    "details": {
        "roleDefinitionIds": [
            "/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
        ],
        "operations": [
            {
                "operation": "Remove",
                "field": "tags['env']"
            },
            {
                "operation": "addOrReplace",
                "field": "tags['environment']",
                "value": "[parameters('tagValue')]"
            }
        ]
    }
}
```

## Deny

Deny is used to prevent a resource request that doesn't match defined standards through a policy
definition and fails the request.

### Deny evaluation

When creating or updating a matched resource, deny prevents the request before being sent to the
Resource Provider. The request is returned as a `403 (Forbidden)`. In the portal, the Forbidden can
be viewed as a status on the deployment that was prevented by the policy assignment.

During evaluation of existing resources, resources that match a deny policy definition are marked as
non-compliant.

### Deny properties

The deny effect doesn't have any additional properties for use in the **then** condition of the
policy definition.

### Deny example

Example: Using the deny effect.

```json
"then": {
    "effect": "deny"
}
```

## Audit

Audit is used to create a warning event in the activity log when evaluating a non-compliant
resource, but it doesn't stop the request.

### Audit evaluation

Audit is the last effect checked by Azure Policy during the creation or update of a resource. Azure
Policy then sends the resource to the Resource Provider. Audit works the same for a resource request
and an evaluation cycle. Azure Policy adds a `Microsoft.Authorization/policies/audit/action`
operation to the activity log and marks the resource as non-compliant.

### Audit properties

The audit effect doesn't have any additional properties for use in the **then** condition of the
policy definition.

### Audit example

Example: Using the audit effect.

```json
"then": {
    "effect": "audit"
}
```

## AuditIfNotExists

AuditIfNotExists enables auditing on resources that match the **if** condition, but doesn't have
the components specified in the **details** of the **then** condition.

### AuditIfNotExists evaluation

AuditIfNotExists runs after a Resource Provider has handled a create or update resource request and
has returned a success status code. The audit occurs if there are no related resources or if the
resources defined by **ExistenceCondition** don't evaluate to true. Azure Policy adds a
`Microsoft.Authorization/policies/audit/action` operation to the activity log the same way as the
audit effect. When triggered, the resource that satisfied the **if** condition is the resource that
is marked as non-compliant.

### AuditIfNotExists properties

The **details** property of the AuditIfNotExists effects has all the subproperties that define the
related resources to match.

- **Type** [required]
  - Specifies the type of the related resource to match.
  - If **details.type** is a resource type underneath the **if** condition resource, the policy
    queries for resources of this **type** within the scope of the evaluated resource. Otherwise,
    policy queries within the same resource group as the evaluated resource.
- **Name** (optional)
  - Specifies the exact name of the resource to match and causes the policy to fetch one specific
    resource instead of all resources of the specified type.
  - When the condition values for **if.field.type** and **then.details.type** match, then **Name**
    becomes _required_ and must be `[field('name')]`. However, an [audit](#audit) effect should be
    considered instead.
- **ResourceGroupName** (optional)
  - Allows the matching of the related resource to come from a different resource group.
  - Doesn't apply if **type** is a resource that would be underneath the **if** condition resource.
  - Default is the **if** condition resource's resource group.
- **ExistenceScope** (optional)
  - Allowed values are _Subscription_ and _ResourceGroup_.
  - Sets the scope of where to fetch the related resource to match from.
  - Doesn't apply if **type** is a resource that would be underneath the **if** condition resource.
  - For _ResourceGroup_, would limit to the **if** condition resource's resource group or the
    resource group specified in **ResourceGroupName**.
  - For _Subscription_, queries the entire subscription for the related resource.
  - Default is _ResourceGroup_.
- **ExistenceCondition** (optional)
  - If not specified, any related resource of **type** satisfies the effect and doesn't trigger the
    audit.
  - Uses the same language as the policy rule for the **if** condition, but is evaluated against
    each related resource individually.
  - If any matching related resource evaluates to true, the effect is satisfied and doesn't trigger
    the audit.
  - Can use [field()] to check equivalence with values in the **if** condition.
  - For example, could be used to validate that the parent resource (in the **if** condition) is in
    the same resource location as the matching related resource.

### AuditIfNotExists example

Example: Evaluates Virtual Machines to determine if the Antimalware extension exists then audits
when missing.

```json
{
    "if": {
        "field": "type",
        "equals": "Microsoft.Compute/virtualMachines"
    },
    "then": {
        "effect": "auditIfNotExists",
        "details": {
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "existenceCondition": {
                "allOf": [{
                        "field": "Microsoft.Compute/virtualMachines/extensions/publisher",
                        "equals": "Microsoft.Azure.Security"
                    },
                    {
                        "field": "Microsoft.Compute/virtualMachines/extensions/type",
                        "equals": "IaaSAntimalware"
                    }
                ]
            }
        }
    }
}
```

## DeployIfNotExists

Similar to AuditIfNotExists, a DeployIfNotExists policy definition executes a template deployment
when the condition is met.

> [!NOTE]
> [Nested templates](../../../azure-resource-manager/templates/linked-templates.md#nested-template) are supported with **deployIfNotExists**, but
> [linked templates](../../../azure-resource-manager/templates/linked-templates.md#linked-template) are currently not supported.

### DeployIfNotExists evaluation

DeployIfNotExists runs about 15 minutes after a Resource Provider has handled a create or update
resource request and has returned a success status code. A template deployment occurs if there are
no related resources or if the resources defined by **ExistenceCondition** don't evaluate to true.
The duration of the deployment depends on the complexity of resources included in the template.

During an evaluation cycle, policy definitions with a DeployIfNotExists effect that match resources
are marked as non-compliant, but no action is taken on that resource.

### DeployIfNotExists properties

The **details** property of the DeployIfNotExists effect has all the subproperties that define the
related resources to match and the template deployment to execute.

- **Type** [required]
  - Specifies the type of the related resource to match.
  - Starts by trying to fetch a resource underneath the **if** condition resource, then queries
    within the same resource group as the **if** condition resource.
- **Name** (optional)
  - Specifies the exact name of the resource to match and causes the policy to fetch one specific
    resource instead of all resources of the specified type.
  - When the condition values for **if.field.type** and **then.details.type** match, then **Name**
    becomes _required_ and must be `[field('name')]`.
- **ResourceGroupName** (optional)
  - Allows the matching of the related resource to come from a different resource group.
  - Doesn't apply if **type** is a resource that would be underneath the **if** condition resource.
  - Default is the **if** condition resource's resource group.
  - If a template deployment is executed, it's deployed in the resource group of this value.
- **ExistenceScope** (optional)
  - Allowed values are _Subscription_ and _ResourceGroup_.
  - Sets the scope of where to fetch the related resource to match from.
  - Doesn't apply if **type** is a resource that would be underneath the **if** condition resource.
  - For _ResourceGroup_, would limit to the **if** condition resource's resource group or the
    resource group specified in **ResourceGroupName**.
  - For _Subscription_, queries the entire subscription for the related resource.
  - Default is _ResourceGroup_.
- **ExistenceCondition** (optional)
  - If not specified, any related resource of **type** satisfies the effect and doesn't trigger the
    deployment.
  - Uses the same language as the policy rule for the **if** condition, but is evaluated against
    each related resource individually.
  - If any matching related resource evaluates to true, the effect is satisfied and doesn't trigger
    the deployment.
  - Can use [field()] to check equivalence with values in the **if** condition.
  - For example, could be used to validate that the parent resource (in the **if** condition) is in
    the same resource location as the matching related resource.
- **roleDefinitionIds** [required]
  - This property must include an array of strings that match role-based access control role ID
    accessible by the subscription. For more information, see
    [remediation - configure policy definition](../how-to/remediate-resources.md#configure-policy-definition).
- **DeploymentScope** (optional)
  - Allowed values are _Subscription_ and _ResourceGroup_.
  - Sets the type of deployment to be triggered. _Subscription_ indicates a [deployment at subscription level](../../../azure-resource-manager/templates/deploy-to-subscription.md),
    _ResourceGroup_ indicates a deployment to a resource group.
  - A _location_ property must be specified in the _Deployment_ when using subscription level
    deployments.
  - Default is _ResourceGroup_.
- **Deployment** [required]
  - This property should include the full template deployment as it would be passed to the
    `Microsoft.Resources/deployments` PUT API. For more information, see the [Deployments REST API](/rest/api/resources/deployments).

  > [!NOTE]
  > All functions inside the **Deployment** property are evaluated as components of the template,
  > not the policy. The exception is the **parameters** property that passes values from the policy
  > to the template. The **value** in this section under a template parameter name is used to
  > perform this value passing (see _fullDbName_ in the DeployIfNotExists example).

### DeployIfNotExists example

Example: Evaluates SQL Server databases to determine if transparentDataEncryption is enabled. If
not, then a deployment to enable is executed.

```json
"if": {
    "field": "type",
    "equals": "Microsoft.Sql/servers/databases"
},
"then": {
    "effect": "DeployIfNotExists",
    "details": {
        "type": "Microsoft.Sql/servers/databases/transparentDataEncryption",
        "name": "current",
        "roleDefinitionIds": [
            "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/{roleGUID}",
            "/providers/Microsoft.Authorization/roleDefinitions/{builtinroleGUID}"
        ],
        "existenceCondition": {
            "field": "Microsoft.Sql/transparentDataEncryption.status",
            "equals": "Enabled"
        },
        "deployment": {
            "properties": {
                "mode": "incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {
                        "fullDbName": {
                            "type": "string"
                        }
                    },
                    "resources": [{
                        "name": "[concat(parameters('fullDbName'), '/current')]",
                        "type": "Microsoft.Sql/servers/databases/transparentDataEncryption",
                        "apiVersion": "2014-04-01",
                        "properties": {
                            "status": "Enabled"
                        }
                    }]
                },
                "parameters": {
                    "fullDbName": {
                        "value": "[field('fullName')]"
                    }
                }
            }
        }
    }
}
```

## EnforceOPAConstraint

This effect is used with a policy definition *mode* of `Microsoft.Kubernetes.Data`. It's used to
pass Gatekeeper v3 admission control rules defined with
[OPA Constraint Framework](https://github.com/open-policy-agent/frameworks/tree/master/constraint#opa-constraint-framework)
to [Open Policy Agent](https://www.openpolicyagent.org/) (OPA) to self-managed Kubernetes clusters
on Azure.

> [!NOTE]
> [Azure Policy for AKS Engine](aks-engine.md) is in Public Preview and only supports built-in
> policy definitions.

### EnforceOPAConstraint evaluation

The Open Policy Agent admission controller evaluates any new request on the cluster in real time.
Every 5 minutes, a full scan of the cluster is completed and the results reported to Azure Policy.

### EnforceOPAConstraint properties

The **details** property of the EnforceOPAConstraint effect has the subproperties that describe the
Gatekeeper v3 admission control rule.

- **constraintTemplate** [required]
  - The Constraint template CustomResourceDefinition (CRD) that defines new Constraints. The
    template defines the Rego logic, the Constraint schema, and the Constraint parameters which are
    passed via **values** from Azure Policy.
- **constraint** [required]
  - The CRD implementation of the Constraint template. Uses parameters passed via **values** as
    `{{ .Values.<valuename> }}`. In the example below, this would be `{{ .Values.cpuLimit }}` and
    `{{ .Values.memoryLimit }}`.
- **values** [optional]
  - Defines any parameters and values to pass to the Constraint. Each value must exist in the
    Constraint template CRD.

### EnforceRegoPolicy example

Example: Gatekeeper v3 admission control rule to set container CPU and memory resource limits in AKS
Engine.

```json
"if": {
    "allOf": [
        {
            "field": "type",
            "in": [
                "Microsoft.ContainerService/managedClusters",
                "AKS Engine"
            ]
        },
        {
            "field": "location",
            "equals": "westus2"
        }
    ]
},
"then": {
    "effect": "enforceOPAConstraint",
    "details": {
        "constraintTemplate": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-resource-limits/template.yaml",
        "constraint": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-resource-limits/constraint.yaml",
        "values": {
            "cpuLimit": "[parameters('cpuLimit')]",
            "memoryLimit": "[parameters('memoryLimit')]"
        }
    }
}
```

## EnforceRegoPolicy

This effect is used with a policy definition *mode* of `Microsoft.ContainerService.Data`. It's used
to pass Gatekeeper v2 admission control rules defined with
[Rego](https://www.openpolicyagent.org/docs/latest/policy-language/#what-is-rego) to
[Open Policy Agent](https://www.openpolicyagent.org/) (OPA) on
[Azure Kubernetes Service](../../../aks/intro-kubernetes.md).

> [!NOTE]
> [Azure Policy for AKS](rego-for-aks.md) is in Limited Preview and only supports built-in policy
> definitions

### EnforceRegoPolicy evaluation

The Open Policy Agent admission controller evaluates any new request on the cluster in real time.
Every 5 minutes, a full scan of the cluster is completed and the results reported to Azure Policy.

### EnforceRegoPolicy properties

The **details** property of the EnforceRegoPolicy effect has the subproperties that describe the
Gatekeeper v2 admission control rule.

- **policyId** [required]
  - A unique name passed as a parameter to the Rego admission control rule.
- **policy** [required]
  - Specifies the URI of the Rego admission control rule.
- **policyParameters** [optional]
  - Defines any parameters and values to pass to the rego policy.

### EnforceRegoPolicy example

Example: Gatekeeper v2 admission control rule to allow only the specified container images in AKS.

```json
"if": {
    "allOf": [
        {
            "field": "type",
            "equals": "Microsoft.ContainerService/managedClusters"
        },
        {
            "field": "location",
            "equals": "westus2"
        }
    ]
},
"then": {
    "effect": "EnforceRegoPolicy",
    "details": {
        "policyId": "ContainerAllowedImages",
        "policy": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/KubernetesService/container-allowed-images/limited-preview/gatekeeperpolicy.rego",
        "policyParameters": {
            "allowedContainerImagesRegex": "[parameters('allowedContainerImagesRegex')]"
        }
    }
}
```

## Layering policies

A resource may be impacted by several assignments. These assignments may be at the same scope or at
different scopes. Each of these assignments is also likely to have a different effect defined. The
condition and effect for each policy is independently evaluated. For example:

- Policy 1
  - Restricts resource location to 'westus'
  - Assigned to subscription A
  - Deny effect
- Policy 2
  - Restricts resource location to 'eastus'
  - Assigned to resource group B in subscription A
  - Audit effect
  
This setup would result in the following outcome:

- Any resource already in resource group B in 'eastus' is compliant to policy 2 and non-compliant to
  policy 1
- Any resource already in resource group B not in 'eastus' is non-compliant to policy 2 and
  non-compliant to policy 1 if not in 'westus'
- Any new resource in subscription A not in 'westus' is denied by policy 1
- Any new resource in subscription A and resource group B in 'westus' is created and non-compliant
  on policy 2

If both policy 1 and policy 2 had effect of deny, the situation changes to:

- Any resource already in resource group B not in 'eastus' is non-compliant to policy 2
- Any resource already in resource group B not in 'westus' is non-compliant to policy 1
- Any new resource in subscription A not in 'westus' is denied by policy 1
- Any new resource in resource group B of subscription A is denied

Each assignment is individually evaluated. As such, there isn't an opportunity for a resource to
slip through a gap from differences in scope. The net result of layering policies or policy overlap
is considered to be **cumulative most restrictive**. As an example, if both policy 1 and 2 had a
deny effect, a resource would be blocked by the overlapping and conflicting policies. If you still
need the resource to be created in the target scope, review the exclusions on each assignment to
validate the right policies are affecting the right scopes.

## Next steps

- Review examples at [Azure Policy samples](../samples/index.md).
- Review the [Azure Policy definition structure](definition-structure.md).
- Understand how to [programmatically create policies](../how-to/programmatically-create.md).
- Learn how to [get compliance data](../how-to/get-compliance-data.md).
- Learn how to [remediate non-compliant resources](../how-to/remediate-resources.md).
- Review what a management group is with [Organize your resources with Azure management groups](../../management-groups/overview.md).
