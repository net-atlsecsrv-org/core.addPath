---
title: Deploy multiple instances of resources
description: Use copy operation and arrays in an Azure Resource Manager template (ARM template) to deploy resource type many times.
ms.topic: conceptual
ms.date: 04/01/2021
---
# Resource iteration in ARM templates

This article shows you how to create more than one instance of a resource in your Azure Resource Manager template (ARM template). By adding copy loop to the resources section of your template, you can dynamically set the number of resources to deploy. You also avoid having to repeat template syntax.

You can also use copy loop with [properties](copy-properties.md), [variables](copy-variables.md), and [outputs](copy-outputs.md).

If you need to specify whether a resource is deployed at all, see [condition element](conditional-resource-deployment.md).

## Syntax

# [JSON](#tab/json)

Add the `copy` element to the resources section of your template to deploy multiple instances of the resource. The `copy` element has the following general format:

```json
"copy": {
  "name": "<name-of-loop>",
  "count": <number-of-iterations>,
  "mode": "serial" <or> "parallel",
  "batchSize": <number-to-deploy-serially>
}
```

The `name` property is any value that identifies the loop. The `count` property specifies the number of iterations you want for the resource type.

Use the `mode` and `batchSize` properties to specify if the resources are deployed in parallel or in sequence. These properties are described in [Serial or Parallel](#serial-or-parallel).

# [Bicep](#tab/bicep)

Loops can be used declare multiple resources by:

- Iterating over an array:

  ```bicep
  @batchSize(<number>)
  resource <resource-symbolic-name> '<resource-type>@<api-version>' = [for <item> in <collection>: {
    <resource-properties>
  }]
  ```

- Iterating over the elements of an array

  ```bicep
  @batchSize(<number>)
  resource <resource-symbolic-name> '<resource-type>@<api-version>' = [for (<item>, <index>) in <collection>: {
    <resource-properties>
  }]
  ```

- Using loop index

  ```bicep
  @batchSize(<number>)
  resource <resource-symbolic-name> '<resource-type>@<api-version>' = [for <index> in range(<start>, <stop>): {
    <resource-properties>
  }]
  ```

---

## Copy limits

The count can't exceed 800.

The count can't be a negative number. It can be zero if you deploy the template with a recent version of Azure CLI, PowerShell, or REST API. Specifically, you must use:

- Azure PowerShell **2.6** or later
- Azure CLI **2.0.74** or later
- REST API version **2019-05-10** or later
- [Linked deployments](linked-templates.md) must use API version **2019-05-10** or later for the deployment resource type

Earlier versions of PowerShell, CLI, and the REST API don't support zero for count.

Be careful using [complete mode deployment](deployment-modes.md) with copy loop. If you redeploy with complete mode to a resource group, any resources that aren't specified in the template after resolving the copy loop are deleted.

## Resource iteration

The following example creates the number of storage accounts specified in the `storageCount` parameter.

# [JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageCount": {
      "type": "int",
      "defaultValue": 2
    }
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {},
      "copy": {
        "name": "storagecopy",
        "count": "[parameters('storageCount')]"
      }
    }
  ]
}
```

Notice that the name of each resource includes the `copyIndex()` function, which returns the current iteration in the loop. `copyIndex()` is zero-based. So, the following example:

```json
"name": "[concat('storage', copyIndex())]",
```

Creates these names:

- storage0
- storage1
- storage2.

To offset the index value, you can pass a value in the `copyIndex()` function. The number of iterations is still specified in the copy element, but the value of `copyIndex` is offset by the specified value. So, the following example:

```json
"name": "[concat('storage', copyIndex(1))]",
```

Creates these names:

- storage1
- storage2
- storage3

The copy operation is helpful when working with arrays because you can iterate through each element in the array. Use the `length` function on the array to specify the count for iterations, and `copyIndex` to retrieve the current index in the array.

# [Bicep](#tab/bicep)

```bicep
param storageCount int = 2

resource storage_id 'Microsoft.Storage/storageAccounts@2019-04-01' = [for i in range(0, storageCount): {
  name: '${i}storage${uniqueString(resourceGroup().id)}'
  location: resourceGroup().location
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
  properties: {}
}]
```

Notice the index `i` is used in creating the storage account resource name.

---

The following example creates one storage account for each name provided in the parameter.

# [JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "storageNames": {
          "type": "array",
          "defaultValue": [
            "contoso",
            "fabrikam",
            "coho"
          ]
      }
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(parameters('storageNames')[copyIndex()], uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {},
      "copy": {
        "name": "storagecopy",
        "count": "[length(parameters('storageNames'))]"
      }
    }
  ],
  "outputs": {}
}
```

# [Bicep](#tab/bicep)

```bicep
param storageNames array = [
  'contoso'
  'fabrikam'
  'coho'
]

resource storageNames_id 'Microsoft.Storage/storageAccounts@2019-04-01' = [for name in storageNames: {
  name: concat(name, uniqueString(resourceGroup().id))
  location: resourceGroup().location
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
  properties: {}
}]
```

