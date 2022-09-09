---
title: DropDown UI element
description: Describes the Microsoft.Common.DropDown UI element for Azure portal. Use to select from available options when deploying a managed application.
author: tfitzmac

ms.topic: conceptual
ms.date: 07/14/2020
ms.author: tomfitz

---

# Microsoft.Common.DropDown UI element

A selection control with a dropdown list. You can allow selection of only a single item or multiple items. You can also optionally include a description with the items.

## UI sample

The DropDown element has different options which determine its appearance in the portal.

When only a single item is allowed for selection, the control appears as:

:::image type="content" source="./media/managed-application-elements/microsoft-common-dropdown-1.png" alt-text="Microsoft.Common.DropDown single selection":::

When descriptions are included, the control appears as:

:::image type="content" source="./media/managed-application-elements/microsoft-common-dropdown-2.png" alt-text="Microsoft.Common.DropDown single selection with descriptions":::

When multi-select is enabled, the control adds a **Select all** option and checkboxes for selecting more than one item:

:::image type="content" source="./media/managed-application-elements/microsoft-common-dropdown-3.png" alt-text="Microsoft.Common.DropDown multi-select":::

Descriptions can be included with multi-select enabled.

:::image type="content" source="./media/managed-application-elements/microsoft-common-dropdown-4.png" alt-text="Screenshot that shows how descriptions can be included with multi-select enabled":::

When filtering is enabled, the control includes a text box for adding the filtering value.

:::image type="content" source="./media/managed-application-elements/microsoft-common-dropdown-5.png" alt-text="Microsoft.Common.DropDown multi-select with descriptions":::

## Schema

```json
{
    "name": "element1",
    "type": "Microsoft.Common.DropDown",
    "label": "Example drop down",
    "placeholder": "",
    "defaultValue": "Value two",
    "toolTip": "",
    "multiselect": true,  
    "selectAll": true,  
    "filter": true,  
    "filterPlaceholder": "Filter items ...",  
    "multiLine": true,  
    "defaultDescription": "A value for selection",  
    "constraints": {
        "allowedValues": [
            {
                "label": "Value one",
                "description": "The value to select for option 1.",
                "value": "one"
            },
            {
                "label": "Value two",
                "description": "The value to select for option 2.",
                "value": "two"
            }
        ],
        "required": true
    },
    "visible": true
}
```

## Sample output

```json
"two"
```

## Remarks

- Use `multiselect` to specify whether users can select more than one item.
- By default, `selectAll` is `true` when multi-select is enabled.
- The `filter` property enables users to search within a long list of options.
- The label for `constraints.allowedValues` is the display text for an item, and its value is the output value of the element when selected.
- If specified, the default value must be a label present in `constraints.allowedValues`. If not specified, the first item in `constraints.allowedValues` is selected. The default value is **null**.
- `constraints.allowedValues` must have at least one item.
- To emulate a value not being required, add an item with a label and value of `""` (empty string) to `constraints.allowedValues`.
- The `defaultDescription` property is used for items that don't have a description.
- The `placeholder` property is help text that disappears when the user begins editing. If the `placeholder` and `defaultValue` are both defined, the `defaultValue` takes precedence and is shown.

## Next steps

* For an introduction to creating UI definitions, see [Getting started with CreateUiDefinition](create-uidefinition-overview.md).
* For a description of common properties in UI elements, see [CreateUiDefinition elements](create-uidefinition-elements.md).
