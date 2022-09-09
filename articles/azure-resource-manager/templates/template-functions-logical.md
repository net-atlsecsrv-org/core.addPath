---
title: Template functions - logical
description: Describes the functions to use in an Azure Resource Manager template to determine logical values.
ms.topic: conceptual
ms.date: 11/18/2020
---
# Logical functions for ARM templates

Resource Manager provides several functions for making comparisons in your Azure Resource Manager (ARM) templates.

* [and](#and)
* [bool](#bool)
* [false](#false)
* [if](#if)
* [not](#not)
* [or](#or)
* [true](#true)

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## and

`and(arg1, arg2, ...)`

Checks whether all parameter values are true. The `and` function is not supported in Bicep. Use the `&&` operator instead.

### Parameters

| Parameter | Required | Type | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |boolean |The first value to check whether is true. |
| arg2 |Yes |boolean |The second value to check whether is true. |
| additional arguments |No |boolean |Additional arguments to check whether are true. |

### Return value

Returns **True** if all values are true; otherwise, **False**.

### Examples

The following [example template](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) shows how to use logical functions.

# [JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "andExampleOutput": {
      "type": "bool",
      "value": "[and(bool('true'), bool('false'))]"
    },
    "orExampleOutput": {
      "type": "bool",
      "value": "[or(bool('true'), bool('false'))]"
    },
    "notExampleOutput": {
      "type": "bool",
      "value": "[not(bool('true'))]"
    }
  }
}
```

# [Bicep](#tab/bicep)

```bicep
output andExampleOutput bool = bool('true') && bool('false')
output orExampleOutput bool = bool('true') || bool('false')
output notExampleOutput bool = !(bool('true'))
```

---

The output from the preceding example is:

| Name | Type | Value |
| ---- | ---- | ----- |
| andExampleOutput | Bool | False |
| orExampleOutput | Bool | True |
| notExampleOutput | Bool | False |

## bool

`bool(arg1)`

Converts the parameter to a boolean.

### Parameters

| Parameter | Required | Type | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |string or int |The value to convert to a boolean. |

### Return value

A boolean of the converted value.

### Remarks

You can also use [true()](#true) and [false()](#false) to get boolean values.

### Examples

The following [example template](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/bool.json) shows how to use bool with a string or integer.

# [JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "trueString": {
      "type": "bool",
      "value": "[bool('true')]",
    },
    "falseString": {
      "type": "bool",
      "value": "[bool('false')]"
    },
    "trueInt": {
      "type": "bool",
      "value": "[bool(1)]"
    },
    "falseInt": {
      "type": "bool",
      "value": "[bool(0)]"
    }
  }
}
```

# [Bicep](#tab/bicep)

```bicep
output trueString bool = bool('true')
output falseString bool = bool('false')
output trueInt bool = bool(1)
output falseInt bool = bool(0)
```

---
The output from the preceding example with the default values is:

| Name | Type | Value |
| ---- | ---- | ----- |
| trueString | Bool | True |
| falseString | Bool | False |
| trueInt | Bool | True |
| falseInt | Bool | False |

## false

`false()`

Returns false. The `false` function is not available in Bicep.  Use the `false` keyword instead.

### Parameters

The false function doesn't accept any parameters.

### Return value

A boolean that is always false.

### Example

The following example returns a false output value.

# [JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "falseOutput": {
      "type": "bool",
      "value": "[false()]"
    }
  }
}
```

# [Bicep](#tab/bicep)

```bicep
output falseOutput bool = false
```

---

The output from the preceding example is:

| Name | Type | Value |
| ---- | ---- | ----- |
| falseOutput | Bool | False |

## if

`if(condition, trueValue, falseValue)`

Returns a value based on whether a condition is true or false. The `if` function is not supported in Bicep. Use the `?:` operator instead.

### Parameters

| Parameter | Required | Type | Description |
|:--- |:--- |:--- |:--- |
| condition |Yes |boolean |The value to check whether it's true or false. |
| trueValue |Yes | string, int, object, or array |The value to return when the condition is true. |
| falseValue |Yes | string, int, object, or array |The value to return when the condition is false. |

### Return value

Returns second parameter when first parameter is **True**; otherwise, returns third parameter.

### Remarks

When the condition is **True**, only the true value is evaluated. When the condition is **False**, only the false value is evaluated. With the **if** function, you can include expressions that are only conditionally valid. For example, you can reference a resource that exists under one condition but not under the other condition. An example of conditionally evaluating expressions is shown in the following section.

### Examples

The following [example template](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/if.json) shows how to use the `if` function.

# [JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
  ],
  "outputs": {
    "yesOutput": {
      "type": "string",
      "value": "[if(equals('a', 'a'), 'yes', 'no')]"
    },
    "noOutput": {
      "type": "string",
      "value": "[if(equals('a', 'b'), 'yes', 'no')]"
    },
    "objectOutput": {
      "type": "object",
      "value": "[if(equals('a', 'a'), json('{\"test\": \"value1\"}'), json('null'))]"
    }
  }
}
```

# [Bicep](#tab/bicep)

```bicep
output yesOutput string = 'a' == 'a' ? 'yes' : 'no'
output noOutput string = 'a' == 'b' ? 'yes' : 'no'
output objectOutput object = 'a' == 'a' ? json('{"test": "value1"}') : json('null')
```

---

The output from the preceding example is:

| Name | Type | Value |
| ---- | ---- | ----- |
| yesOutput | String | yes |
| noOutput | String | no |
| objectOutput | Object | { "test": "value1" } |

The following [example template](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/conditionWithReference.json) shows how to use this function with expressions that are only conditionally valid.

# [JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string"
    },
    "location": {
      "type": "string"
    },
    "logAnalytics": {
      "type": "string",
      "defaultValue": ""
    }
  },
  "resources": [
    {
      "condition": "[not(empty(parameters('logAnalytics')))]",
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "apiVersion": "2017-03-30",
      "name": "[concat(parameters('vmName'),'/omsOnboarding')]",
      "location": "[parameters('location')]",
      "properties": {
        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
        "type": "MicrosoftMonitoringAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "workspaceId": "[if(not(empty(parameters('logAnalytics'))), reference(parameters('logAnalytics'), '2015-11-01-preview').customerId, json('null'))]"
        },
        "protectedSettings": {
          "workspaceKey": "[if(not(empty(parameters('logAnalytics'))), listKeys(parameters('logAnalytics'), '2015-11-01-preview').primarySharedKey, json('null'))]"
        }
      }
    }
  ],
  "outputs": {
    "mgmtStatus": {
      "type": "string",
      "value": "[if(not(empty(parameters('logAnalytics'))), 'Enabled monitoring for VM!', 'Nothing to enable')]"
    }
  }
}
```

# [Bicep](#tab/bicep)

> [!NOTE]
> `Conditions` are not yet implemented in Bicep. See [Conditions](https://github.com/Azure/bicep/issues/186).

---

## not

`not(arg1)`

Converts boolean value to its opposite value. The `not` function is not supported in Bicep. Use the `!` operator instead.

### Parameters

| Parameter | Required | Type | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |boolean |The value to convert. |

### Return value

Returns **True** when parameter is **False**. Returns **False** when parameter is **True**.

### Examples

The following [example template](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) shows how to use logical functions.

# [JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "andExampleOutput": {
      "type": "bool",
      "value": "[and(bool('true'), bool('false'))]",
    },
    "orExampleOutput": {
      "type": "bool",
      "value": "[or(bool('true'), bool('false'))]"
    },
    "notExampleOutput": {
      "type": "bool",
      "value": "[not(bool('true'))]"
    }
  }
}
```

