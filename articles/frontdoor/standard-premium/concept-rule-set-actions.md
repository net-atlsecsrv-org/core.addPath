---
title: Configure Azure Front Door Standard/Premium rule set actions
description: This article provides a list of the various actions you can do with Azure Front Door rule set. 
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: yuajia
---

# Azure Front Door Standard/Premium Rule Set Actions

> [!Note]
> This documentation is for Azure Front Door Standard/Premium (Preview). Looking for information on Azure Front Door? View [here](../front-door-overview.md).

An Azure Front Door [Rule Set](concept-rule-set.md) consist of rules with a combination of match conditions and actions. This article provides a detailed description of the actions you can use in a Rule Set. The action defines the behavior that gets applied to a request type that a match condition(s) identifies. In an Azure Front Door Rule Set, a rule can contain up to five actions. Server variable is supported on all actions.

> [!IMPORTANT]
> Azure Front Door Standard/Premium (Preview) is currently in public preview.
> This preview version is provided without a service level agreement, and it's not recommended for production workloads. Certain features might not be supported or might have constrained capabilities.
> For more information, see [Supplemental Terms of Use for Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

The following actions are available to use in Azure Front Door rule set.  

## Cache expiration

Use this action to overwrite the time to live (TTL) value of the endpoint for requests that the rules match conditions specify.

### Required fields

The following description applies when selecting these cache behaviors and the rule matches:

Cache behavior |  Description              
---------------|----------------
Bypass cache | The content isn't cached.
Override | The TTL value returned from your origin is overwritten with the value specified in the action. This behavior will only be applied if the response is cacheable. For cache-control response header with values "no-cache", "private", "no-store", the action won't be applicable.
Set if missing | If no TTL value gets returned from your origin, the rule sets the TTL to the value specified in the action. This behavior will only be applied if the response is cacheable. For cache-control response header with values "no-cache", "private", "no-store", the action won't be applicable.

### Additional fields

Days | Hours | Minutes | Seconds
-----|-------|---------|--------
Int | Int | Int | Int 

## Cache key query string

Use this action to modify the cache key based on query strings.

### Required fields

The following description applies when selecting these behaviors and the rule matches:

Behavior | Description
---------|------------
Include | Query strings specified in the parameters get included when the cache key gets generated. 
Cache every unique URL | Each unique URL has its own cache key. 
Exclude | Query strings specified in the parameters get excluded when the cache key gets generated.
Ignore query strings | Query strings aren't considered when the cache key gets generated. 

## Modify request header

Use this action to modify headers that are present in requests sent to your origin.

### Required fields

The following description applies when selecting these actions and the rule matches:

Action | HTTP header name | Value
-------|------------------|------
Append | The header specified in **Header name** gets added to the request with the specified value. If the header is already present, the value is appended to the existing value. | String
Overwrite | The header specified in **Header name** gets added to the request with the specified value. If the header is already present, the specified value overwrites the existing value. | String
Delete | If the header specified in the rule is present, the header gets deleted from the request. | String

## Modify response header

Use this action to modify headers that are present in responses returned to your clients.

### Required fields

The following description applies when selecting these actions and the rule matches:

Action | HTTP Header name | Value
-------|------------------|------
Append | The header specified in **Header name** gets added to the response by using the specified **Value**. If the header is already present, **Value** is appended to the existing value. | String
Overwrite | The header specified in **Header name** gets added to the response by using the specified **Value**. If the header is already present, **Value** overwrites the existing value. | String
Delete | If the header specified in the rule is present, the header gets deleted from the response. | String

## URL redirect

Use this action to redirect clients to a new URL. 

### Required fields

Field | Description 
------|------------
Redirect Type | Select the response type to return to the requestor: Found (302), Moved (301), Temporary redirect (307), and Permanent redirect (308).
Redirect protocol | Match Request, HTTP, HTTPS.
Destination host | Select the host name you want the request to be redirected to. Leave blank to preserve the incoming host.
Destination path | Define the path to use in the redirect. Leave blank to preserve the incoming path.  
Query string | Define the query string used in the redirect. Leave blank to preserve the incoming query string. 
Destination fragment | Define the fragment to use in the redirect. Leave blank to preserve the incoming fragment. 

## URL rewrite

Use this action to rewrite the path of a request that's en route to your origin.

### Required fields

Field | Description 
------|------------
Source pattern | Define the source pattern in the URL path to replace. Currently, source pattern uses a prefix-based match. To match all URL paths, use a forward slash (**/**) as the source pattern value.
Destination | Define the destination path to use in the rewrite. The destination path overwrites the source pattern.
Preserve unmatched path | If set to **Yes**, the remaining path after the source pattern is appended to the new destination path. 

## Server Variable

### Supported Variables

| Variable name | Description                                                  |
| -------------------------- | :----------------------------------------------------------- |
| socket_ip                  | The IP address of the direct connection to Azure Front Door edge. If the client used an HTTP proxy or a load balancer to send the request, the value of SocketIp is the IP address of the proxy or load balancer. |
| client_ip                  | The IP address of the client that made the original request. If there was an X-Forwarded-For header in the request, then the Client IP is picked from the same. |
| client_port                | The IP port of the client that made the request. |
| hostname                      | The host name in the request from client. |
| geo_country                     | Indicates the requester's country/region of origin through its country/region code. |
| http_method                | The method used to make the URL request. For example, GET or POST. |
| http_version               | The request protocol. Usually HTTP/1.0, HTTP/1.1, or HTTP/2.0. |
| query_string               | The list of variable/value pairs that follows the "?" in the requested URL. Example: in the request *http://contoso.com:8080/article.aspx?id=123&title=fabrikam*, query_string value will be *id=123&title=fabrikam* |
| request_scheme             | The request scheme: http or https. |
| request_uri                | The full original request URI (with arguments). Example: in the request *http://contoso.com:8080/article.aspx?id=123&title=fabrikam*, request_uri value will be */article.aspx?id=123&title=fabrikam* |
| server_port                | The port of the server that accepted a request. |
| ssl_protocol    | The protocol of an established TLS connection. |
| url_path                   | Identifies the specific resource in the host that the web client wants to access. This is the part of the request URI without the arguments. Example: in the request *http://contoso.com:8080/article.aspx?id=123&title=fabrikam*, uri_path value will be */article.aspx* |

### Server Variable Format    

**Format:** {variable:offset}, {variable:offset:length}, {variable}

### Supported server variable actions

* Request header
* Response header
* Cache key query string
* URL rewrite
* URL redirect

## Next steps

* Learn more about [Azure Front Door Stanard/Premium Rule Set](concept-rule-set.md).
* Learn more about [Rule Set Match Conditions](concept-rule-set-match-conditions.md).
