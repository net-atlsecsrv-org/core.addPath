---
title: Publish service catalog managed app
description: Shows how to create an Azure managed application that is intended for members of your organization.
author: tfitzmac

ms.topic: tutorial
ms.date: 10/04/2018
ms.author: tomfitz
---
# Create and publish a managed application definition

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

You can create and publish Azure [managed applications](overview.md) that are intended for members of your organization. For example, an IT department can publish managed applications that fulfill organizational standards. These managed applications are available through the service catalog, not the Azure marketplace.

To publish a managed application to your Azure Service Catalog, you must:

* Create a template that defines the resources to deploy with the managed application.
* Define the user interface elements for the portal when deploying the managed application.
* Create a .zip package that contains the required template files.
* Decide which user, group, or application needs access to the resource group in the user's subscription.
* Create the managed application definition that points to the .zip package and requests access for the identity.

For this article, your managed application has only a storage account. It's intended to illustrate the steps of publishing a managed application. For complete examples, see [Sample projects for Azure managed applications](sample-projects.md).

The PowerShell examples in this article require Azure PowerShell 6.2 or later. If needed, [update your version](/powershell/azure/install-Az-ps).

## Create the resource template

Every managed application definition includes a file named **mainTemplate.json**. In it, you define the Azure resources to deploy. The template is no different than a regular Resource Manager template.

Create a file named **mainTemplate.json**. The name is case-sensitive.

Add the following JSON to your file. It defines the parameters for creating a storage account, and specifies the properties for the storage account.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccountNamePrefix": {
            "type": "string"
        },
        "storageAccountType": {
            "type": "string"
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {
        "storageAccountName": "[concat(parameters('storageAccountNamePrefix'), uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageAccountName')]",
            "apiVersion": "2016-01-01",
            "location": "[parameters('location')]",
            "sku": {
                "name": "[parameters('storageAccountType')]"
            },
            "kind": "Storage",
            "properties": {}
        }
    ],
    "outputs": {
        "storageEndpoint": {
            "type": "string",
            "value": "[reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2016-01-01').primaryEndpoints.blob]"
        }
    }
}
```

Save the mainTemplate.json file.

## Defining your create experience using CreateUiDefinition.json

As a publisher, you define your create experience using the **createUiDefinition.json** file which generates the interface for users creating managed applications. You define how users provide input for each parameter using [control elements](create-uidefinition-elements.md) including drop-downs, text boxes, and password boxes.

Create a file named **createUiDefinition.json** (This name is case-sensitive)

Add the following starter JSON to the file and save it.

```json
{
   "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
   "handler": "Microsoft.Azure.CreateUIDef",
   "version": "0.1.2-preview",
   "parameters": {
        "basics": [
            {}
        ],
        "steps": [
            {
                "name": "storageConfig",
                "label": "Storage settings",
                "subLabel": {
                    "preValidation": "Configure the infrastructure settings",
                    "postValidation": "Done"
                },
                "bladeTitle": "Storage settings",
                "elements": [
                    {
                        "name": "storageAccounts",
                        "type": "Microsoft.Storage.MultiStorageAccountCombo",
                        "label": {
                            "prefix": "Storage account name prefix",
                            "type": "Storage account type"
                        },
                        "defaultValue": {
                            "type": "Standard_LRS"
                        },
                        "constraints": {
                            "allowedTypes": [
                                "Premium_LRS",
                                "Standard_LRS",
                                "Standard_GRS"
                            ]
                        }
                    }
                ]
            }
        ],
        "outputs": {
            "storageAccountNamePrefix": "[steps('storageConfig').storageAccounts.prefix]",
            "storageAccountType": "[steps('storageConfig').storageAccounts.type]",
            "location": "[location()]"
        }
    }
}
```

To learn more, see [Get started with CreateUiDefinition](create-uidefinition-overview.md).

## Package the files

Add the two files to a .zip file named app.zip. The two files must be at the root level of the .zip file. If you put them in a folder, you receive an error when creating the managed application definition that states the required files aren't present. 

Upload the package to an accessible location from where it can be consumed. 

```powershell
New-AzResourceGroup -Name storageGroup -Location eastus
$storageAccount = New-AzStorageAccount -ResourceGroupName storageGroup `
  -Name "mystorageaccount" `
  -Location eastus `
  -SkuName Standard_LRS `
  -Kind Storage

$ctx = $storageAccount.Context

New-AzStorageContainer -Name appcontainer -Context $ctx -Permission blob

Set-AzStorageBlobContent -File "D:\myapplications\app.zip" `
  -Container appcontainer `
  -Blob "app.zip" `
  -Context $ctx 
```

## Create the managed application definition

### Create an Azure Active Directory user group or application

