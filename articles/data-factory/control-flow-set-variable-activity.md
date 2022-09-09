---
title: Set Variable Activity in Azure Data Factory 
description: Learn how to use the Set Variable activity to set the value of an existing variable defined in a Data Factory pipeline
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services

ms.topic: conceptual
ms.date: 10/10/2018
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
---
# Set Variable Activity in Azure Data Factory

Use the Set Variable activity to set the value of an existing variable of type String, Bool, or Array defined in a Data Factory pipeline.

## Type properties

Property | Description | Required
-------- | ----------- | --------
name | Name of the activity in pipeline | Yes
description | Text describing what the activity does | no
type | Activity Type is SetVariable | yes
value | String literal or expression object value used to set specified variable | yes
variableName | Name of the variable that will be set by this activity | yes


## Next steps
Learn about a related control flow activity supported by Data Factory: 

- [Append Variable Activity](control-flow-append-variable-activity.md)
