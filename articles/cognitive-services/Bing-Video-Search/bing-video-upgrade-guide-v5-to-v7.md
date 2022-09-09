---
title: Upgrade Bing Video Search API v5 to v7
titlesuffix: Azure Cognitive Services
description: Identifies the parts of your application that you need to update to use version 7.
services: cognitive-services
author: swhite-msft
manager: nitinme

ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: scottwhi
---

# Video Search API upgrade guide

This upgrade guide identifies the changes between version 5 and version 7 of the Bing Video Search API. Use this guide to help you identify the parts of your application that you need to update to use version 7.

## Breaking changes

### Endpoints

- The endpoint's version number changed from v5 to v7. For example, `https://api.cognitive.microsoft.com/bing/v7.0/videos/search`.

### Error response objects and error codes

- All failed requests should now include an `ErrorResponse` object in the response body.

- Added the following fields to the `Error` object.  
  - `subCode`&mdash;Partitions the error code into discrete buckets, if possible
  - `moreDetails`&mdash;Additional information about the error described in the `message` field
   

- Replaced the v5 error codes with the following possible `code` and `subCode` values.

|Code|SubCode|Description
|-|-|-
|ServerError|UnexpectedError<br/>ResourceError<br/>NotImplemented|Bing returns ServerError whenever any of the sub-code conditions occur. The response includes these errors if the HTTP status code is 500.
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>Blocked|Bing returns InvalidRequest whenever any part of the request is not valid. For example, a required parameter is missing or a parameter value is not valid.<br/><br/>If the error is ParameterMissing or ParameterInvalidValue, the HTTP status code is 400.<br/><br/>If the error is HttpNotAllowed, the HTTP status code 410.
|RateLimitExceeded||Bing returns RateLimitExceeded whenever you exceed your queries per second (QPS) or queries per month (QPM) quota.<br/><br/>Bing returns HTTP status code 429 if you exceeded QPS and 403 if you exceeded QPM.
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|Bing returns InvalidAuthorization when Bing cannot authenticate the caller. For example, the `Ocp-Apim-Subscription-Key` header is missing or the subscription key is not valid.<br/><br/>Redundancy occurs if you specify more than one authentication method.<br/><br/>If the error is InvalidAuthorization, the HTTP status code is 401.
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|Bing returns InsufficientAuthorization when the caller does not have permissions to access the resource. This can occur if the subscription key has been disabled or has expired. <br/><br/>If the error is InsufficientAuthorization, the HTTP status code is 403.

- The following maps the previous error codes to the new codes. If you've taken a dependency on v5 error codes, update your code accordingly.

|Version 5 code|Version 7 code.subCode
|-|-
|RequestParameterMissing|InvalidRequest.ParameterMissing
RequestParameterInvalidValue|InvalidRequest.ParameterInvalidValue
ResourceAccessDenied|InsufficientAuthorization
ExceededVolume|RateLimitExceeded
ExceededQpsLimit|RateLimitExceeded
Disabled|InsufficientAuthorization.AuthorizationDisabled
UnexpectedError|ServerError.UnexpectedError
DataSourceErrors|ServerError.ResourceError
AuthorizationMissing|InvalidAuthorization.AuthorizationMissing
HttpNotAllowed|InvalidRequest.HttpNotAllowed
UserAgentMissing|InvalidRequest.ParameterMissing
NotImplemented|ServerError.NotImplemented
InvalidAuthorization|InvalidAuthorization
InvalidAuthorizationMethod|InvalidAuthorization
MultipleAuthorizationMethod|InvalidAuthorization.AuthorizationRedundancy
ExpiredAuthorizationToken|InsufficientAuthorization.AuthorizationExpired
InsufficientScope|InsufficientAuthorization
Blocked|InvalidRequest.Blocked

### Query parameters

- Renamed the `modulesRequested` query parameter to [modules](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#modulesrequested).  

### Object changes

- Renamed the `nextOffsetAddCount` field of [Videos](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videos) to `nextOffset`. The way you use the offset has also changed. Previously, you would set the [offset](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#offset) query parameter to the `nextOffset` value plus the previous offset value plus the number of videos in the result. Now, you simply set the `offset` query parameter to the `nextOffset` value.  
  
- Changed the data type of the `relatedVideos` field from `Video[]` to [VideosModule](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videosmodule) (see [VideoDetails](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videodetails)).

