---
title: Define multiple instances of a property
description: Use copy operation in an Azure Resource Manager template to iterate multiple times when creating a property on a resource.
ms.topic: conceptual
ms.date: 02/13/2020
---
# Property iteration in Azure Resource Manager templates

This article shows you how to create more than one instance of a property in your Azure Resource Manager template. By adding the **copy** element to the properties section of a resource in your template, you can dynamically set the number of items for a property during deployment. You also avoid having to repeat template syntax.

You can also use copy with [resources](copy-resources.md), [variables](copy-variables.md), and [outputs](copy-outputs.md).

## Property iteration

The copy element has the following general format:

```json
"copy": [
  {
    "name": "<name-of-loop>",
    "count": <number-of-iterations>,
    "input": <values-for-the-property>
  }
]
```

For **name**, provide the name of the resource property that you want to create. The **count** property specifies the number of iterations you want for the property.

The **input** property specifies the properties that you want to repeat. You create an array of elements constructed from the value in the **input** property.

The following example shows how to apply `copy` to the dataDisks property on a virtual machine:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "numberOfDataDisks": {
      "type": "int",
      "minValue": 0,
      "maxValue": 16,
      "defaultValue": 16,
      "metadata": {
        "description": "The number of dataDisks to create."
      }
    },
    ...
  },
  "resources": [
    {
      "type": "Microsoft.Compute/virtualMachines",
      "apiVersion": "2017-03-30",
      ...
      "properties": {
        "storageProfile": {
          ...
          "copy": [
            {
              "name": "dataDisks",
              "count": "[parameters('numberOfDataDisks')]",
              "input": {
                "diskSizeGB": 1023,
                "lun": "[copyIndex('dataDisks')]",
                "createOption": "Empty"
              }
            }
          ]
        }
      }
    }
  ]
}
```

Notice that when using `copyIndex` inside a property iteration, you must provide the name of the iteration.

> [!NOTE]
> Property iteration also supports an offset argument. The offset must come after the name of the iteration, such as copyIndex('dataDisks', 1).
>

Resource Manager expands the `copy` array during deployment. The name of the array becomes the name of the property. The input values become the object properties. The deployed template becomes:

```json
{
  "name": "examplevm",
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2017-03-30",
  "properties": {
    "storageProfile": {
      "dataDisks": [
        {
          "lun": 0,
          "createOption": "Empty",
          "diskSizeGB": 1023
        },
        {
          "lun": 1,
          "createOption": "Empty",
          "diskSizeGB": 1023
        },
        {
          "lun": 2,
          "createOption": "Empty",
          "diskSizeGB": 1023
        }
      ],
      ...
```

The copy element is an array so you can specify more than one property for the resource.

```json
{
  "type": "Microsoft.Network/loadBalancers",
  "apiVersion": "2017-10-01",
  "name": "examleLB",
  "properties": {
    "copy": [
      {
        "name": "loadBalancingRules",
        "count": "[length(parameters('loadBalancingRules'))]",
        "input": {
          ...
        }
      },
      {
        "name": "probes",
        "count": "[length(parameters('loadBalancingRules'))]",
        "input": {
          ...
        }
      }
    ]
  }
}
```

You can use resource and property iteration together. Reference the property iteration by name.

```json
{
  "type": "Microsoft.Network/virtualNetworks",
  "apiVersion": "2018-04-01",
  "name": "[concat(parameters('vnetname'), copyIndex())]",
  "copy":{
    "count": 2,
    "name": "vnetloop"
  },
  "location": "[resourceGroup().location]",
  "properties": {
    "addressSpace": {
      "addressPrefixes": [
        "[parameters('addressPrefix')]"
      ]
    },
    "copy": [
      {
        "name": "subnets",
        "count": 2,
        "input": {
          "name": "[concat('subnet-', copyIndex('subnets'))]",
          "properties": {
            "addressPrefix": "[variables('subnetAddressPrefix')[copyIndex('subnets')]]"
          }
        }
      }
    ]
  }
}
```

## Copy limits

The count can't exceed 800.

The count can't be a negative number. If you deploy a template with Azure PowerShell 2.6 or later, Azure CLI 2.0.74 or later, or REST API version **2019-05-10** or later, you can set count to zero. Earlier versions of PowerShell, CLI, and the REST API don't support zero for count.

## Example templates

The following example shows a common scenario for creating more than one value for a property.

|Template  |Description  |
|---------|---------|
|[VM deployment with a variable number of data disks](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-windows-copy-datadisks) |Deploys several data disks with a virtual machine. |

## Next steps

* To go through a tutorial, see [Tutorial: create multiple resource instances using Resource Manager templates](template-tutorial-create-multiple-instances.md).
* For other uses of the copy element, see:
  * [Resource iteration in Azure Resource Manager templates](copy-resources.md)
  * [Variable iteration in Azure Resource Manager templates](copy-variables.md)
  * [Output iteration in Azure Resource Manager templates](copy-outputs.md)
* If you want to learn about the sections of a template, see [Authoring Azure Resource Manager Templates](template-syntax.md).
* To learn how to deploy your template, see [Deploy an application with Azure Resource Manager Template](deploy-powershell.md).

