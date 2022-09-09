---
title: Authorize REST API in Azure IoT Central
description: How to authenticate and authorize IoT Central REST API calls
author: dominicbetts
ms.author: dobett
ms.date: 03/24/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central

---

# How to authenticate and authorize IoT Central REST API calls

The IoT Central REST API lets you develop client applications that integrate with IoT Central applications. Use the REST API to work with resources in your IoT Central application such as device templates, devices, jobs, users, and roles.

Every IoT Central REST API call requires an authorization header that IoT Central uses to determine the identity of the caller and the permissions that caller is granted within the application.

This article describes the types of token you can use in the authorization header, and how to get them.

## Token types

To access an IoT Central application using the REST API, you can use an:

- _Azure Active Directory bearer token_. A bearer token is associated with an Azure Active Directory user account. The token grants the caller the same permissions the user has in the IoT Central application.
- IoT Central API token. An API token is associated with a role in your IoT Central application.

To learn more about users and roles in IoT Central, see [Manage users and roles in your IoT Central application](howto-manage-users-roles.md).

## Get a bearer token

To get a bearer token for your Azure Active Directory user account, use the following Azure CLI commands:

```azurecli
az login
az account get-access-token --resource https://apps.azureiotcentral.com
```

> [!IMPORTANT]
> The `az login` command is necessary even if you're using the Cloud Shell.

The JSON output from the previous command looks like the following example:

```json
{
  "accessToken": "eyJ0eX...fNQ",
  "expiresOn": "2021-03-22 11:11:16.072222",
  "subscription": "{your subscription id}",
  "tenant": "{your tenant id}",
  "tokenType": "Bearer"
}
```

The bearer token is valid for approximately one hour, after which you need to create a new one.

## Get an API token

To get an API token, you can use the IoT Central UI or a REST API call.

In the IoT Central UI:

1. Navigate to **Administration > API tokens**.
1. Select **+ Generate token**.
1. Enter a name for the token and select a role.
1. Select **Generate**.
1. IoT Central displays the token that looks like the following example:

    `SharedAccessSignature sr=5782ed70...&sig=dvZZE...&skn=operator-token&se=1647948035850`

    This screen is the only time you can see the API token, if you lose it you need to generate a new one.

An API token is valid for approximately one year. You can generate tokens for both built-in and custom roles in your IoT Central application.

You can delete API tokens in the IoT Central UI if you need to revoke access.

Using the REST API:

1. Use the REST API to retrieve a list of role IDs from your application:

    ```http
    GET https://{your app subdomain}.azureiotcentral.com/api/roles?api-version=1.0
    ```

    The response to this request looks like the following example:

    ```json
    {
      "value": [
        {
          "displayName": "Administrator",
          "id": "ca310b8d-2f4a-44e0-a36e-957c202cd8d4"
        },
        {
          "displayName": "Operator",
          "id": "ae2c9854-393b-4f97-8c42-479d70ce626e"
        },
        {
          "displayName": "Builder",
          "id": "344138e9-8de4-4497-8c54-5237e96d6aaf"
        }
      ]
    }
    ```

1. Use the REST API to create an API token for a role. For example, to create an API token called `operator-token` for the operator role:

    ```http
    PUT https://{your app subdomain}.azureiotcentral.com/api/roles/operator-token?api-version=1.0
    ```

    Request body:

    ```json
    {
      "roles": [
        {
          "role": "ae2c9854-393b-4f97-8c42-479d70ce626e"
        }
      ]
    }
    ```

    The response to the previous command looks like the following JSON:

    ```json
    {
      "expiry": "2022-03-22T12:01:27.889Z",
      "id": "operator-token",
      "roles": [
        {
          "role": "ae2c9854-393b-4f97-8c42-479d70ce626e"
        }
      ],
      "token": "SharedAccessSignature sr=e8a...&sig=jKY8W...&skn=operator-token&se=1647950487889"
    }
    ```

    This response is the only time you have access to the API token, if you lose it you need to generate a new one.

You can use the REST API to list and delete API tokens in an application.

## Use a bearer token

To use a bearer token when you make a REST API call, your authorization header looks like the following example:

`Authorization: Bearer eyJ0eX...fNQ`

## Use an API token

To use an API token when you make a REST API call, your authorization header looks like the following example:

`Authorization: SharedAccessSignature sr=e8a...&sig=jKY8W...&skn=operator-token&se=1647950487889`

## Next steps

Now that you've learned how to authorize REST API calls, a suggested next step is to [How to use the IoT Central REST API to manage users and roles](howto-manage-users-roles-with-rest-api.md).
