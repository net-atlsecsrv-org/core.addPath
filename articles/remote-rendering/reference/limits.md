---
title: Limits
description: Code limitations for SDK features
author: erscorms
ms.author: erscor
ms.date: 02/11/2020
ms.topic: reference
---

# Limits

A number of features have size or count limitations due to internal details of the running system.

## Azure Frontend

* Total AzureFrontend instances per process: 16.
* Total AzureSession instances per AzureFrontend: 16.

## Objects

* Total allowable objects of a single type (Entity, CutPlaneComponent, etc.): 16,777,215.
* Total allowable active cut planes: 8.

## Materials

* Total allowable materials in an asset: 65,535.

## Overall number of polygons

The allowable number of polygons for all loaded models depends on the size of the VM as passed to [the session management REST API](../how-tos/session-rest-api.md#create-a-session):

| VM size | Maximum number of polygons |
|:--------|:------------------|
|standard| 20 million |
|premium| no limit |



