---
title: Deploy resources to subscription
description: Describes how to create a resource group in an Azure Resource Manager template. It also shows how to deploy resources at the Azure subscription scope.
ms.topic: conceptual
ms.date: 03/09/2020
---

# Create resource groups and resources at the subscription level

To simplify the management of resources in your Azure subscription, you can define and assign [policies](../../governance/policy/overview.md) or [role-based access controls](../../role-based-access-control/overview.md) across the subscription. With subscription level templates, you declaratively apply policies and assign roles at the subscription. You can also create resource groups and deploy resources.

To deploy templates at the subscription level, use Azure CLI, PowerShell, or REST API. The Azure portal doesn't support deployment in the subscription level.

## Supported resources

You can deploy the following resource types at the subscription level:

* [budgets](/azure/templates/microsoft.consumption/budgets)
* [deployments](/azure/templates/microsoft.resources/deployments) - for nested templates that deploy to resource groups.
* [peerAsns](/azure/templates/microsoft.peering/peerasns)
* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [resourceGroups](/azure/templates/microsoft.resources/resourcegroups)
* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)

### Schema

The schema you use for subscription-level deployments is different than the schema for resource group deployments.

For templates, use:

```json
https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#
```

The schema for a parameter file is the same for all deployment scopes. For parameter files, use:

```json
https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#
```

## Deployment commands

The commands for subscription-level deployments are different than the commands for resource group deployments.

For the Azure CLI, use [az deployment create](/cli/azure/deployment?view=azure-cli-latest#az-deployment-create). The following example deploys a template to create a resource group:

```azurecli-interactive
az deployment create \
  --name demoDeployment \
  --location centralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json \
  --parameters rgName=demoResourceGroup rgLocation=centralus
```


For the PowerShell deployment command, use [New-AzDeployment](/powershell/module/az.resources/new-azdeployment) or **New-AzSubscriptionDeployment**. The following example deploys a template to create a resource group:

```azurepowershell-interactive
New-AzSubscriptionDeployment `
  -Name demoDeployment `
  -Location centralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json `
  -rgName demoResourceGroup `
  -rgLocation centralus
```

For REST API, use [Deployments - Create At Subscription Scope](/rest/api/resources/deployments/createorupdateatsubscriptionscope).

## Deployment location and name

For subscription level deployments, you must provide a location for the deployment. The location of the deployment is separate from the location of the resources you deploy. The deployment location specifies where to store deployment data.

You can provide a name for the deployment, or use the default deployment name. The default name is the name of the template file. For example, deploying a template named **azuredeploy.json** creates a default deployment name of **azuredeploy**.

For each deployment name, the location is immutable. You can't create a deployment in one location when there's an existing deployment with the same name in a different location. If you get the error code `InvalidDeploymentLocation`, either use a different name or the same location as the previous deployment for that name.

## Use template functions

For subscription-level deployments, there are some important considerations when using template functions:

* The [resourceGroup()](template-functions-resource.md#resourcegroup) function is **not** supported.
* The [reference()](template-functions-resource.md#reference) and [list()](template-functions-resource.md#list) functions are supported.
* Use the [subscriptionResourceId()](template-functions-resource.md#subscriptionresourceid) function to get the resource ID for resources that are deployed at subscription level.

  For example, to get the resource ID for a policy definition, use:
  
  ```json
  subscriptionResourceId('Microsoft.Authorization/roleDefinitions/', parameters('roleDefinition'))
  ```
  
  The returned resource ID has the following format:

  ```json
  /subscriptions/{subscriptionId}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
  ```

## Create resource groups

To create a resource group in an Azure Resource Manager template, define a [Microsoft.Resources/resourceGroups](/azure/templates/microsoft.resources/allversions) resource with a name and location for the resource group. You can create a resource group and deploy resources to that resource group in the same template.

The following template creates an empty resource group.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "rgName": {
      "type": "string"
    },
    "rgLocation": {
      "type": "string"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Resources/resourceGroups",
      "apiVersion": "2018-05-01",
      "name": "[parameters('rgName')]",
      "location": "[parameters('rgLocation')]",
      "properties": {}
    }
  ],
  "outputs": {}
}
```

Use the [copy element](copy-resources.md) with resource groups to create more than one resource group.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "rgNamePrefix": {
      "type": "string"
    },
    "rgLocation": {
      "type": "string"
    },
    "instanceCount": {
      "type": "int"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Resources/resourceGroups",
      "apiVersion": "2018-05-01",
      "location": "[parameters('rgLocation')]",
      "name": "[concat(parameters('rgNamePrefix'), copyIndex())]",
      "copy": {
        "name": "rgCopy",
        "count": "[parameters('instanceCount')]"
      },
      "properties": {}
    }
  ],
  "outputs": {}
}
```

For information about resource iteration, see [Deploy more than one instance of a resource  in Azure Resource Manager Templates](./copy-resources.md), and [Tutorial: Create multiple resource instances with Resource Manager templates](./template-tutorial-create-multiple-instances.md).

## Resource group and resources

To create the resource group and deploy resources to it, use a nested template. The nested template defines the resources to deploy to the resource group. Set the nested template as dependent on the resource group to make sure the resource group exists before deploying the resources.

The following example creates a resource group, and deploys a storage account to the resource group.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "rgName": {
      "type": "string"
    },
    "rgLocation": {
      "type": "string"
    },
    "storagePrefix": {
      "type": "string",
      "maxLength": 11
    }
  },
  "variables": {
    "storageName": "[concat(parameters('storagePrefix'), uniqueString(subscription().id, parameters('rgName')))]"
  },
  "resources": [
    {
      "type": "Microsoft.Resources/resourceGroups",
      "apiVersion": "2018-05-01",
      "location": "[parameters('rgLocation')]",
      "name": "[parameters('rgName')]",
      "properties": {}
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2018-05-01",
      "name": "storageDeployment",
      "resourceGroup": "[parameters('rgName')]",
      "dependsOn": [
        "[resourceId('Microsoft.Resources/resourceGroups/', parameters('rgName'))]"
      ],
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {},
          "variables": {},
          "resources": [
            {
              "type": "Microsoft.Storage/storageAccounts",
              "apiVersion": "2017-10-01",
              "name": "[variables('storageName')]",
              "location": "[parameters('rgLocation')]",
              "sku": {
                "name": "Standard_LRS"
              }
              "kind": "StorageV2",
            }
          ],
          "outputs": {}
        }
      }
    }
  ],
  "outputs": {}
}
```

## Create policies

### Assign policy

The following example assigns an existing policy definition to the subscription. If the policy takes parameters, provide them as an object. If the policy doesn't take parameters, use the default empty object.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "policyDefinitionID": {
      "type": "string"
    },
    "policyName": {
      "type": "string"
    },
    "policyParameters": {
      "type": "object",
      "defaultValue": {}
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Authorization/policyAssignments",
      "apiVersion": "2018-03-01",
      "name": "[parameters('policyName')]",
      "properties": {
        "scope": "[subscription().id]",
        "policyDefinitionId": "[parameters('policyDefinitionID')]",
        "parameters": "[parameters('policyParameters')]"
      }
    }
  ]
}
```

