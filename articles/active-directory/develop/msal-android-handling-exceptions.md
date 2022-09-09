---
title: Errors and exceptions (MSAL Android)  | Azure
titleSuffix: Microsoft identity platform
description: Learn how to handle errors and exceptions, Conditional Access, and claims challenges in MSAL Android applications.
services: active-directory
author: hamiltonha
manager: CelesteDG

ms.service: active-directory
ms.subservice: develop
ms.topic: troubleshooting
ms.workload: identity
ms.date: 08/07/2020
ms.author: hahamil
ms.reviewer: marsma
---


# Handle exceptions and errors in MSAL for Android

Exceptions in the Microsoft Authentication Library (MSAL) are intended to help app developers troubleshoot their application. Exception messages are not localized.

When processing exceptions and errors, you can use the exception type itself and the error code to distinguish between exceptions.  For a list of error codes, see [Authentication and authorization error codes](reference-aadsts-error-codes.md).

During the sign-in experience, you may encounter errors about consents, Conditional Access (MFA, Device Management, Location-based restrictions), token issuance and redemption, and user properties.


|Error class | Cause/error string| How to handle |
|-----------|------------|----------------|
|`MsalUiRequiredException`| <ul><li>`INVALID_GRANT`: The refresh token used to redeem access token is invalid, expired, or revoked. This exception may be because of a password change. </li><li>`NO_TOKENS_FOUND`: Access token doesn't exist and no refresh token can be found to redeem access token.</li> <li>Step-up required<ul><li>MFA</li><li>Missing claims</li></ul></li><li>Blocked by Conditional Access (for example, [authentication broker](./msal-android-single-sign-on.md) installation required)</li><li>`NO_ACCOUNT_FOUND`: No account available in the cache for silent authentication.</li></ul> |Call `acquireToken()` to prompt the user to enter their username and password, and possibly consent and perform multi factor authentication.|
|`MsalDeclinedScopeException`|<ul><li>`DECLINED_SCOPE`: User or server has not accepted all scopes. The server may decline a scope if the requested scope is not supported, not recognized, or not supported for a particular account. </li></ul>| The developer should decide whether to continue authentication with the granted scopes or end the authentication process. Option to resubmit the acquire token request only for the granted scopes and provide hints for which permissions have been granted by passing `silentParametersForGrantedScopes` and calling `acquireTokenSilent`. |
|`MsalServiceException`|<ul><li>`INVALID_REQUEST`: This request is missing a required parameter, includes an invalid parameter, includes a parameter more than once, or is otherwise malformed. </li><li>`SERVICE_NOT_AVAILABLE`: Represents 500/503/506 error codes due to the service being down. </li><li>`UNAUTHORIZED_REQUEST`: The client is not authorized to request an authorization code.</li><li>`ACCESS_DENIED`: The resource owner or authorization server denied the request.</li><li>`INVALID_INSTANCE`: `AuthorityMetadata` validation failed</li><li>`UNKNOWN_ERROR`: Request to server failed, but no error and `error_description` are returned back from the service.</li><ul>| This exception class represents errors when communicating to the service, can be from the authorize or token endpoints. MSAL reads the error and error_description from the server response. Generally, these errors are resolved by fixing app configurations either in code or in the app registration portal. Rarely a service outage can trigger this warning, which can only be mitigated by waiting for the service to recover.  |
|`MsalClientException`|<ul><li> `MULTIPLE_MATCHING_TOKENS_DETECTED`: There are multiple cache entries found and the sdk cannot identify the correct access or refresh token from the cache. This exception usually indicates a bug in the sdk for storing tokens or that the authority is not provided in the silent request and multiple matching tokens are found. </li><li>`DEVICE_NETWORK_NOT_AVAILABLE`: No active network is available on the device. </li><li>`JSON_PARSE_FAILURE`: The sdk failed to parse the JSON format.</li><li>`IO_ERROR`: `IOException` happened, could be a device or network error. </li><li>`MALFORMED_URL`: The url is malformed. Likely caused when constructing the auth request, authority, or redirect URI. </li><li>`UNSUPPORTED_ENCODING`: The encoding is not supported by the device. </li><li>`NO_SUCH_ALGORITHM`: The algorithm used to generate [PKCE](https://tools.ietf.org/html/rfc7636) challenge is not supported. </li><li>`INVALID_JWT`: `JWT` returned by the server is not valid or is empty or malformed. </li><li>`STATE_MISMATCH`: State from authorization response did not match the state in the authorization request. For authorization requests, the sdk will verify the state returned from redirect and the one sent in the request. </li><li>`UNSUPPORTED_URL`: Unsupported url, cannot perform ADFS authority validation. </li><li> `AUTHORITY_VALIDATION_NOT_SUPPORTED`: The authority is not supported for authority validation. The sdk supports B2C authorities, but doesn't support B2C authority validation. Only well-known host will be supported. </li><li>`CHROME_NOT_INSTALLED`: Chrome is not installed on the device. The sdk uses chrome custom tab for authorization requests if available, and will fall back to chrome browser. </li><li>`USER_MISMATCH`: The user provided in the acquire token request doesn't match the user returned from server.</li></ul>|This exception class represents general errors that are local to the library. These exceptions can be handled by correcting the request.|
|`MsalUserCancelException`|<ul><li>`USER_CANCELED`: The user initiated interactive flow and prior to receiving tokens back they canceled the request. </li></ul>||
|`MsalArgumentException`|<ul><li>`ILLEGAL_ARGUMENT_ERROR_CODE`</li><li>`AUTHORITY_REQUIRED_FOR_SILENT`: Authority must be specified for `acquireTokenSilent`.</li></ul>|These errors can be mitigated by the developer correcting arguments and ensuring activity for interactive auth, completion callback, scopes, and an account with a valid ID have been provided.|


## Catching errors

The following code snippet shows an example of catching errors in the case of silent `acquireToken` calls.

```java
/**
 * Callback used in for silent acquireToken calls.
 */
private SilentAuthenticationCallback getAuthSilentCallback() {
    return new SilentAuthenticationCallback() {

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            Log.d(TAG, "Successfully authenticated");

            /* Successfully got a token, use it to call a protected resource - MSGraph */
            callGraphAPI(authenticationResult);
        }

        @Override
        public void onError(MsalException exception) {
            /* Failed to acquireToken */
            Log.d(TAG, "Authentication failed: " + exception.toString());
            displayError(exception);

            if (exception instanceof MsalClientException) {
                /* Exception inside MSAL, more info inside MsalError.java */
            } else if (exception instanceof MsalServiceException) {
                /* Exception when communicating with the STS, likely config issue */
            } else if (exception instanceof MsalUiRequiredException) {
                /* Tokens expired or no session, retry with interactive */
            }
        }
    };
}
```

## Next steps

Learn more about [logging errors](./msal-logging.md?tabs=android)