---

If you want to return values from the deployed resources, you can use [copy in the outputs section](copy-outputs.md).

## Serial or Parallel

By default, Resource Manager creates the resources in parallel. It applies no limit to the number of resources deployed in parallel, other than the total limit of 800 resources in the template. The order in which they're created isn't guaranteed.

However, you may want to specify that the resources are deployed in sequence. For example, when updating a production environment, you may want to stagger the updates so only a certain number are updated at any one time.

For example, to serially deploy storage accounts two at a time, use:

# [JSON](#tab/json)

To serially deploy more than one instance of a resource, set `mode` to **serial** and `batchSize` to the number of instances to deploy at a time. With serial mode, Resource Manager creates a dependency on earlier instances in the loop, so it doesn't start one batch until the previous batch completes.

The value for `batchSize` can't exceed the value for `count` in the copy element.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "copy": {
        "name": "storagecopy",
        "count": 4,
        "mode": "serial",
        "batchSize": 2
      },
      "properties": {}
    }
  ],
  "outputs": {}
}
```

The `mode` property also accepts **parallel**, which is the default value.

# [Bicep](#tab/bicep)

To serially deploy more than one instance of a resource, set the `batchSize` [decorator](./bicep-file.md#resource-and-module-decorators) to the number of instances to deploy at a time. With serial mode, Resource Manager creates a dependency on earlier instances in the loop, so it doesn't start one batch until the previous batch completes.

```bicep
@batchSize(2)
resource storage_id 'Microsoft.Storage/storageAccounts@2019-04-01' = [for i in range(0, 4): {
  name: '${i}storage${uniqueString(resourceGroup().id)}'
  location: resourceGroup().location
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
  properties: {}
}]
```

---

## Iteration for a child resource

You can't use a copy loop for a child resource. To create more than one instance of a resource that you typically define as nested within another resource, you must instead create that resource as a top-level resource. You define the relationship with the parent resource through the type and name properties.

For example, suppose you typically define a dataset as a child resource within a data factory.

```json
"resources": [
{
  "type": "Microsoft.DataFactory/factories",
  "name": "exampleDataFactory",
  ...
  "resources": [
    {
      "type": "datasets",
      "name": "exampleDataSet",
      "dependsOn": [
        "exampleDataFactory"
      ],
      ...
    }
  ]
```

To create more than one data set, move it outside of the data factory. The dataset must be at the same level as the data factory, but it's still a child resource of the data factory. You preserve the relationship between data set and data factory through the type and name properties. Since type can no longer be inferred from its position in the template, you must provide the fully qualified type in the format: `{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}`.

To establish a parent/child relationship with an instance of the data factory, provide a name for the data set that includes the parent resource name. Use the format: `{parent-resource-name}/{child-resource-name}`.

The following example shows the implementation:

# [JSON](#tab/json)

```json
"resources": [
{
  "type": "Microsoft.DataFactory/factories",
  "name": "exampleDataFactory",
  ...
},
{
  "type": "Microsoft.DataFactory/factories/datasets",
  "name": "[concat('exampleDataFactory', '/', 'exampleDataSet', copyIndex())]",
  "dependsOn": [
    "exampleDataFactory"
  ],
  "copy": {
    "name": "datasetcopy",
    "count": "3"
  },
  ...
}]
```

# [Bicep](#tab/bicep)

```bicep
resource dataFactoryName_resource 'Microsoft.DataFactory/factories@2018-06-01' = {
  name: "exampleDataFactory"
  ...
}

resource dataFactoryName_ArmtemplateTestDatasetIn 'Microsoft.DataFactory/factories/datasets@2018-06-01' = [for i in range(0, 3): {
  name: 'exampleDataFactory/exampleDataset${i}'
  ...
}
```

---

## Example templates

The following examples show common scenarios for creating more than one instance of a resource or property.

|Template  |Description  |
|---------|---------|
|[Copy storage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystorage.json) |Deploys more than one storage account with an index number in the name. |
|[Serial copy storage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/serialcopystorage.json) |Deploys several storage accounts one at time. The name includes the index number. |
|[Copy storage with array](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystoragewitharray.json) |Deploys several storage accounts. The name includes a value from an array. |

## Next steps

- To set dependencies on resources that are created in a copy loop, see [Define the order for deploying resources in ARM templates](define-resource-dependency.md).
- To go through a tutorial, see [Tutorial: Create multiple resource instances with ARM templates](template-tutorial-create-multiple-instances.md).
- For a Microsoft Learn module that covers resource copy, see [Manage complex cloud deployments by using advanced ARM template features](/learn/modules/manage-deployments-advanced-arm-template-features/).
- For other uses of the copy loop, see:
  - [Property iteration in ARM templates](copy-properties.md)
  - [Variable iteration in ARM templates](copy-variables.md)
  - [Output iteration in ARM templates](copy-outputs.md)
- For information about using copy with nested templates, see [Using copy](linked-templates.md#using-copy).
