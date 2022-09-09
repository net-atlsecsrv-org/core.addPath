---
title: Tutorial - Use Azure Key Vault with a Windows virtual machine in .NET | Microsoft Docs
description: In this tutorial, you configure an ASP.NET core application to read a secret from your key vault.
services: key-vault
author: msmbaldwin
manager: rajvijan

ms.service: key-vault
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: pryerram
ms.custom: mvc
#Customer intent: As a developer I want to use Azure Key Vault to store secrets for my app, so that they are kept secure.
---
# Tutorial: Use Azure Key Vault with a Windows virtual machine in .NET

Azure Key Vault helps you to protect secrets such as API keys, the database connection strings you need to access your applications, services, and IT resources.

In this tutorial, you learn how to get a console application to read information from Azure Key Vault. To do so, you use managed identities for Azure resources. 

The tutorial shows you how to:

> [!div class="checklist"]
> * Create a resource group.
> * Create a key vault.
> * Add a secret to the key vault.
> * Retrieve a secret from the key vault.
> * Create an Azure virtual machine.
> * Enable a [managed identity](../active-directory/managed-identities-azure-resources/overview.md) for the Virtual Machine.
> * Assign permissions to the VM identity.

Before you begin, read [Key Vault basic concepts](key-vault-whatis.md#basic-concepts). 

If you don’t have an Azure subscription, create a [free account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## Prerequisites

For Windows, Mac, and Linux:
  * [Git](https://git-scm.com/downloads)
  * This tutorial requires that you run the Azure CLI locally. You must have the Azure CLI version 2.0.4 or later installed. Run `az --version` to find the version. If you need to install or upgrade the CLI, see [Install Azure CLI 2.0](https://review.docs.microsoft.com/cli/azure/install-azure-cli).

## About Managed Service Identity

Azure Key Vault stores credentials securely, so they're not displayed in your code. However, you need to authenticate to Azure Key Vault to retrieve your keys. To authenticate to Key Vault, you need a credential. It's a classic bootstrap dilemma. Managed Service Identity (MSI) solves this issue by providing a _bootstrap identity_ that simplifies the process.

When you enable MSI for an Azure service, such as Azure Virtual Machines, Azure App Service, or Azure Functions, Azure creates a [service principal](key-vault-whatis.md#basic-concepts). MSI does this for the instance of the service in Azure Active Directory (Azure AD) and injects the service principal credentials into that instance. 

![MSI](media/MSI.png)

Next, to get an access token, your code calls a local metadata service that's available on the Azure resource. To authenticate to an Azure Key Vault service, your code uses the access token that it gets from the local MSI endpoint. 

## Log in to Azure

To log in to Azure by using the Azure CLI, enter:

```azurecli
az login
```

## Create a resource group

An Azure resource group is a logical container into which Azure resources are deployed and managed.

Create a resource group by using the [az group create](/cli/azure/group#az-group-create) command. 

Then, select a resource group name and fill in the placeholder. The following example creates a resource group in the West US location:

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "West US"
```

You use your newly created resource group throughout this tutorial.

## Create a key vault

To create a key vault in the resource group that you created in the preceding step, provide the following information:

* Key vault name: a string of 3 to 24 characters that can contain only numbers (0-9), letters (a-z, A-Z), and hyphens (-)
* Resource group name
* Location: **West US**

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```
At this point, your Azure account is the only one that's authorized to perform operations on this new key vault.

## Add a secret to the key vault

We're adding a secret to help illustrate how this works. The secret might be a SQL connection string or any other information that you need to keep both secure and available to your application.

To create a secret in the key vault called **AppSecret**, enter the following command:

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

This secret stores the value **MySecret**.

## Create a virtual machine
You can create a virtual machine by using one of the following methods:

* [The Azure CLI](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-cli)
* [PowerShell](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-powershell)
* [The Azure portal](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal)

## Assign an identity to the VM
In this step, you create a system-assigned identity for the virtual machine by running the following command in the Azure CLI:

```azurecli
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

Note the system-assigned identity that's displayed in the following code. The output of the preceding command would be: 

```azurecli
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

## Assign permissions to the VM identity
Now you can assign the previously created identity permissions to your key vault by running the following command:

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## Log on to the virtual machine

To log on to the virtual machine, follow the instructions in [Connect and log on to an Azure virtual machine running Windows](https://docs.microsoft.com/azure/virtual-machines/windows/connect-logon).

## Install .NET Core

To install .NET Core, go to the [.NET downloads](https://www.microsoft.com/net/download) page.

## Create and run a sample .NET app

Open a command prompt.

You can print "Hello World" to the console by running the following commands:

```batch
dotnet new console -o helloworldapp
cd helloworldapp
dotnet run
```

## Edit the console app

Open the *Program.cs* file and add these packages:

```csharp
using System;
using System.IO;
using System.Net;
using System.Text;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
```

Edit the class file to contain the code in the following two-step process:

1. Fetch a token from the local MSI endpoint on the VM. Doing so also fetches a token from Azure AD.
1. Pass the token to your key vault, and then fetch your secret. 

```csharp
 class Program
    {
        static void Main(string[] args)
        {
            // Step 1: Get a token from the local (URI) Managed Service Identity endpoint, which in turn fetches it from Azure AD
            var token = GetToken();

            // Step 2: Fetch the secret value from your key vault
            System.Console.WriteLine(FetchSecretValueFromKeyVault(token));
        }

        static string GetToken()
        {
            WebRequest request = WebRequest.Create("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net");
            request.Headers.Add("Metadata", "true");
            WebResponse response = request.GetResponse();
            return ParseWebResponse(response, "access_token");
        }

        static string FetchSecretValueFromKeyVault(string token)
        {
            WebRequest kvRequest = WebRequest.Create("https://<YourVaultName>.vault.azure.net/secrets/<YourSecretName>?api-version=2016-10-01");
            kvRequest.Headers.Add("Authorization", "Bearer "+  token);
            WebResponse kvResponse = kvRequest.GetResponse();
            return ParseWebResponse(kvResponse, "value");
        }

        private static string ParseWebResponse(WebResponse response, string tokenName)
        {
            string token = String.Empty;
            using (Stream stream = response.GetResponseStream())
            {
                StreamReader reader = new StreamReader(stream, Encoding.UTF8);
                String responseString = reader.ReadToEnd();

                JObject joResponse = JObject.Parse(responseString);    
                JValue ojObject = (JValue)joResponse[tokenName];             
                token = ojObject.Value.ToString();
            }
            return token;
        }
    }
```

The preceding code shows you how to do operations with Azure Key Vault in a Windows virtual machine.

## Clean up resources

When they are no longer needed, delete the virtual machine and your key vault.

## Next steps

> [!div class="nextstepaction"]
> [Azure Key Vault REST API](https://docs.microsoft.com/rest/api/keyvault/)
