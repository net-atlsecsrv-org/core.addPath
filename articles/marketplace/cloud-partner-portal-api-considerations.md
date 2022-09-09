---
title: API considerations - Azure Marketplace
description: Versioning, error-handling, and authorization issues when using the marketplace APIs.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
---

# API considerations

API versioning
--------------

> [!NOTE]
> The Cloud Partner Portal APIs are integrated with and will continue working in Partner Center. The transition introduces small changes. Review the changes listed in [Cloud Partner Portal API Reference](./cloud-partner-portal-api-overview.md) to ensure your code continues working after transitioning to Partner Center. CPP APIs should only be used for existing products that were already integrated before transition to Partner Center; new products should use Partner Center submission APIs.

There may be multiple versions of the API that are available at the same
time. Clients must indicate which version they wish to invoke use by
providing the `api-version` parameter as part of the query string.

   `GET https://cloudpartner.azure.com/api/offerTypes?api-version=2017-10-31`

The response to a request with an unknown or invalid API version is an
HTTP code 400. This error returns the collection of known API versions in the response
body.

``` json
    {
        "error": { 
            "code":"InvalidAPIVersion",
            "message":"Invalid api version. Allowed values are [2016-08-01-preview]"
        }
    }
```            

Errors
------

The API responds to errors with the corresponding HTTP status codes and
optionally, additional information in the response serialized as JSON.
When you receive an error, especially a 400-class error, do not retry
the request before fixing the underlying cause. For example, in the
sample response above, fix the API version parameter before resending
the request.

Authorization header
--------------------

For all the APIs in this reference, you must pass the authorization
header along with the bearer token obtained from Azure Active
Directory (Azure AD). This header is required to receive a valid response; if not present, 
a `401 Unauthorized` error is returned. 

``` HTTP
  GET https://cloudpartner.azure.com/api/offerTypes?api-version=2016-08-01-preview

    Accept: application/json 
    Authorization: Bearer <YOUR_TOKEN> 
```
