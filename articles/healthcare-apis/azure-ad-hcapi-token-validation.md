---
title: Azure API for FHIR access token validation
description: Walks through token validation and gives tips on how to troubleshoot access issues
services: healthcare-apis
author: caitlinv39
ms.reviewer: mihansen
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: cavoeg
---
# Azure API for FHIR access token validation

How Azure API for FHIR validates the access token will depend on implementation and configuration. In this article, we will walk through the validation steps, which can be helpful when troubleshooting access issues.

## Validate token has no issues with identity provider

The first step in the token validation is to verify that the token was issued by the correct identity provider and that it hasn't been modified. The FHIR server will be configured to use a specific identity provider known as the authority `Authority`. The FHIR server will retrieve information about the identity provider from the `/.well-known/openid-configuration` endpoint. When using Azure AD, the full URL would be:

```
GET https://login.microsoftonline.com/<TENANT-ID>/.well-known/openid-configuration
```

where `<TENANT-ID>` is the specific Azure AD tenant (either a tenant ID or a domain name).

Azure AD will return a document like the one below to the FHIR server.

```json
{
    "authorization_endpoint": "https://login.microsoftonline.com/<TENANT-ID>/oauth2/authorize",
    "token_endpoint": "https://login.microsoftonline.com/<TENANT-ID>/oauth2/token",
    "token_endpoint_auth_methods_supported": [
        "client_secret_post",
        "private_key_jwt",
        "client_secret_basic"
    ],
    "jwks_uri": "https://login.microsoftonline.com/common/discovery/keys",
    "response_modes_supported": [
        "query",
        "fragment",
        "form_post"
    ],
    "subject_types_supported": [
        "pairwise"
    ],
    "id_token_signing_alg_values_supported": [
        "RS256"
    ],
    "http_logout_supported": true,
    "frontchannel_logout_supported": true,
    "end_session_endpoint": "https://login.microsoftonline.com/<TENANT-ID>/oauth2/logout",
    "response_types_supported": [
        "code",
        "id_token",
        "code id_token",
        "token id_token",
        "token"
    ],
    "scopes_supported": [
        "openid"
    ],
    "issuer": "https://sts.windows.net/<TENANT-ID>/",
    "claims_supported": [
        "sub",
        "iss",
        "cloud_instance_name",
        "cloud_instance_host_name",
        "cloud_graph_host_name",
        "msgraph_host",
        "aud",
        "exp",
        "iat",
        "auth_time",
        "acr",
        "amr",
        "nonce",
        "email",
        "given_name",
        "family_name",
        "nickname"
    ],
    "microsoft_multi_refresh_token": true,
    "check_session_iframe": "https://login.microsoftonline.com/<TENANT-ID>/oauth2/checksession",
    "userinfo_endpoint": "https://login.microsoftonline.com/<TENANT-ID>/openid/userinfo",
    "tenant_region_scope": "WW",
    "cloud_instance_name": "microsoftonline.com",
    "cloud_graph_host_name": "graph.windows.net",
    "msgraph_host": "graph.microsoft.com",
    "rbac_url": "https://pas.windows.net"
}
``` 
The important properties for the FHIR server are `jwks_uri`, which tells the server where to fetch the encryption keys needed to validate the token signature and `issuer`, which tells the server what will be in the issuer claim (`iss`) of tokens issued by this server. The FHIR server can use this to validate that it is receiving an authentic token.

## Validate claims of the token

Once the server has verified the authenticity of the token, the FHIR server will then proceed to validate that the client has the required claims to access the token.

When using the Azure API for FHIR, the server will validate:

1. The token has the right `Audience` (`aud` claim).
1. The `oid` claim contains an identity object ID, which is in the list of allowed object IDs.

See details on [finding identity object IDs](find-identity-object-ids.md). 

When using the OSS Microsoft FHIR server for Azure, the server will validate:

1. The token has the right `Audience` (`aud` claim).
1. The token has a role in the `roles` claim, which is allowed access to the FHIR server.

Consult details on how to [define roles on the FHIR server](https://github.com/microsoft/fhir-server/blob/master/docs/Roles.md).

A FHIR server may also validate that an access token has the scopes (in token claim `scp`) to access the part of the FHIR API that a client is trying to access. Currently, the Azure API for FHIR and the FHIR server for Azure do not validate token scopes.

## Next steps
Now that you know how to walk through token validation, you can complete the tutorial to create a JavaScript application and read FHIR data.

>[!div class="nextstepaction"]
>[Web application tutorial](tutorial-web-app-fhir-server.md)