---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 9/1/2020
ms.author: mikben
---

## Prerequisites

- An Azure account with an active subscription. [Create an account for free](https://azure.microsoft.com/free/dotnet/).
- The latest version [.NET Core client library](https://dotnet.microsoft.com/download/dotnet-core) for your operating system.
- Get the latest version of the [.NET Identity client library](https://docs.microsoft.com/dotnet/api/azure.identity?view=azure-dotnet).
- Get the latest version of the [.NET Management client library](../../concepts/sdk-options.md).

## Installing the client library

First, include the Communication Services Management client library in your C# project:

```csharp
using Azure.ResourceManager.Communication;
```

## Subscription ID

You'll need to know the ID of your Azure subscription. This can be acquired from the portal:

1.  Login into your Azure account
2.  Select Subscriptions in the left sidebar
3.  Select whichever subscription is needed
4.  Click on Overview
5.  Select your Subscription ID

In this quickstart, we'll assume that you've stored the subscription ID in an environment variable called `AZURE_SUBSCRIPTION_ID`.

## Authentication

To communicate with Azure Communication Services, you must first authenticate yourself to Azure. You'll usually do this using a service principal identity.

### Option 1: Managed Identity

If your code is running as a service in Azure, the easiest way to authenticate is to acquire a managed identity from Azure. Learn more about [managed identities](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

[Azure services that support Managed Identities](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities)

[How to use managed identities for App Service and Azure Functions](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet)

#### [System-assigned Managed Identity](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet#add-a-system-assigned-identity)

```csharp
using Azure.Identity;
using Azure.ResourceManager.Communication;
using Azure.ResourceManager.Communication.Models;
using System;
...
var subscriptionId = "AZURE_SUBSCRIPTION_ID";
var acsClient = new CommunicationManagementClient(subscriptionId, new ManagedIdentityCredential());
```

#### [User-assigned Managed Identity](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet#add-a-user-assigned-identity)

ClientId of the managed identity that you created must be passed to the `ManagedIdentityCredential` explicitly.

```csharp
using Azure.Identity;
using Azure.ResourceManager.Communication;
using Azure.ResourceManager.Communication.Models;
using System;
...
var subscriptionId = "AZURE_SUBSCRIPTION_ID";
var managedIdentityCredential = new ManagedIdentityCredential("AZURE_CLIENT_ID");
var acsClient = new CommunicationManagementClient(subscriptionId, managedIdentityCredential);
```

### Option 2: Service Principal

Instead of using a managed identity, you may want to authenticate to Azure using a service principal that you manage yourself. Learn more using documentation on [creating and managing a service principal in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

After you've created your service principal, you'll need to collect the following information about it from the Azure portal:

- **Client ID**
- **Client Secret**
- **Tenant ID**

Store these values in environment variables named `AZURE_CLIENT_ID`, `AZURE_CLIENT_SECRET`, and `AZURE_TENANT_ID` respectively. You can then create a Communication Services management client like this:

```csharp
using Azure.Identity;
using Azure.ResourceManager.Communication;
using Azure.ResourceManager.Communication.Models;
using System;
...
var subscriptionId = Environment.GetEnvironmentVariable("AZURE_SUBSCRIPTION_ID");
var acsClient = new CommunicationManagementClient(subscriptionId, new EnvironmentCredential());
```

### Option 3: User Identity

If you want to call Azure on behalf of an interactive user, rather than using a service identity, you can use the following code to create an Azure Communication Services Management client. This will open a browser window to prompt the user for their MSA or Azure AD credentials.

```csharp
using Azure.Identity;
using Azure.ResourceManager.Communication;
using Azure.ResourceManager.Communication.Models;
using System;
...
var subscriptionId = Environment.GetEnvironmentVariable("AZURE_SUBSCRIPTION_ID");
var communicationServiceClient = new CommunicationManagementClient(subscriptionId, new InteractiveBrowserCredential());
```

## Managing Communication Services Resources

### Interacting with Azure resources

Now that you're authenticated, you can use your management client to make API calls.

For each of the following examples, we'll be assigning our Communication Services resources to an existing resource group.

If you need to create a resource group, you can do so by using the [Azure portal](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-portal) or the [Azure Resource Manager client library](https://github.com/Azure/azure-sdk-for-net/blob/master/doc/mgmt_preview_quickstart.md).

### Create and manage a Communication Services resource

Our instance of the Communication Services Management client library client (``Azure.ResourceManager.Communication.CommunicationManagementClient``) can be used to perform operations on Communication Services resources.

#### Create a Communication Services resource

When creating a Communication Services resource, you'll specify the resource group name and resource name. Note that the `Location` property will always be `global`, and during public preview the `DataLocation` value must be `UnitedStates`.

```csharp
var resourceGroupName = "myResourceGroupName";
var resourceName = "myResource";
var resource = new CommunicationServiceResource { Location = "Global", DataLocation = "UnitedStates"  };
var operation = await acsClient.CommunicationService.StartCreateOrUpdateAsync(resourceGroupName, resourceName, resource);
await operation.WaitForCompletionAsync();
```

#### Update a Communication Services resource

```csharp
...
var resourceGroupName = "myResourceGroupName";
var resourceName = "myResource";
var resource = new CommunicationServiceResource { Location = "Global", DataLocation = "UnitedStates" };
resource.Tags.Add("environment","test");
resource.Tags.Add("department","tech");
// Use existing resource name and new resource object
var operation = await acsClient.CommunicationService.StartCreateOrUpdateAsync(resourceGroupName, resourceName, resource);
await operation.WaitForCompletionAsync();
```

#### List all Communication Services resources

```csharp
var resources = acsClient.CommunicationService.ListBySubscription();
foreach (var resource in resources)
{
    Console.WriteLine(resource.Name);
}
```

#### Delete a Communication Services resource

```csharp
var resourceGroupName = "myResourceGroupName";
var resourceName = "myResource";
await acsClient.CommunicationService.StartDeleteAsync(resourceGroupName, resourceName);
```

## Managing keys and connection strings

Every Communication Services resource has a pair of access keys and corresponding connection strings. These keys can be accessed with the Management client library and then used by other Communication Services client libraries to authenticate themselves to Azure Communication Services.

#### Get access keys for a Communication Services resource

```csharp
var resourceGroupName = "myResourceGroupName";
var resourceName = "myResource";
var keys = await acsClient.CommunicationService.ListKeysAsync(resourceGroupName, resourceName);

Console.WriteLine(keys.Value.PrimaryConnectionString);
Console.WriteLine(keys.Value.SecondaryConnectionString);
```

#### Regenerate an access key for a Communication Services resource

```csharp
var resourceGroupName = "myResourceGroupName";
var resourceName = "myResource";
var keyParams = new RegenerateKeyParameters { KeyType = KeyType.Primary };
var keys = await acsClient.CommunicationService.RegenerateKeyAsync(resourceGroupName, resourceName, keyParams);

Console.WriteLine(keys.Value.PrimaryKey);
```
