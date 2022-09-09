---
title: Expression builder in mapping data flow
description: Build expressions by using Expression Builder in mapping data flows in Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 09/14/2020
---

# Build expressions in mapping data flow

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In mapping data flow, many transformation properties are entered as expressions. These expressions are composed of column values, parameters, functions, operators, and literals that evaluate to a Spark data type at run time. Mapping data flows has a dedicated experience aimed to aid you in building these expressions called the **Expression Builder**. Utilizing  [IntelliSense](https://docs.microsoft.com/visualstudio/ide/using-intellisense) code completion for highlighting, syntax checking, and autocompleting, the expression builder is designed to make building data flows easy. This article explains how to use the expression builder to effectively build your business logic.

![Expression Builder](media/data-flow/expresion-builder.png "Expression Builder")

## Open Expression Builder

There are multiple entry points to opening the expression builder. These are all dependent on the specific context of the data flow transformation. The most common use case is in transformations like [derived column](data-flow-derived-column.md) and [aggregate](data-flow-aggregate.md) where users create or update columns using the data flow expression language. The expression builder can be opened by selecting **Open expression builder** above the list of columns. You can also click on a column context and open the expression builder directly to that expression.

![Open Expression Builder derive](media/data-flow/open-expression-builder-derive.png "Open Expression Builder derive")

In some transformations like [filter](data-flow-filter.md), clicking on a blue expression text box will open the expression builder. 

![Blue expression box](media/data-flow/expressionbox.png "Expression Builder")

When you reference columns in a matching or group-by condition, an expression can extract values from columns. To create an expression, select **Computed column**.

![Computed column option](media/data-flow/computedcolumn.png "Expression Builder")

In cases where an expression or a literal value are valid inputs, select **Add dynamic content** to build an expression that evaluates to a literal value.

![Add dynamic content option](media/data-flow/add-dynamic-content.png "Expression Builder")

## Expression elements

In mapping data flows, expressions can be composed of column values, parameters, functions, local variables, operators, and literals. These expressions must evaluate to a Spark data type such as string, boolean, or integer.

![Expression elements](media/data-flow/expression-elements.png "Expression elements")

### Functions

Mapping data flows has built-in functions and operators that can be used in expressions. For a list of available functions, see the [mapping data flow language reference](data-flow-expression-functions.md).

#### Address array indexes

When dealing with columns or functions that return array types, use brackets ([]) to access a specific element. If the index doesn't exist, the expression evaluates into NULL.

![Expression Builder array](media/data-flow/expression-array.png "Expression Data Preview")

> [!IMPORTANT]
> In mapping data flows, arrays are one-based meaning the first element is referenced by index one. For example, myArray[1] will access the first element of an array called 'myArray'.

### Input schema

If your data flow uses a defined schema in any of its sources, you can reference a column by name in many expressions. If you are utilizing schema drift, you can reference columns explicitly using the `byName()` or `byNames()` functions or match using column patterns.

#### Column names with special characters

When you have column names that include special characters or spaces, surround the name with curly braces to reference them in an expression.

```{[dbo].this_is my complex name$$$}```

### Parameters

Parameters are values that are passed into a data flow at run time from a pipeline. To reference a parameter, either click on the parameter from the **Expression elements** view or reference it with a dollar sign in front of its name. For example, a parameter called parameter1 would be referenced by `$parameter1`. To learn more, see [parameterizing mapping data flows](parameters-data-flow.md).

### Locals

If you are sharing logic across multiple columns or want to compartmentalize your logic, you can create a local within a derived column\. To reference a local, either click on the local from the **Expression elements** view or reference it with a colon in front of its name. For example, a local called local1 would be referenced by `:local1`. Learn more about locals in the [derived column documentation](data-flow-derived-column.md#locals).

## Preview expression results

If [debug mode](concepts-data-flow-debug-mode.md) is switched on, you can interactively use the debug cluster to preview what your expression evaluates to. Select **Refresh** next to data preview to update the results of the data preview. You can see the output of each row given the input columns.

![In-progress preview](media/data-flow/preview-expression.png "Expression Data Preview")

## String interpolation

When creating long strings that use expression elements, use string interpolation to easily build up complex string logic. String interpolation avoids extensive use of string concatenation when parameters are included in query strings. Use double quotation marks to enclose literal string text together with expressions. You can include expression functions, columns, and parameters. To use expression syntax, enclose it in curly braces,

Some examples of string interpolation:

* ```"My favorite movie is {iif(instr(title,', The')>0,"The {split(title,', The')[1]}",title)}"```

* ```"select * from {$tablename} where orderyear > {$year}"```

* ```"Total cost with sales tax is {round(totalcost * 1.08,2)}"```

* ```"{:playerName} is a {:playerRating} player"```

## Commenting expressions

Add comments to your expressions by using single-line and multiline comment syntax.

The following examples are valid comments:

* ```/* This is my comment */```

* ```/* This is a```
* ```multi-line comment */```

If you put a comment at the top of your expression, it appears in the transformation text box to document your transformation expressions.

![Comment in the transformation text box](media/data-flow/comment-expression.png "Comments")

## Regular expressions

Many expression language functions use regular expression syntax. When you use regular expression functions, Expression Builder tries to interpret a backslash (\\) as an escape character sequence. When you use backslashes in your regular expression, either enclose the entire regex in backticks (\`) or use a double backslash.

An example that uses backticks:

```
regex_replace('100 and 200', `(\d+)`, 'digits')
```

An example that uses double slashes:

```
regex_replace('100 and 200', '(\\d+)', 'digits')
```

## Keyboard shortcuts

Below are a list of shortcuts available in the expression builder. Most intellisense shortcuts are available when creating expressions.

* Ctrl+K Ctrl+C: Comment entire line.
* Ctrl+K Ctrl+U: Uncomment.
* F1: Provide editor help commands.
* Alt+Down arrow key: Move down current line.
* Alt+Up arrow key: Move up current line.
* Ctrl+Spacebar: Show context help.

## Commonly used expressions

### Convert to dates or timestamps

To include string literals in your timestamp output, wrap your conversion in ```toString()```.

```toString(toTimestamp('12/31/2016T00:12:00', 'MM/dd/yyyy\'T\'HH:mm:ss'), 'MM/dd /yyyy\'T\'HH:mm:ss')```

To convert milliseconds from epoch to a date or timestamp, use `toTimestamp(<number of milliseconds>)`. If time is coming in seconds, multiply by 1,000.

```toTimestamp(1574127407*1000l)```

The trailing "l" at the end of the previous expression signifies conversion to a long type as inline syntax.

### Find time from epoch or Unix Time

toLong( 
    currentTimestamp() -
    toTimestamp('1970-01-01 00:00:00.000', 'yyyy-MM-dd HH:mm:ss.SSS')
) * 1000l

## Next steps

[Begin building data transformation expressions](data-flow-expression-functions.md)
