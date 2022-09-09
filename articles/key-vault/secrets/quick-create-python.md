---
title: Quickstart – Azure Key Vault Python client library – manage secrets
description: Learn how to create, retrieve, and delete secrets from an Azure key vault using the Python client library
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/03/2020
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: devx-track-python

---

# Quickstart: Azure Key Vault secret client library for Python

Get started with the Azure Key Vault secret client library for Python. Follow the steps below to install the package and try out example code for basic tasks. By using Key Vault to store secrets, you avoid storing secrets in your code, which increases the security of your app.

[API reference documentation](/python/api/overview/azure/keyvault-secrets-readme) | [Library source code](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-secrets) | [Package (Python Package Index)](https://pypi.org/project/azure-keyvault-secrets/)

## Prerequisites

- An Azure subscription - [create one for free](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Python 2.7+ or 3.5.3+](https://docs.microsoft.com/azure/developer/python/configure-local-development-environment)
- [Azure CLI](/cli/azure/install-azure-cli)

This quickstart assumes you are running [Azure CLI](/cli/azure/install-azure-cli) in a Linux terminal window.


## Set up your local environment
This quickstart is using Azure Identity library with Azure CLI to authenticate user to Azure Services. Developers can also use Visual Studio or Visual Studio Code to authenticate their calls, for more information, see [Authenticate the client with Azure Identity client library](https://docs.microsoft.com/java/api/overview/azure/identity-readme).

### Sign in to Azure

1. Run the `login` command.

    ```azurecli-interactive
    az login
    ```

    If the CLI can open your default browser, it will do so and load an Azure sign-in page.

    Otherwise, open a browser page at [https://aka.ms/devicelogin](https://aka.ms/devicelogin) and enter the
    authorization code displayed in your terminal.

2. Sign in with your account credentials in the browser.

### Install the packages

1. In a terminal or command prompt, create a suitable project folder, and then create and activate a Python virtual environment as described on [Use Python virtual environments](/azure/developer/python/configure-local-development-environment?tabs=cmd#use-python-virtual-environments).

1. Install the Azure Active Directory identity library:

    ```terminal
    pip install azure.identity
    ```


1. Install the Key Vault secrets library:

    ```terminal
    pip install azure-keyvault-secrets
    ```

### Create a resource group and key vault

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-python-qs-rg-kv-creation.md)]

### Grant access to your key vault

Create an access policy for your key vault that grants secret permission to your user account.

```console
az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --secret-permissions delete get list set
```

#### Set environment variables

This application is using key vault name as an environment variable called `KEY_VAULT_NAME`.

Windows
```cmd
set KEY_VAULT_NAME=<your-key-vault-name>
````
Windows PowerShell
```powershell
$Env:KEY_VAULT_NAME=<your-key-vault-name>
```

macOS or Linux
```cmd
export KEY_VAULT_NAME=<your-key-vault-name>
```

## Create the sample code

The Azure Key Vault secret client library for Python allows you to manage secrets. The following code sample demonstrates how to create a client, set a secret, retrieve a secret, and delete a secret.

Create a file named *kv_secrets.py* that contains this code.

```python
import os
import cmd
from azure.keyvault.secrets import SecretClient
from azure.identity import DefaultAzureCredential

keyVaultName = os.environ["KEY_VAULT_NAME"]
KVUri = f"https://{keyVaultName}.vault.azure.net"

credential = DefaultAzureCredential()
client = SecretClient(vault_url=KVUri, credential=credential)

secretName = input("Input a name for your secret > ")
secretValue = input("Input a value for your secret > ")

print(f"Creating a secret in {keyVaultName} called '{secretName}' with the value '{secretValue}' ...")

client.set_secret(secretName, secretValue)

print(" done.")

print(f"Retrieving your secret from {keyVaultName}.")

retrieved_secret = client.get_secret(secretName)

print(f"Your secret is '{retrieved_secret.value}'.")
print(f"Deleting your secret from {keyVaultName} ...")

poller = client.begin_delete_secret(secretName)
deleted_secret = poller.result()

print(" done.")
```

## Run the code

Make sure the code in the previous section is in a file named *kv_secrets.py*. Then run the code with the following command:

```terminal
python kv_secrets.py
```

- If you encounter permissions errors, make sure you ran the [`az keyvault set-policy` command](#grant-access-to-your-key-vault).
- Re-running the code with the same secrete name may produce the error, "(Conflict) Secret <name> is currently in a deleted but recoverable state." Use a different secret name.

## Code details

### Authenticate and create a client

In this quickstart, logged in user is used to authenticate to key vault, which is preferred method for local development. For applications deployed to Azure, managed identity should be assigned to App Service or Virtual Machine, for more information, see [Managed Identity Overview](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

In below example, the name of your key vault is expanded to the key vault URI, in the format "https://\<your-key-vault-name\>.vault.azure.net". This example is using  ['DefaultAzureCredential()'](https://docs.microsoft.com/python/api/azure-identity/azure.identity.defaultazurecredential) class, which allows to use the same code across different environments with different options to provide identity. For more information, see [Default Azure Credential Authentication](https://docs.microsoft.com/python/api/overview/azure/identity-readme). 

```python
credential = DefaultAzureCredential()
client = SecretClient(vault_url=KVUri, credential=credential)
```

### Save a secret

Once you've obtained the client object for the key vault, you can store a secret using the [set_secret](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient?#set-secret-name--value----kwargs-) method: 

```python
client.set_secret(secretName, secretValue)
```

Calling `set_secret` generates a call to the Azure REST API for the key vault.

When handling the request, Azure authenticates the caller's identity (the service principal) using the credential object you provided to the client.

### Retrieve a secret

To read a secret from Key Vault, use the [get_secret](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient?#get-secret-name--version-none----kwargs-) method:

```python
retrieved_secret = client.get_secret(secretName)
 ```

The secret value is contained in `retrieved_secret.value`.

You can also retrieve a secret with the the Azure CLI command [az keyvault secret show](/cli/azure/keyvault/secret?#az-keyvault-secret-show).

### Delete a secret

To delete a secret, use the [begin_delete_secret](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient?#begin-delete-secret-name----kwargs-) method:

```python
poller = client.begin_delete_secret(secretName)
deleted_secret = poller.result()
```

The `begin_delete_secret` method is asynchronous and returns a poller object. Calling the poller's `result` method waits for its completion.

You can verify that the secret had been removed with the Azure CLI command [az keyvault secret show](/cli/azure/keyvault/secret?#az-keyvault-secret-show).

Once deleted, a secret remains in a deleted but recoverable state for a time. If you run the code again, use a different secret name.

## Clean up resources

If you want to also experiment with [certificates](../certificates/quick-create-python.md) and [keys](../keys/quick-create-python.md), you can reuse the Key Vault created in this article.

Otherwise, when you're finished with the resources created in this article, use the following command to delete the resource group and all its contained resources:

```azurecli
az group delete --resource-group KeyVault-PythonQS-rg
```

## Next steps

- [Overview of Azure Key Vault](../general/overview.md)
- [Secure access to a key vault](../general/secure-your-key-vault.md)
- [Azure Key Vault developer's guide](../general/developers-guide.md)
- [Azure Key Vault best practices](../general/best-practices.md)
- [Authenticate with Key Vault](../general/authentication.md)
