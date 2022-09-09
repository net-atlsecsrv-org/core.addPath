---
title: Template functions - numeric
description: Describes the functions to use in an Azure Resource Manager template (ARM template) to work with numbers.
ms.topic: conceptual
ms.date: 11/18/2020
---
# Numeric functions for ARM templates

Resource Manager provides the following functions for working with integers in your Azure Resource Manager template (ARM template):

* [add](#add)
* [copyIndex](#copyindex)
* [div](#div)
* [float](#float)
* [int](#int)
* [max](#max)
* [min](#min)
* [mod](#mod)
* [mul](#mul)
* [sub](#sub)

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## add

`add(operand1, operand2)`

Returns the sum of the two provided integers. The `add` function is not supported in Bicep. Use the `+` operator instead.

### Parameters

| Parameter | Required | Type | Description |
|:--- |:--- |:--- |:--- |
|operand1 |Yes |int |First number to add. |
|operand2 |Yes |int |Second number to add. |

### Return value

An integer that contains the sum of the parameters.

### Example

The following [example template](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/add.json) adds two parameters.

# [JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "first": {
      "type": "int",
      "defaultValue": 5,
      "metadata": {
        "description": "First integer to add"
      }
    },
    "second": {
      "type": "int",
      "defaultValue": 3,
      "metadata": {
        "description": "Second integer to add"
      }
    }
  },
  "resources": [
  ],
  "outputs": {
    "addResult": {
      "type": "int",
      "value": "[add(parameters('first'), parameters('second'))]"
    }
  }
}
```

# [Bicep](#tab/bicep)

```bicep
param first int = 5
param second int = 3

output addResult int = first + second
```

---

The output from the preceding example with the default values is:

| Name | Type | Value |
| ---- | ---- | ----- |
| addResult | Int | 8 |

## copyIndex

`copyIndex(loopName, offset)`

Returns the index of an iteration loop.

### Parameters

| Parameter | Required | Type | Description |
|:--- |:--- |:--- |:--- |
| loopName | No | string | The name of the loop for getting the iteration. |
| offset |No |int |The number to add to the zero-based iteration value. |

### Remarks

This function is always used with a **copy** object. If no value is provided for **offset**, the current iteration value is returned. The iteration value starts at zero.

The **loopName** property enables you to specify whether copyIndex is referring to a resource iteration or property iteration. If no value is provided for **loopName**, the current resource type iteration is used. Provide a value for **loopName** when iterating on a property.

For more information about using copy, see:

* [Resource iteration in ARM templates](copy-resources.md)
* [Property iteration in ARM templates](copy-properties.md)
* [Variable iteration in ARM templates](copy-variables.md)
* [Output iteration in ARM templates](copy-outputs.md)

### Example

The following example shows a copy loop and the index value included in the name.

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
  ],
  "outputs": {}
}
```

# [Bicep](#tab/bicep)

> [!NOTE]
> Loops and `copyIndex` are not implemented yet in Bicep.  See [Loops](https://github.com/Azure/bicep/blob/main/docs/spec/loops.md).

---

### Return value

An integer representing the current index of the iteration.

## div

`div(operand1, operand2)`

Returns the integer division of the two provided integers. The `div` function is not supported in Bicep. Use the `/` operator instead.

### Parameters

| Parameter | Required | Type | Description |
|:--- |:--- |:--- |:--- |
| operand1 |Yes |int |The number being divided. |
| operand2 |Yes |int |The number that is used to divide. Can't be 0. |

### Return value

An integer representing the division.

### Example

The following [example template](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/div.json) divides one parameter by another parameter.

# [JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "first": {
      "type": "int",
      "defaultValue": 8,
      "metadata": {
        "description": "Integer being divided"
      }
    },
    "second": {
      "type": "int",
      "defaultValue": 3,
      "metadata": {
        "description": "Integer used to divide"
      }
    }
  },
  "resources": [
  ],
  "outputs": {
    "divResult": {
      "type": "int",
      "value": "[div(parameters('first'), parameters('second'))]"
    }
  }
}
```

# [Bicep](#tab/bicep)

```bicep
param first int = 8
param second int = 3

output addResult int = first / second
```

---

The output from the preceding example with the default values is:

| Name | Type | Value |
| ---- | ---- | ----- |
| divResult | Int | 2 |

## float

`float(arg1)`

Converts the value to a floating point number. You only use this function when passing custom parameters to an application, such as a Logic App. The `float` function is not supported in Becip.  See [Support numeric types other than 32bit integers](https://github.com/Azure/bicep/issues/486).

### Parameters

| Parameter | Required | Type | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |string or int |The value to convert to a floating point number. |

### Return value

A floating point number.

### Example

The following example shows how to use float to pass parameters to a Logic App:

# [JSON](#tab/json)

```json
{
  "type": "Microsoft.Logic/workflows",
  "properties": {
    ...
    "parameters": {
      "custom1": {
        "value": "[float('3.0')]"
      },
      "custom2": {
        "value": "[float(3)]"
      },
```

# [Bicep](#tab/bicep)

> [!NOTE]
> The `float` function is not supported in Bicep.  See [Support numeric types other than 32bit integers](https://github.com/Azure/bicep/issues/486).

---

## int

`int(valueToConvert)`

Converts the specified value to an integer.

### Parameters

| Parameter | Required | Type | Description |
|:--- |:--- |:--- |:--- |
| valueToConvert |Yes |string or int |The value to convert to an integer. |

### Return value

An integer of the converted value.

### Example

The following [example template](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/int.json) converts the user-provided parameter value to integer.

# [JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "stringToConvert": {
      "type": "string",
      "defaultValue": "4"
    }
  },
  "resources": [
  ],
  "outputs": {
    "intResult": {
      "type": "int",
      "value": "[int(parameters('stringToConvert'))]"
    }
  }
}
```

# [Bicep](#tab/bicep)

```bicep
param stringToConvert string = '4'

output inResult int = int(stringToConvert)
```

---

The output from the preceding example with the default values is:

| Name | Type | Value |
| ---- | ---- | ----- |
| intResult | Int | 4 |

## max

`max (arg1)`

Returns the maximum value from an array of integers or a comma-separated list of integers.

### Parameters

| Parameter | Required | Type | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |array of integers, or comma-separated list of integers |The collection to get the maximum value. |

### Return value

An integer representing the maximum value from the collection.

### Example

The following [example template](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/max.json) shows how to use max with an array and a list of integers:

# [JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "arrayToTest": {
      "type": "array",
      "defaultValue": [ 0, 3, 2, 5, 4 ]
    }
  },
  "resources": [],
  "outputs": {
    "arrayOutput": {
      "type": "int",
      "value": "[max(parameters('arrayToTest'))]"
    },
    "intOutput": {
      "type": "int",
      "value": "[max(0,3,2,5,4)]"
    }
  }
}
```

# [Bicep](#tab/bicep)

```bicep
param arrayToTest array = [
  0
  3
  2
  5
  4
]

output arrayOutPut int = max(arrayToTest)
output intOutput int = max(0,3,2,5,4)
```

---

The output from the preceding example with the default values is:

| Name | Type | Value |
| ---- | ---- | ----- |
| arrayOutput | Int | 5 |
| intOutput | Int | 5 |

## min

`min (arg1)`

Returns the minimum value from an array of integers or a comma-separated list of integers.

### Parameters

| Parameter | Required | Type | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |array of integers, or comma-separated list of integers |The collection to get the minimum value. |

### Return value

An integer representing minimum value from the collection.

### Example

The following [example template](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/min.json) shows how to use min with an array and a list of integers:

# [JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "arrayToTest": {
      "type": "array",
      "defaultValue": [ 0, 3, 2, 5, 4 ]
    }
  },
  "resources": [],
  "outputs": {
    "arrayOutput": {
      "type": "int",
      "value": "[min(parameters('arrayToTest'))]"
    },
    "intOutput": {
      "type": "int",
      "value": "[min(0,3,2,5,4)]"
    }
  }
}
```

# [Bicep](#tab/bicep)

```bicep
param arrayToTest array = [
  0
  3
  2
  5
  4
]

output arrayOutPut int = min(arrayToTest)
output intOutput int = min(0,3,2,5,4)
```

---

The output from the preceding example with the default values is:

| Name | Type | Value |
| ---- | ---- | ----- |
| arrayOutput | Int | 0 |
| intOutput | Int | 0 |

## mod

`mod(operand1, operand2)`

Returns the remainder of the integer division using the two provided integers. The `mod` function is not supported in Bicep. Use the `%` operator instead.

### Parameters

| Parameter | Required | Type | Description |
|:--- |:--- |:--- |:--- |
| operand1 |Yes |int |The number being divided. |
| operand2 |Yes |int |The number that is used to divide, Can't be 0. |

### Return value

An integer representing the remainder.

### Example

The following [example template](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/mod.json) returns the remainder of dividing one parameter by another parameter.

# [JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "first": {
      "type": "int",
      "defaultValue": 7,
      "metadata": {
        "description": "Integer being divided"
      }
    },
    "second": {
      "type": "int",
      "defaultValue": 3,
      "metadata": {
        "description": "Integer used to divide"
      }
    }
  },
  "resources": [
  ],
  "outputs": {
    "modResult": {
      "type": "int",
      "value": "[mod(parameters('first'), parameters('second'))]"
    }
  }
}
```

# [Bicep](#tab/bicep)

```bicep
param first int = 7
param second int = 3

output modResult int = first % second
```

---

The output from the preceding example with the default values is:

| Name | Type | Value |
| ---- | ---- | ----- |
| modResult | Int | 1 |

## mul

`mul(operand1, operand2)`

Returns the multiplication of the two provided integers. The `mul` function is not supported in Bicep. Use the `*` operator instead.

### Parameters

| Parameter | Required | Type | Description |
|:--- |:--- |:--- |:--- |
| operand1 |Yes |int |First number to multiply. |
| operand2 |Yes |int |Second number to multiply. |

### Return value

An integer representing the multiplication.

### Example

The following [example template](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/mul.json) multiplies one parameter by another parameter.

# [JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "first": {
      "type": "int",
      "defaultValue": 5,
      "metadata": {
        "description": "First integer to multiply"
      }
    },
    "second": {
      "type": "int",
      "defaultValue": 3,
      "metadata": {
        "description": "Second integer to multiply"
      }
    }
  },
  "resources": [
  ],
  "outputs": {
    "mulResult": {
      "type": "int",
      "value": "[mul(parameters('first'), parameters('second'))]"
    }
  }
}
```

# [Bicep](#tab/bicep)

```bicep
param first int = 5
param second int = 3

output mulResult int = first * second
```

---

The output from the preceding example with the default values is:

| Name | Type | Value |
| ---- | ---- | ----- |
| mulResult | Int | 15 |

## sub

`sub(operand1, operand2)`

Returns the subtraction of the two provided integers. The `sub` function is not supported in Bicep. Use the `-` operator instead.

### Parameters

| Parameter | Required | Type | Description |
|:--- |:--- |:--- |:--- |
| operand1 |Yes |int |The number that is subtracted from. |
| operand2 |Yes |int |The number that is subtracted. |

### Return value

An integer representing the subtraction.

### Example

The following [example template](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/sub.json) subtracts one parameter from another parameter.

# [JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "first": {
      "type": "int",
      "defaultValue": 7,
      "metadata": {
        "description": "Integer subtracted from"
      }
    },
    "second": {
      "type": "int",
      "defaultValue": 3,
      "metadata": {
        "description": "Integer to subtract"
      }
    }
  },
  "resources": [
  ],
  "outputs": {
    "subResult": {
      "type": "int",
      "value": "[sub(parameters('first'), parameters('second'))]"
    }
  }
}
```

# [Bicep](#tab/bicep)

```bicep
param first int = 7
param second int = 3

output subResult int = first - second
```

---

The output from the preceding example with the default values is:

| Name | Type | Value |
| ---- | ---- | ----- |
| subResult | Int | 4 |

## Next steps

* For a description of the sections in an ARM template, see [Understand the structure and syntax of ARM templates](template-syntax.md).
* To iterate a specified number of times when creating a type of resource, see [Resource iteration in ARM templates](copy-resources.md).
