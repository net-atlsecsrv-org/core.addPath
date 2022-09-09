---
title: Quickstart - Azure Key Vault client library for .NET (v4)
description: Learn how to create, retrieve, and delete secrets from an Azure key vault using the .NET client library (v4)
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/23/2020
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: devx-track-csharp, devx-track-azurecli
---

# Quickstart: Azure Key Vault secret client library for .NET (SDK v4)

Get started with the Azure Key Vault secret client library for .NET. Follow the steps below to install the package and try out example code for basic tasks.

[API reference documentation](/dotnet/api/azure.security.keyvault.secrets?view=azure-dotnet&preserve-view=true) | [Library source code](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault) | [Package (NuGet)](https://www.nuget.org/packages/Azure.Security.KeyVault.Secrets/)

## Prerequisites

* An Azure subscription - [create one for free](https://azure.microsoft.com/free/dotnet)
* [.NET Core 3.1 SDK or later](https://dotnet.microsoft.com/download/dotnet-core)
* [Azure CLI](/cli/azure/install-azure-cli)

This quickstart is using `dotnet` and Azure CLI

## Setup

This quickstart is using Azure Identity library with Azure CLI to authenticate user to Azure Services. Developers can also use Visual Studio or Visual Studio Code to authenticate their calls, for more information, see [Authenticate the client with Azure Identity client library](/dotnet/api/overview/azure/identity-readme#authenticate-the-client&preserve-view=true).

### Sign in to Azure

1. Run the `login` command.

    ```azurecli-interactive
    az login
    ```

    If the CLI can open your default browser, it will do so and load an Azure sign-in page.

    Otherwise, open a browser page at [https://aka.ms/devicelogin](https://aka.ms/devicelogin) and enter the
    authorization code displayed in your terminal.

2. Sign in with your account credentials in the browser.


### Create new .NET console app

1. In a command shell, run the following command to create a project named `key-vault-console-app`:

    ```dotnetcli
    dotnet new console --name key-vault-console-app
    ```

1. Change to the newly created *key-vault-console-app* directory, and run the following command to build the project:

    ```dotnetcli
    dotnet build
    ```

    The build output should contain no warnings or errors.
    
    ```console
    Build succeeded.
     0 Warning(s)
     0 Error(s)
    ```

### Install the packages

From the command shell, install the Azure Key Vault secret client library for .NET:

```dotnetcli
dotnet add package Azure.Security.KeyVault.Secrets
```

For this quickstart, you'll also need to install the Azure SDK client library for Azure Identity:

```dotnetcli
dotnet add package Azure.Identity
```

### Create a resource group and key vault

[!INCLUDE[Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

#### Grant access to your key vault

Create an access policy for your key vault that grants secret permission to your user account

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
```bash
export KEY_VAULT_NAME=<your-key-vault-name>
```

## Object model

The Azure Key Vault secret client library for .NET allows you to manage secrets. The [Code examples](#code-examples) section shows how to create a client, set a secret, retrieve a secret, and delete a secret.

## Code examples

### Add directives

Add the following directives to the top of *Program.cs*:

[!code-csharp[](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=directives)]

### Authenticate and create a client

In this quickstart, logged in user is used to authenticate to key vault, which is preferred method for local development. For applications deployed to Azure, managed identity should be assigned to App Service or Virtual Machine, for more information, see [Managed Identity Overview](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

In below example, the name of your key vault is expanded to the key vault URI, in the format "https://\<your-key-vault-name\>.vault.azure.net". This example is using  ['DefaultAzureCredential()'](/dotnet/api/azure.identity.defaultazurecredential) class, which allows to use the same code across different environments with different options to provide identity. For more information, see [Default Azure Credential Authentication](https://docs.microsoft.com/dotnet/api/overview/azure/identity-readme?#defaultazurecredential). 

[!code-csharp[](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=authenticate)]

### Save a secret

Now that the console app is authenticated, add a secret to the key vault. For this task, use the [SetSecretAsync](/dotnet/api/azure.security.keyvault.secrets.secretclient.setsecretasync) method. The method's first parameter accepts a name for the secret&mdash;"mySecret" in this sample.

```csharp
await client.SetSecretAsync(secretName, secretValue);
``````

You can verify that the secret has been set with the [az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show&preserve-view=true) command:

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

```azurepowershell
(Get-AzKeyVaultSecret -VaultName <your-unique-keyvault-name> -Name mySecret).SecretValue | ConvertFrom-SecureString -AsPlainText
```

### Retrieve a secret

You can now retrieve the previously set value with the [GetSecretAsync](/dotnet/api/azure.security.keyvault.secrets.secretclient.getsecretasync) method.

```csharp
var secret = await client.GetSecretAsync(secretName);
``````

Your secret is now saved as `secret.Value`.

### Delete a secret

Finally, let's delete the secret from your key vault with the [StartDeleteSecretAsync](/dotnet/api/azure.security.keyvault.secrets.secretclient.startdeletesecretasync) method.

```csharp
await client.StartDeleteSecretAsync(secretName);
``````

You can verify that the secret is gone with the [az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show&preserve-view=true) command:

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

```azurepowershell
(Get-AzKeyVaultSecret -VaultName <your-unique-keyvault-name> -Name mySecret).SecretValue | ConvertFrom-SecureString -AsPlainText
```

## Clean up resources

When no longer needed, you can use the Azure CLI or Azure PowerShell to remove your key vault and the corresponding resource group.

### Delete a Key Vault

```azurecli
az keyvault delete --name <your-unique-keyvault-name>
```

```azurepowershell
Remove-AzKeyVault -VaultName <your-unique-keyvault-name>
```

### Purge a Key Vault

```azurecli
az keyvault purge --location eastus --name <your-unique-keyvault-name>
```

```azurepowershell
Remove-AzKeyVault -VaultName <your-unique-keyvault-name> -InRemovedState -Location eastus
```

### Delete a resource group

```azurecli
az group delete -g "myResourceGroup"
```

```azurepowershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## Sample code

Modify the .NET Core console app to interact with the Key Vault by completing the following steps:

1. Replace the code in *Program.cs* with the following code:

    ```csharp
    using System;
    using Azure.Identity;
    using Azure.Security.KeyVault.Secrets;
    
    namespace key_vault_console_app
    {
        class Program
        {
            static async Task Main(string[] args)
            {
                const string secretName = "mySecret";
                var keyVaultName = Environment.GetEnvironmentVariable("KEY_VAULT_NAME");
                var kvUri = $"https://{keyVaultName}.vault.azure.net";
    
                var client = new SecretClient(new Uri(kvUri), new DefaultAzureCredential());
    
                Console.Write("Input the value of your secret > ");
                var secretValue = Console.ReadLine();
    
                Console.Write($"Creating a secret in {keyVaultName} called '{secretName}' with the value '{secretValue}' ...");
                await client.SetSecretAsync(secretName, secretValue);
                Console.WriteLine(" done.");
    
                Console.WriteLine("Forgetting your secret.");
                secretValue = string.Empty;
                Console.WriteLine($"Your secret is '{secretValue}'.");
    
                Console.WriteLine($"Retrieving your secret from {keyVaultName}.");
                var secret = await client.GetSecretAsync(secretName);
                Console.WriteLine($"Your secret is '{secret.Value}'.");
    
                Console.Write($"Deleting your secret from {keyVaultName} ...");
                DeleteSecretOperation operation = await client.StartDeleteSecretAsync(secretName);
                // You only need to wait for completion if you want to purge or recover the secret.
                await operation.WaitForCompletionAsync();

                await client.PurgeDeletedSecret(secretName);
                Console.WriteLine(" done.");
            }
        }
    }
    ```

1. Execute the following command to run the app.

    ```dotnetcli
    dotnet run
    ```

1. When prompted, enter a secret value. For example, mySecretPassword.

    A variation of the following output appears:

    ```console
    Input the value of your secret > mySecretPassword
    Creating a secret in <your-unique-keyvault-name> called 'mySecret' with the value 'mySecretPassword' ... done.
    Forgetting your secret.
    Your secret is ''.
    Retrieving your secret from <your-unique-keyvault-name>.
    Your secret is 'mySecretPassword'.
    Deleting your secret from <your-unique-keyvault-name> ... done.    
    ```

## Next steps

In this quickstart, you created a key vault, stored a secret, and retrieved that secret. See the [entire console app in GitHub](https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/key-vault-console-app).

To learn more about Key Vault and how to integrate it with your apps, see the following articles:

- Read an [Overview of Azure Key Vault](../general/overview.md)
- See an [Access Key Vault from App Service Application Tutorial](../general/tutorial-net-create-vault-azure-web-app.md)
- See an [Access Key Vault from Virtual Machine Tutorial](../general/tutorial-net-virtual-machine.md)
- See the [Azure Key Vault developer's guide](../general/developers-guide.md)
- Review [Azure Key Vault best practices](../general/best-practices.md)