The next step is to select a user group or application for managing the resources on behalf of the customer. This user group or application has permissions on the managed resource group according to the role that is assigned. The role can be any built-in Role-Based Access Control (RBAC) role like Owner or Contributor. You also can give an individual user permission to manage the resources, but typically you assign this permission to a user group. To create a new Active Directory user group, see [Create a group and add members in Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

You need the object ID of the user group to use for managing the resources. 

```powershell
$groupID=(Get-AzADGroup -DisplayName mygroup).Id
```

### Get the role definition ID

Next, you need the role definition ID of the RBAC built-in role you want to grant access to the user, user group, or application. Typically, you use the Owner or Contributor or Reader role. The following command shows how to get the role definition ID for the Owner role:

```powershell
$ownerID=(Get-AzRoleDefinition -Name Owner).Id
```

### Create the managed application definition

If you don't already have a resource group for storing your managed application definition, create one now:

```powershell
New-AzResourceGroup -Name appDefinitionGroup -Location westcentralus
```

Now, create the managed application definition resource.

```powershell
$blob = Get-AzStorageBlob -Container appcontainer -Blob app.zip -Context $ctx

New-AzManagedApplicationDefinition `
  -Name "ManagedStorage" `
  -Location "westcentralus" `
  -ResourceGroupName appDefinitionGroup `
  -LockLevel ReadOnly `
  -DisplayName "Managed Storage Account" `
  -Description "Managed Azure Storage Account" `
  -Authorization "${groupID}:$ownerID" `
  -PackageFileUri $blob.ICloudBlob.StorageUri.PrimaryUri.AbsoluteUri
```

## Bring your own storage for the managed application definition
You can choose to store your managed application definition within a storage account provided by you during creation so that it's location and access can be fully managed by you for your regulatory needs.

> [!NOTE]
> Bring your own storage is only supported with ARM Template or REST API deployments of the managed application definition.

### Select your storage account
You must [create a storage account](../../storage/common/storage-account-create.md) to contain your managed application definition for use with Service Catalog.

Copy the storage account's resource ID. It will be used later when deploying the definition.

### Set the role assignment for "Appliance Resource Provider" in your storage account
Before your managed application definition can be deployed to your storage account, you must give contributor permissions to the **Appliance Resource Provider** role so that it can write the definition files to your storage account's container.

1. In the [Azure portal](https://portal.azure.com), navigate to your storage account.
1. Select **Access control (IAM)** to display the access control settings for the storage account. Select the **Role assignments** tab to see the list of role assignments.
1. In the **Add role assignment** window, select the **Contributor** role. 
1. From the **Assign access to** field, select **Azure AD user, group, or service principal**.
1. Under **Select** search for **Appliance Resource Provider** role and select it.
1. Save the role assignment.

### Deploy the managed application definition with an ARM Template 

Use the following ARM Template to deploy your packaged managed application as a new managed application definition in Service Catalog whose definition files are stored and maintained in your own storage account:
   
```json
    {
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        },
        "applicationName": {
            "type": "string",
            "metadata": {
                "description": "Managed Application name"
            }
        },
        "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ],
      "metadata": {
        "description": "Storage Account type"
      }
    },
        "definitionStorageResourceID": {
            "type": "string",
            "metadata": {
                "description": "Storage account resource ID for where you're storing your definition"
            }
        },
        "_artifactsLocation": {
            "type": "string",
            "metadata": {
                "description": "The base URI where artifacts required by this template are located."
            }
        }
    },
    "variables": {
        "lockLevel": "None",
        "description": "Sample Managed application definition",
        "displayName": "Sample Managed application definition",
        "managedApplicationDefinitionName": "[parameters('applicationName')]",
        "packageFileUri": "[parameters('_artifactsLocation')]",
        "defLocation": "[parameters('definitionStorageResourceID')]",
        "managedResourceGroupId": "[concat(subscription().id,'/resourceGroups/', concat(parameters('applicationName'),'_managed'))]",
        "applicationDefinitionResourceId": "[resourceId('Microsoft.Solutions/applicationDefinitions',variables('managedApplicationDefinitionName'))]"
    },
    "resources": [
        {
            "type": "Microsoft.Solutions/applicationDefinitions",
            "apiVersion": "2019-07-01",
            "name": "[variables('managedApplicationDefinitionName')]",
            "location": "[parameters('location')]",
            "properties": {
                "lockLevel": "[variables('lockLevel')]",
                "description": "[variables('description')]",
                "displayName": "[variables('displayName')]",
                "packageFileUri": "[variables('packageFileUri')]",
                "storageAccountId": "[variables('defLocation')]"
            }
        }
    ],
    "outputs": {}
}
```

We have added a new property named **storageAccountId** to your applicationDefintion's properties and provide storage account id you wish to store your definition in as its value:

You can verify that the application definition files are saved in your provided storage account in a container titled **applicationdefinitions**.

> [!NOTE]
> For added security, you can create a managed applications definition store it in an [Azure storage account blob where encryption is enabled](../../storage/common/storage-service-encryption.md). The definition contents are encrypted through the storage account's encryption options. Only users with permissions to the file can see the definition in Service Catalog.

### Make sure users can see your definition

You have access to the managed application definition, but you want to make sure other users in your organization can access it. Grant them at least the Reader role on the definition. They may have inherited this level of access from the subscription or resource group. To check who has access to the definition and add users or groups, see [Use Role-Based Access Control to manage access to your Azure subscription resources](../../role-based-access-control/role-assignments-portal.md).

## Next steps

* To publish your managed application to the Azure Marketplace, see [Azure managed applications in the Marketplace](publish-marketplace-app.md).
* To deploy a managed application instance, see [Deploy service catalog app through Azure portal](deploy-service-catalog-quickstart.md).