To deploy this template with Azure CLI, use:

```azurecli-interactive
# Built-in policy that accepts parameters
definition=$(az policy definition list --query "[?displayName=='Allowed locations'].id" --output tsv)

az deployment create \
  --name demoDeployment \
  --location centralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json \
  --parameters policyDefinitionID=$definition policyName=setLocation policyParameters="{'listOfAllowedLocations': {'value': ['westus']} }"
```

To deploy this template with PowerShell, use:

```azurepowershell-interactive
$definition = Get-AzPolicyDefinition | Where-Object { $_.Properties.DisplayName -eq 'Allowed locations' }

$locations = @("westus", "westus2")
$policyParams =@{listOfAllowedLocations = @{ value = $locations}}

New-AzSubscriptionDeployment `
  -Name policyassign `
  -Location centralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json `
  -policyDefinitionID $definition.PolicyDefinitionId `
  -policyName setLocation `
  -policyParameters $policyParams
```

### Define and assign policy

You can [define](../../governance/policy/concepts/definition-structure.md) and assign a policy in the same template.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Authorization/policyDefinitions",
      "apiVersion": "2018-05-01",
      "name": "locationpolicy",
      "properties": {
        "policyType": "Custom",
        "parameters": {},
        "policyRule": {
          "if": {
            "field": "location",
            "equals": "northeurope"
          },
          "then": {
            "effect": "deny"
          }
        }
      }
    },
    {
      "type": "Microsoft.Authorization/policyAssignments",
      "apiVersion": "2018-05-01",
      "name": "location-lock",
      "dependsOn": [
        "locationpolicy"
      ],
      "properties": {
        "scope": "[subscription().id]",
        "policyDefinitionId": "[resourceId('Microsoft.Authorization/policyDefinitions', 'locationpolicy')]"
      }
    }
  ]
}
```

To create the policy definition in your subscription, and apply it to the subscription, use the following CLI command:

```azurecli
az deployment create \
  --name demoDeployment \
  --location centralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json
```

To deploy this template with PowerShell, use:

```azurepowershell
New-AzSubscriptionDeployment `
  -Name definePolicy `
  -Location centralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json
```

## Template samples

* [Create a resource group, lock it and give permissions to it](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-level-deployments/create-rg-lock-role-assignment).
* [Create a resource group, a policy, and a policy assignment](https://github.com/Azure/azure-docs-json-samples/blob/master/subscription-level-deployment/azuredeploy.json).

## Next steps

* To learn about assigning roles, see [Manage access to Azure resources using RBAC and Azure Resource Manager templates](../../role-based-access-control/role-assignments-template.md).
* For an example of deploying workspace settings for Azure Security Center, see [deployASCwithWorkspaceSettings.json](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json).
* Sample templates can be found at [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-level-deployments).
* You can also deploy templates at [management group level](deploy-to-management-group.md) and [tenant level](deploy-to-tenant.md).
