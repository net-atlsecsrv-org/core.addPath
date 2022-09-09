---
title: Concurrency Control - Azure Marketplace
description: Concurrency control strategies for the Cloud Partner Portal publishing APIs.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
---

# Concurrency Control

> [!NOTE]
> The Cloud Partner Portal APIs are integrated with and will continue working in Partner Center. The transition introduces small changes. Review the changes listed in [Cloud Partner Portal API Reference](./cloud-partner-portal-api-overview.md) to ensure your code continues working after transitioning to Partner Center. CPP APIs should only be used for existing products that were already integrated before transition to Partner Center; new products should use Partner Center submission APIs.

Every call to the Cloud Partner Portal publishing APIs must explicitly
specify which concurrency control strategy to use. Failure to provide
the **If-Match** header will result in an HTTP 400 error response. We
offer two strategies for concurrency control.

-   **Optimistic** - The client performing the update verifies if the
    data has changed since it last read the data.
-   **Last one wins** - The client directly updates the data,
    regardless of whether another application has modified it since the
    last read time.

Optimistic concurrency workflow
-------------------------------

We recommend using the optimistic concurrency strategy, with the
following workflow, to guarantee that no unexpected edits are made to your
resources.

1.  Retrieve an entity using the APIs. The response includes an ETag
    value that identifies the currently stored version of the entity (at
    the time of the response).
2.  At the time of update, include this same ETag value in the mandatory
    **If-Match** request header.
3.  The API compares the ETag value received in the request with the
    current ETag value of the entity in an atomic transaction.
    *   If the ETag values are different, the API returns a `412 Precondition Failed` HTTP 
    response. This error indicates that either another
    process has updated the entity since the client last retrieved it,
    or that the ETag value specified in the request is incorrect.
    *  If the ETag values are the same, or the **If-Match** header contains
    the wildcard asterisk character (`*`), the API performs the requested
    operation. The API operation also updates the stored ETag value of the entity.


> [!NOTE]
> Specifying the wildcard character (*) in the **If-Match** header results in the API using the Last-one-wins concurrency strategy. In this case, the ETag comparison does not occur and the resource is updated without any checks. 
