---
title: "Azure Batch pool delete start event | Microsoft Docs"
description: Reference for Batch pool delete start event.
services: batch
author: laurenhughes
manager: jeconnoc

ms.assetid: 
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: lahugh
---

# Pool delete start event

 This event is emitted when a pool delete operation has started. Since the pool delete is an asynchronous event, you can expect a pool delete complete event to be emitted once the delete operation completes.

 The following example shows the body of a pool delete start event.

```
{
	"id": "myPool1"
}
```

|Element|Type|Notes|
|-------------|----------|-----------|
|id|String|The id of the pool.|