# [Bicep](#tab/bicep)

```bicep
output andExampleOutput bool = bool('true') && bool('false')
output orExampleOutput bool = bool('true') || bool('false')
output notExampleOutput bool = !(bool('true'))
```

---

The output from the preceding example is:

| Name | Type | Value |
| ---- | ---- | ----- |
| andExampleOutput | Bool | False |
| orExampleOutput | Bool | True |
| notExampleOutput | Bool | False |

The following [example template](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/not-equals.json) uses **not** with [equals](template-functions-comparison.md#equals).

# [JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
  ],
  "outputs": {
    "checkNotEquals": {
      "type": "bool",
      "value": "[not(equals(1, 2))]"
    }
  }
}
```

# [Bicep](#tab/bicep)

```bicep
output checkNotEquals bool = !(1 == 2)
```

---

The output from the preceding example is:

| Name | Type | Value |
| ---- | ---- | ----- |
| checkNotEquals | Bool | True |

## or

`or(arg1, arg2, ...)`

Checks whether any parameter value is true. The `or` function is not supported in Bicep. Use the `||` operator instead.

### Parameters

| Parameter | Required | Type | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |boolean |The first value to check whether is true. |
| arg2 |Yes |boolean |The second value to check whether is true. |
| additional arguments |No |boolean |Additional arguments to check whether are true. |

### Return value

Returns **True** if any value is true; otherwise, **False**.

### Examples

The following [example template](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) shows how to use logical functions.

# [JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "andExampleOutput": {
      "value": "[and(bool('true'), bool('false'))]",
      "type": "bool"
    },
    "orExampleOutput": {
      "value": "[or(bool('true'), bool('false'))]",
      "type": "bool"
    },
    "notExampleOutput": {
      "value": "[not(bool('true'))]",
      "type": "bool"
    }
  }
}
```

# [Bicep](#tab/bicep)

```bicep
output andExampleOutput bool = bool('true') && bool('false')
output orExampleOutput bool = bool('true') || bool('false')
output notExampleOutput bool = !(bool('true'))
```

---

The output from the preceding example is:

| Name | Type | Value |
| ---- | ---- | ----- |
| andExampleOutput | Bool | False |
| orExampleOutput | Bool | True |
| notExampleOutput | Bool | False |

## true

`true()`

Returns true. The `true` function is not available in Bicep.  Use the `true` keyword instead.

### Parameters

The true function doesn't accept any parameters. The `true` function is not available in Bicep.  Use the `true` keyword instead.

### Return value

A boolean that is always true.

### Example

The following example returns a true output value.

# [JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "trueOutput": {
      "type": "bool",
      "value": "[true()]"
    }
  }
}
```

# [Bicep](#tab/bicep)

```bicep
output trueOutput bool = true
```

---

The output from the preceding example is:

| Name | Type | Value |
| ---- | ---- | ----- |
| trueOutput | Bool | True |

## Next steps

* For a description of the sections in an Azure Resource Manager template, see [Understand the structure and syntax of ARM templates](template-syntax.md).
