---
title: Bicep logical operators
description: Describes Bicep logical operators that evaluate conditions.
ms.topic: conceptual
ms.date: 04/07/2021
---

# Bicep logical operators

The logical operators evaluate boolean values, return non-null values, or evaluate a conditional expression. To run the examples, use Azure CLI or Azure PowerShell to [deploy a Bicep file](bicep-tutorial-create-first-bicep.md#deploy-bicep-file).

| Operator | Name |
| ---- | ---- |
| `&&` | [And](#and-) |
| `||` | [Or](#or-) |
| `!` | [Not](#not-) |
| `??` | [Coalesce](#coalesce-) |
| `?` `:` | [Conditional expression](#conditional-expression--) |

## And &&

`operand1 && operand2`

Determines if both values are true.

### Operands

| Operand | Type | Description |
| ---- | ---- | ---- |
| `operand1` | boolean | The first value to check if true. |
| `operand2` | boolean | The second value to check if true. |
| More operands | boolean | More operands can be included. |

### Return value

`True` when both values are true, otherwise `false` is returned.

### Example

Evaluates a set of parameter values and a set of expressions.

```bicep
param operand1 bool = true
param operand2 bool = true

output andResultParm bool = operand1 && operand2
output andResultExp bool = bool(10 >= 10) && bool(5 > 2)
```

Output from the example:

| Name | Type | Value |
| ---- | ---- | ---- |
| `andResultParm` | boolean | true |
| `andResultExp` | boolean | true |

## Or ||

`operand1 || operand2`

Determines if either value is true.

### Operands

| Operand | Type | Description |
| ---- | ---- | ---- |
| `operand1` | boolean | The first value to check if true. |
| `operand2` | boolean | The second value to check if true. |
| More operands | boolean | More operands can be included. |

### Return value

`True` when either value is true, otherwise `false` is returned.

### Example

Evaluates a set of parameter values and a set of expressions.

```bicep
param operand1 bool = true
param operand2 bool = false

output orResultParm bool = operand1 || operand2
output orResultExp bool = bool(10 >= 10) || bool(5 < 2)
```

Output from the example:

| Name | Type | Value |
| ---- | ---- | ---- |
| `orResultParm` | boolean | true |
| `orResultExp` | boolean | true |

## Not !

`!boolValue`

Negates a boolean value.

### Operand

| Operand | Type | Description |
| ---- | ---- | ---- |
| `boolValue` | boolean | Boolean value that's negated. |

### Return value

Negates the initial value and returns a boolean. If the initial value is `true`, then `false` is returned.

### Example

The `not` operator negates a value. The values can be wrapped with parentheses.

```bicep
param initTrue bool = true
param initFalse bool = false

output startedTrue bool = !(initTrue)
output startedFalse bool = !initFalse
```

Output from the example:

| Name | Type | Value |
| ---- | ---- | ---- |
| `startedTrue` | boolean | false |
| `startedFalse` | boolean | true |

## Coalesce ??

`operand1 ?? operand2`

Returns first non-null value from operands.

### Operands

| Operand | Type | Description |
| ---- | ---- | ---- |
| `operand1` | string, integer, boolean, object, array | Value to test for `null`. |
| `operand2` | string, integer, boolean, object, array | Value to test for `null`. |
| More operands | string, integer, boolean, object, array | Value to test for `null`. |

### Return value

Returns the first non-null value. Empty strings, empty arrays, and empty objects aren't `null` and an \<empty> value is returned.

### Example

The output statements return the non-null values. The output type must match the type in the comparison or an error is generated.

```bicep
param myObject object = {
  'isnull1': null
  'isnull2': null
  'string': 'demoString'
  'emptystr': ''
  'integer': 10
  }

output nonNullStr string = myObject.isnull1 ?? myObject.string ?? myObject.isnull2
output nonNullInt int = myObject.isnull1 ?? myObject.integer ?? myObject.isnull2
output nonNullEmpty string = myObject.isnull1 ?? myObject.emptystr ?? myObject.string ?? myObject.isnull2
```

Output from the example:

| Name | Type | Value |
| ---- | ---- | ---- |
| `nonNullStr` | string | demoString |
| `nonNullInt` | int | 10 |
| `nonNullEmpty` | string | \<empty> |

## Conditional expression ? :

`condition ? true-value : false-value`

Evaluates a condition and returns a value whether the condition is true or false.

### Operands

| Operand | Type | Description |
| ---- | ---- | ---- |
| `condition` | boolean | Condition to evaluate as true or false. |
| `true-value` | string, integer, boolean, object, array | Value when condition is true. |
| `false-value` | string, integer, boolean, object, array | Value when condition is false. |

### Example

This example evaluates a parameter's initial and returns a value whether the condition is true or false.

```bicep
param initValue bool = true

output outValue string = initValue ? 'true value' : 'false value'
```

Output from the example:

| Name | Type | Value |
| ---- | ---- | ---- |
| `outValue` | string | true value |

## Next steps

- To create a Bicep file, see [Tutorial: Create and deploy first Azure Resource Manager Bicep file](bicep-tutorial-create-first-bicep.md).
- For information about how to resolve Bicep type errors, see [Any function for Bicep](template-functions-any.md).
- To compare syntax for Bicep and JSON, see [Comparing JSON and Bicep for templates](compare-template-syntax.md).
- For examples of Bicep and ARM template functions, see [ARM template functions](template-functions.md).
