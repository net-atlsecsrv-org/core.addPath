---
title: Azure API Management access restriction policies | Microsoft Docs
description: Learn about the access restriction policies available for use in Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''

ms.assetid: 034febe3-465f-4840-9fc6-c448ef520b0f
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/10/2020
ms.author: apimpm
---

# API Management access restriction policies

This topic provides a reference for the following API Management policies. For information on adding and configuring policies, see [Policies in API Management](https://go.microsoft.com/fwlink/?LinkID=398186).

## <a name="AccessRestrictionPolicies"></a> Access restriction policies

-   [Check HTTP header](api-management-access-restriction-policies.md#CheckHTTPHeader) - Enforces existence and/or value of a HTTP Header.
-   [Limit call rate by subscription](api-management-access-restriction-policies.md#LimitCallRate) - Prevents API usage spikes by limiting call rate, on a per subscription basis.
-   [Limit call rate by key](#LimitCallRateByKey) - Prevents API usage spikes by limiting call rate, on a per key basis.
-   [Restrict caller IPs](api-management-access-restriction-policies.md#RestrictCallerIPs) - Filters (allows/denies) calls from specific IP addresses and/or address ranges.
-   [Set usage quota by subscription](api-management-access-restriction-policies.md#SetUsageQuota) - Allows you to enforce a renewable or lifetime call volume and/or bandwidth quota, on a per subscription basis.
-   [Set usage quota by key](#SetUsageQuotaByKey) - Allows you to enforce a renewable or lifetime call volume and/or bandwidth quota, on a per key basis.
-   [Validate JWT](api-management-access-restriction-policies.md#ValidateJWT) - Enforces existence and validity of a JWT extracted from either a specified HTTP Header or a specified query parameter.

> [!TIP]
> You can use access restriction policies in different scopes for different purposes. For example, you can secure the whole API with AAD authentication by applying the `validate-jwt` policy on the API level or you can apply it on the API operation level and use `claims` for more granular control.

## <a name="CheckHTTPHeader"></a> Check HTTP header

Use the `check-header` policy to enforce that a request has a specified HTTP header. You can optionally check to see if the header has a specific value or check for a range of allowed values. If the check fails, the policy terminates request processing and returns the HTTP status code and error message specified by the policy.

### Policy statement

```xml
<check-header name="header name" failed-check-httpcode="code" failed-check-error-message="message" ignore-case="true">
    <value>Value1</value>
    <value>Value2</value>
</check-header>
```

### Example

```xml
<check-header name="Authorization" failed-check-httpcode="401" failed-check-error-message="Not authorized" ignore-case="false">
    <value>f6dc69a089844cf6b2019bae6d36fac8</value>
</check-header>
```

### Elements

| Name         | Description                                                                                                                                   | Required |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| check-header | Root element.                                                                                                                                 | Yes      |
| value        | Allowed HTTP header value. When multiple value elements are specified, the check is considered a success if any one of the values is a match. | No       |

### Attributes

| Name                       | Description                                                                                                                                                            | Required | Default |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| failed-check-error-message | Error message to return in the HTTP response body if the header doesn't exist or has an invalid value. This message must have any special characters properly escaped. | Yes      | N/A     |
| failed-check-httpcode      | HTTP Status code to return if the header doesn't exist or has an invalid value.                                                                                        | Yes      | N/A     |
| header-name                | The name of the HTTP Header to check.                                                                                                                                  | Yes      | N/A     |
| ignore-case                | Can be set to True or False. If set to True case is ignored when the header value is compared against the set of acceptable values.                                    | Yes      | N/A     |

### Usage

This policy can be used in the following policy [sections](./api-management-howto-policies.md#sections) and [scopes](./api-management-howto-policies.md#scopes).

-   **Policy sections:** inbound, outbound

-   **Policy scopes:** all scopes

## <a name="LimitCallRate"></a> Limit call rate by subscription

The `rate-limit` policy prevents API usage spikes on a per subscription basis by limiting the call rate to a specified number per a specified time period. When this policy is triggered the caller receives a `429 Too Many Requests` response status code.

> [!IMPORTANT]
> This policy can be used only once per policy document.
>
> [Policy expressions](api-management-policy-expressions.md) cannot be used in any of the policy attributes for this policy.

> [!CAUTION]
> Due to the distributed nature of throttling architecture, rate limiting is never completely accurate. The difference between configured and the real number of allowed requests vary based on request volume and rate, backend latency, and other factors.

### Policy statement

```xml
<rate-limit calls="number" renewal-period="seconds">
    <api name="API name" id="API id" calls="number" renewal-period="seconds" />
        <operation name="operation name" id="operation id" calls="number" renewal-period="seconds" />
    </api>
</rate-limit>
```

### Example

```xml
<policies>
    <inbound>
        <base />
        <rate-limit calls="20" renewal-period="90" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```

### Elements

| Name       | Description                                                                                                                                                                                                                                                                                              | Required |
| ---------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| rate-limit | Root element.                                                                                                                                                                                                                                                                                            | Yes      |
| api        | Add one or more of these elements to impose a call rate limit on APIs within the product. Product and API call rate limits are applied independently. API can be referenced either via `name` or `id`. If both attributes are provided, `id` will be used and `name` will be ignored.                    | No       |
| operation  | Add one or more of these elements to impose a call rate limit on operations within an API. Product, API, and operation call rate limits are applied independently. Operation can be referenced either via `name` or `id`. If both attributes are provided, `id` will be used and `name` will be ignored. | No       |

### Attributes

| Name           | Description                                                                                           | Required | Default |
| -------------- | ----------------------------------------------------------------------------------------------------- | -------- | ------- |
| name           | The name of the API for which to apply the rate limit.                                                | Yes      | N/A     |
| calls          | The maximum total number of calls allowed during the time interval specified in the `renewal-period`. | Yes      | N/A     |
| renewal-period | The time period in seconds after which the quota resets.                                              | Yes      | N/A     |

### Usage

This policy can be used in the following policy [sections](./api-management-howto-policies.md#sections) and [scopes](./api-management-howto-policies.md#scopes).

-   **Policy sections:** inbound

-   **Policy scopes:** product, api, operation

## <a name="LimitCallRateByKey"></a> Limit call rate by key

> [!IMPORTANT]
> This feature is unavailable in the **Consumption** tier of API Management.

The `rate-limit-by-key` policy prevents API usage spikes on a per key basis by limiting the call rate to a specified number per a specified time period. The key can have an arbitrary string value and is typically provided using a policy expression. Optional increment condition can be added to specify which requests should be counted towards the limit. When this policy is triggered the caller receives a `429 Too Many Requests` response status code.

For more information and examples of this policy, see [Advanced request throttling with Azure API Management](./api-management-sample-flexible-throttling.md).

> [!CAUTION]
> Due to the distributed nature of throttling architecture, rate limiting is never completely accurate. The difference between configured and the real number of allowed requests vary based on request volume and rate, backend latency, and other factors.

### Policy statement

```xml
<rate-limit-by-key calls="number"
                   renewal-period="seconds"
                   increment-condition="condition"
                   counter-key="key value" />

```

### Example

In the following example, the rate limit is keyed by the caller IP address.

```xml
<policies>
    <inbound>
        <base />
        <rate-limit-by-key  calls="10"
              renewal-period="60"
              increment-condition="@(context.Response.StatusCode == 200)"
              counter-key="@(context.Request.IpAddress)"/>
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```

### Elements

| Name              | Description   | Required |
| ----------------- | ------------- | -------- |
| rate-limit-by-key | Root element. | Yes      |

### Attributes

| Name                | Description                                                                                           | Required | Default |
| ------------------- | ----------------------------------------------------------------------------------------------------- | -------- | ------- |
| calls               | The maximum total number of calls allowed during the time interval specified in the `renewal-period`. | Yes      | N/A     |
| counter-key         | The key to use for the rate limit policy.                                                             | Yes      | N/A     |
| increment-condition | The boolean expression specifying if the request should be counted towards the quota (`true`).        | No       | N/A     |
| renewal-period      | The time period in seconds after which the quota resets.                                              | Yes      | N/A     |

### Usage

This policy can be used in the following policy [sections](./api-management-howto-policies.md#sections) and [scopes](./api-management-howto-policies.md#scopes).

-   **Policy sections:** inbound

-   **Policy scopes:** all scopes

## <a name="RestrictCallerIPs"></a> Restrict caller IPs

The `ip-filter` policy filters (allows/denies) calls from specific IP addresses and/or address ranges.

### Policy statement

```xml
<ip-filter action="allow | forbid">
    <address>address</address>
    <address-range from="address" to="address" />
</ip-filter>
```

### Example

In the following example, the policy only allows requests coming either from the single IP address or range of IP addresses specified

```xml
<ip-filter action="allow">
    <address>13.66.201.169</address>
    <address-range from="13.66.140.128" to="13.66.140.143" />
</ip-filter>
```

### Elements

| Name                                      | Description                                         | Required                                                       |
| ----------------------------------------- | --------------------------------------------------- | -------------------------------------------------------------- |
| ip-filter                                 | Root element.                                       | Yes                                                            |
| address                                   | Specifies a single IP address on which to filter.   | At least one `address` or `address-range` element is required. |
| address-range from="address" to="address" | Specifies a range of IP address on which to filter. | At least one `address` or `address-range` element is required. |

### Attributes

| Name                                      | Description                                                                                 | Required                                           | Default |
| ----------------------------------------- | ------------------------------------------------------------------------------------------- | -------------------------------------------------- | ------- |
| address-range from="address" to="address" | A range of IP addresses to allow or deny access for.                                        | Required when the `address-range` element is used. | N/A     |
| ip-filter action="allow &#124; forbid"    | Specifies whether calls should be allowed or not for the specified IP addresses and ranges. | Yes                                                | N/A     |

### Usage

This policy can be used in the following policy [sections](./api-management-howto-policies.md#sections) and [scopes](./api-management-howto-policies.md#scopes).

-   **Policy sections:** inbound
-   **Policy scopes:** all scopes

## <a name="SetUsageQuota"></a> Set usage quota by subscription

The `quota` policy enforces a renewable or lifetime call volume and/or bandwidth quota, on a per subscription basis.

> [!IMPORTANT]
> This policy can be used only once per policy document.
>
> [Policy expressions](api-management-policy-expressions.md) cannot be used in any of the policy attributes for this policy.

### Policy statement

```xml
<quota calls="number" bandwidth="kilobytes" renewal-period="seconds">
    <api name="API name" id="API id" calls="number" renewal-period="seconds" />
        <operation name="operation name" id="operation id" calls="number" renewal-period="seconds" />
    </api>
</quota>
```

### Example

```xml
<policies>
    <inbound>
        <base />
        <quota calls="10000" bandwidth="40000" renewal-period="3600" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```

### Elements

| Name      | Description                                                                                                                                                                                                                                                                                  | Required |
| --------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| quota     | Root element.                                                                                                                                                                                                                                                                                | Yes      |
| api       | Add one or more of these elements to impose call quota on APIs within the product. Product and API call quotas are applied independently. API can be referenced either via `name` or `id`. If both attributes are provided, `id` will be used and `name` will be ignored.                    | No       |
| operation | Add one or more of these elements to impose call quota on operations within an API. Product, API, and operation call quotas are applied independently. Operation can be referenced either via `name` or `id`. If both attributes are provided, `id` will be used and `name` will be ignored. | No       |

### Attributes

| Name           | Description                                                                                               | Required                                                         | Default |
| -------------- | --------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------- | ------- |
| name           | The name of the API or operation for which the quota applies.                                             | Yes                                                              | N/A     |
| bandwidth      | The maximum total number of kilobytes allowed during the time interval specified in the `renewal-period`. | Either `calls`, `bandwidth`, or both together must be specified. | N/A     |
| calls          | The maximum total number of calls allowed during the time interval specified in the `renewal-period`.     | Either `calls`, `bandwidth`, or both together must be specified. | N/A     |
| renewal-period | The time period in seconds after which the quota resets.                                                  | Yes                                                              | N/A     |

### Usage

This policy can be used in the following policy [sections](./api-management-howto-policies.md#sections) and [scopes](./api-management-howto-policies.md#scopes).

-   **Policy sections:** inbound
-   **Policy scopes:** product

## <a name="SetUsageQuotaByKey"></a> Set usage quota by key

> [!IMPORTANT]
> This feature is unavailable in the **Consumption** tier of API Management.

The `quota-by-key` policy enforces a renewable or lifetime call volume and/or bandwidth quota, on a per key basis. The key can have an arbitrary string value and is typically provided using a policy expression. Optional increment condition can be added to specify which requests should be counted towards the quota. If multiple policies would increment the same key value, it is incremented only once per request. When the call limit is reached, the caller receives a `403 Forbidden` response status code.

For more information and examples of this policy, see [Advanced request throttling with Azure API Management](./api-management-sample-flexible-throttling.md).

### Policy statement

```xml
<quota-by-key calls="number"
              bandwidth="kilobytes"
              renewal-period="seconds"
              increment-condition="condition"
              counter-key="key value" />

```

### Example

In the following example, the quota is keyed by the caller IP address.

```xml
<policies>
    <inbound>
        <base />
        <quota-by-key calls="10000" bandwidth="40000" renewal-period="3600"
                      increment-condition="@(context.Response.StatusCode >= 200 && context.Response.StatusCode < 400)"
                      counter-key="@(context.Request.IpAddress)" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```

### Elements

| Name  | Description   | Required |
| ----- | ------------- | -------- |
| quota | Root element. | Yes      |

### Attributes

| Name                | Description                                                                                               | Required                                                         | Default |
| ------------------- | --------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------- | ------- |
| bandwidth           | The maximum total number of kilobytes allowed during the time interval specified in the `renewal-period`. | Either `calls`, `bandwidth`, or both together must be specified. | N/A     |
| calls               | The maximum total number of calls allowed during the time interval specified in the `renewal-period`.     | Either `calls`, `bandwidth`, or both together must be specified. | N/A     |
| counter-key         | The key to use for the quota policy.                                                                      | Yes                                                              | N/A     |
| increment-condition | The boolean expression specifying if the request should be counted towards the quota (`true`)             | No                                                               | N/A     |
| renewal-period      | The time period in seconds after which the quota resets.                                                  | Yes                                                              | N/A     |

### Usage

This policy can be used in the following policy [sections](./api-management-howto-policies.md#sections) and [scopes](./api-management-howto-policies.md#scopes).

-   **Policy sections:** inbound
-   **Policy scopes:** all scopes

## <a name="ValidateJWT"></a> Validate JWT

The `validate-jwt` policy enforces existence and validity of a JWT extracted from either a specified HTTP Header or a specified query parameter.

> [!IMPORTANT]
> The `validate-jwt` policy requires that the `exp` registered claim is included in the JWT token, unless `require-expiration-time` attribute is specified and set to `false`.
> The `validate-jwt` policy supports HS256 and RS256 signing algorithms. For HS256 the key must be provided inline within the policy in the base64 encoded form. For RS256 the key has to be provide via an Open ID configuration endpoint.
> The `validate-jwt` policy supports tokens encrypted with symmetric keys using the following encryption algorithms A128CBC-HS256, A192CBC-HS384, A256CBC-HS512.

### Policy statement

```xml
<validate-jwt
    header-name="name of http header containing the token (use query-parameter-name attribute if the token is passed in the URL)"
    failed-validation-httpcode="http status code to return on failure"
    failed-validation-error-message="error message to return on failure"
    token-value="expression returning JWT token as a string"
    require-expiration-time="true|false"
    require-scheme="scheme"
    require-signed-tokens="true|false"
    clock-skew="allowed clock skew in seconds"
    output-token-variable-name="name of a variable to receive a JWT object representing successfully validated token">
  <openid-config url="full URL of the configuration endpoint, e.g. https://login.constoso.com/openid-configuration" />
  <issuer-signing-keys>
    <key>base64 encoded signing key</key>
    <!-- if there are multiple keys, then add additional key elements -->
  </issuer-signing-keys>
  <decryption-keys>
    <key>base64 encoded signing key</key>
    <!-- if there are multiple keys, then add additional key elements -->
  </decryption-keys>
  <audiences>
    <audience>audience string</audience>
    <!-- if there are multiple possible audiences, then add additional audience elements -->
  </audiences>
  <issuers>
    <issuer>issuer string</issuer>
    <!-- if there are multiple possible issuers, then add additional issuer elements -->
  </issuers>
  <required-claims>
    <claim name="name of the claim as it appears in the token" match="all|any" separator="separator character in a multi-valued claim">
      <value>claim value as it is expected to appear in the token</value>
      <!-- if there is more than one allowed values, then add additional value elements -->
    </claim>
    <!-- if there are multiple possible allowed values, then add additional value elements -->
  </required-claims>
</validate-jwt>

```

### Examples

#### Simple token validation

```xml
<validate-jwt header-name="Authorization" require-scheme="Bearer">
    <issuer-signing-keys>
        <key>{{jwt-signing-key}}</key>  <!-- signing key specified as a named value -->
    </issuer-signing-keys>
    <audiences>
        <audience>@(context.Request.OriginalUrl.Host)</audience>  <!-- audience is set to API Management host name -->
    </audiences>
    <issuers>
        <issuer>http://contoso.com/</issuer>
    </issuers>
</validate-jwt>
```

#### Azure Active Directory token validation

```xml
<validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
    <openid-config url="https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration" />
    <audiences>
        <audience>25eef6e4-c905-4a07-8eb4-0d08d5df8b3f</audience>
    </audiences>
    <required-claims>
        <claim name="id" match="all">
            <value>insert claim here</value>
        </claim>
    </required-claims>
</validate-jwt>
```

#### Azure Active Directory B2C token validation

```xml
<validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
    <openid-config url="https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/b2c_1_signin/v2.0/.well-known/openid-configuration" />
    <audiences>
        <audience>d313c4e4-de5f-4197-9470-e509a2f0b806</audience>
    </audiences>
    <required-claims>
        <claim name="id" match="all">
            <value>insert claim here</value>
        </claim>
    </required-claims>
</validate-jwt>
```

#### Authorize access to operations based on token claims

This example shows how to use the [Validate JWT](api-management-access-restriction-policies.md#ValidateJWT) policy to authorize access to operations based on token claims value.

```xml
<validate-jwt header-name="Authorization" require-scheme="Bearer" output-token-variable-name="jwt">
    <issuer-signing-keys>
        <key>{{jwt-signing-key}}</key> <!-- signing key is stored in a named value -->
    </issuer-signing-keys>
    <audiences>
        <audience>@(context.Request.OriginalUrl.Host)</audience>
    </audiences>
    <issuers>
        <issuer>contoso.com</issuer>
    </issuers>
    <required-claims>
        <claim name="group" match="any">
            <value>finance</value>
            <value>logistics</value>
        </claim>
    </required-claims>
</validate-jwt>
<choose>
    <when condition="@(context.Request.Method == "POST" && !((Jwt)context.Variables["jwt"]).Claims["group"].Contains("finance"))">
        <return-response>
            <set-status code="403" reason="Forbidden" />
        </return-response>
    </when>
</choose>
```

### Elements

| Element             | Description                                                                                                                                                                                                                                                                                                                                           | Required |
| ------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| validate-jwt        | Root element.                                                                                                                                                                                                                                                                                                                                         | Yes      |
| audiences           | Contains a list of acceptable audience claims that can be present on the token. If multiple audience values are present, then each value is tried until either all are exhausted (in which case validation fails) or until one succeeds. At least one audience must be specified.                                                                     | No       |
| issuer-signing-keys | A list of Base64-encoded security keys used to validate signed tokens. If multiple security keys are present, then each key is tried until either all are exhausted (in which case validation fails) or until one succeeds (useful for token rollover). Key elements have an optional `id` attribute used to match against `kid` claim.               | No       |
| decryption-keys     | A list of Base64-encoded keys used to decrypt the tokens. If multiple security keys are present, then each key is tried until either all keys are exhausted (in which case validation fails) or until a key succeeds. Key elements have an optional `id` attribute used to match against `kid` claim.                                                 | No       |
| issuers             | A list of acceptable principals that issued the token. If multiple issuer values are present, then each value is tried until either all are exhausted (in which case validation fails) or until one succeeds.                                                                                                                                         | No       |
| openid-config       | The element used for specifying a compliant Open ID configuration endpoint from which signing keys and issuer can be obtained.                                                                                                                                                                                                                        | No       |
| required-claims     | Contains a list of claims expected to be present on the token for it to be considered valid. When the `match` attribute is set to `all` every claim value in the policy must be present in the token for validation to succeed. When the `match` attribute is set to `any` at least one claim must be present in the token for validation to succeed. | No       |

### Attributes

| Name                            | Description                                                                                                                                                                                                                                                                                                                                                                                                                                            | Required                                                                         | Default                                                                           |
| ------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | -------------------------------------------------------------------------------- | --------------------------------------------------------------------------------- |
| clock-skew                      | Timespan. Use to specify maximum expected time difference between the system clocks of the token issuer and the API Management instance.                                                                                                                                                                                                                                                                                                               | No                                                                               | 0 seconds                                                                         |
| failed-validation-error-message | Error message to return in the HTTP response body if the JWT does not pass validation. This message must have any special characters properly escaped.                                                                                                                                                                                                                                                                                                 | No                                                                               | Default error message depends on validation issue, for example "JWT not present." |
| failed-validation-httpcode      | HTTP Status code to return if the JWT doesn't pass validation.                                                                                                                                                                                                                                                                                                                                                                                         | No                                                                               | 401                                                                               |
| header-name                     | The name of the HTTP header holding the token.                                                                                                                                                                                                                                                                                                                                                                                                         | One of `header-name`, `query-parameter-name` or `token-value` must be specified. | N/A                                                                               |
| query-parameter-name            | The name of the query parameter holding the token.                                                                                                                                                                                                                                                                                                                                                                                                     | One of `header-name`, `query-parameter-name` or `token-value` must be specified. | N/A                                                                               |
| token-value                     | Expression returning a string containing JWT token                                                                                                                                                                                                                                                                                                                                                                                                     | One of `header-name`, `query-parameter-name` or `token-value` must be specified. | N/A                                                                               |
| id                              | The `id` attribute on the `key` element allows you to specify the string that will be matched against `kid` claim in the token (if present) to find out the appropriate key to use for signature validation.                                                                                                                                                                                                                                           | No                                                                               | N/A                                                                               |
| match                           | The `match` attribute on the `claim` element specifies whether every claim value in the policy must be present in the token for validation to succeed. Possible values are:<br /><br /> - `all` - every claim value in the policy must be present in the token for validation to succeed.<br /><br /> - `any` - at least one claim value must be present in the token for validation to succeed.                                                       | No                                                                               | all                                                                               |
| require-expiration-time         | Boolean. Specifies whether an expiration claim is required in the token.                                                                                                                                                                                                                                                                                                                                                                               | No                                                                               | true                                                                              |
| require-scheme                  | The name of the token scheme, e.g. "Bearer". When this attribute is set, the policy will ensure that specified scheme is present in the Authorization header value.                                                                                                                                                                                                                                                                                    | No                                                                               | N/A                                                                               |
| require-signed-tokens           | Boolean. Specifies whether a token is required to be signed.                                                                                                                                                                                                                                                                                                                                                                                           | No                                                                               | true                                                                              |
| separator                       | String. Specifies a separator (e.g. ",") to be used for extracting a set of values from a multi-valued claim.                                                                                                                                                                                                                                                                                                                                          | No                                                                               | N/A                                                                               |
| url                             | Open ID configuration endpoint URL from where Open ID configuration metadata can be obtained. The response should be according to specs as defined at URL:`https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderMetadata`. For Azure Active Directory use the following URL: `https://login.microsoftonline.com/{tenant-name}/.well-known/openid-configuration` substituting your directory tenant name, e.g. `contoso.onmicrosoft.com`. | Yes                                                                              | N/A                                                                               |
| output-token-variable-name      | String. Name of context variable that will receive token value as an object of type [`Jwt`](api-management-policy-expressions.md) upon successful token validation                                                                                                                                                                                                                                                                                     | No                                                                               | N/A                                                                               |

### Usage

This policy can be used in the following policy [sections](./api-management-howto-policies.md#sections) and [scopes](./api-management-howto-policies.md#scopes).

-   **Policy sections:** inbound
-   **Policy scopes:** all scopes

## Next steps

For more information working with policies, see:

-   [Policies in API Management](api-management-howto-policies.md)
-   [Transform APIs](transform-api.md)
-   [Policy Reference](./api-management-policies.md) for a full list of policy statements and their settings
-   [Policy samples](policy-samples.md)
