---
title: Configure Azure Image Builder Service permissions using Azure CLI
description: Configure requirements for Azure VM Image Builder Service including permissions and privileges using Azure CLI
author: cynthn
ms.author: danis
ms.date: 05/06/2020
ms.topic: article
ms.service: virtual-machines
ms.subservice: imaging
---

# Configure Azure Image Builder Service permissions using Azure CLI

Azure Image Builder Service requires configuration of permissions and privileges prior to building an image. The following sections detail how to configure possible scenarios using Azure CLI.

> [!IMPORTANT]
> Azure Image Builder is currently in public preview.
> This preview version is provided without a service level agreement, and it's not recommended for production workloads. Certain features might not be supported or might have constrained capabilities. 
> For more information, see [Supplemental Terms of Use for Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## Register the features

First, you must register for the Azure Image Builder Service. Registration grants the service permission to create, manage, and delete a staging resource group. The service also has rights to add resources the group that are required for the image build.

```azurecli-interactive
az feature register --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview
```

## Create an Azure user-assigned managed identity

Azure Image Builder requires you to create an [Azure user-assigned managed identity](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md). The Azure Image Builder uses the user-assigned managed identity to read images, write images, and access Azure storage accounts. You grant the identity permission to do specific actions in your subscription.

> [!NOTE]
> Previously, Azure Image Builder used the Azure Image Builder service principal name (SPN) to grant permissions to the image resource groups. Using the SPN will be deprecated. Use a user-assigned managed identity instead.

The following example shows you how to create an Azure user-assigned managed identity. Replace the placeholder settings to set your variables.

| Setting | Description |
|---------|-------------|
| \<Resource group\> | Resource group where to create the user-assigned managed identity. |

```azurecli-interactive
identityName="aibIdentity"
imageResourceGroup=<Resource group>

az identity create \
    --resource-group $imageResourceGroup \
    --name $identityName
```

For more information about Azure user-assigned identities, see the [Azure user-assigned managed identity](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md) documentation on how to create an identity.

## Allow Image Builder to distribute images

For Azure Image Builder to distribute images (Managed Images / Shared Image Gallery), the Azure Image Builder service must be allowed to inject the images into these resource groups. To grant the required permissions, you need to create a user-assigned managed identity and grant it rights on the resource group where the image is built. Azure Image Builder **does not** have permission to access resources in other resource groups in the subscription. You need to take explicit actions to allow access to avoid your builds from failing.

You don't need to grant the user-assigned managed identity contributor rights on the resource group to distribute images. However, the user-assigned managed identity needs the following Azure `Actions` permissions in the distribution resource group:

```Actions
Microsoft.Compute/images/write
Microsoft.Compute/images/read
Microsoft.Compute/images/delete
```

If distributing to a shared image gallery, you also need:

```Actions
Microsoft.Compute/galleries/read
Microsoft.Compute/galleries/images/read
Microsoft.Compute/galleries/images/versions/read
Microsoft.Compute/galleries/images/versions/write
```

## Permission to customize existing images

For Azure Image Builder to build images from source custom images (Managed Images / Shared Image Gallery), the Azure Image Builder service must be allowed to read the images into these resource groups. To grant the required permissions, you need to create a user-assigned managed identity and grant it rights on the resource group where the image is located.

Build from an existing custom image:

```Actions
Microsoft.Compute/galleries/read
```

Build from an existing Shared Image Gallery version:

```Actions
Microsoft.Compute/galleries/read
Microsoft.Compute/galleries/images/read
Microsoft.Compute/galleries/images/versions/read
```

## Permission to customize images on your VNETs

Azure Image Builder has the capability to deploy and use an existing VNET in your subscription, thus allowing customizations access to connected resources.

You don't need to grant the user-assigned managed identity contributor rights on the resource group to deploy a VM to an existing VNET. However, the user-assigned managed identity needs the following Azure `Actions` permissions on the VNET resource group:

```Actions
Microsoft.Network/virtualNetworks/read
Microsoft.Network/virtualNetworks/subnets/join/action
```

## Create an Azure role definition

The following examples create an Azure role definition from the actions described in the previous sections. The examples are applied at the resource group level. Evaluate and test if the examples are granular enough for your requirements. For your scenario, you may need to refine it to a specific shared image gallery.

The image actions allow read and write. Decide what is appropriate for your environment. For example, create a role to allow Azure Image Builder to read images from resource group *example-rg-1* and write images to resource group *example-rg-2*.

### Custom image Azure role example

The following example creates an Azure role to use and distribute a source custom image. You then grant the custom role to the user-assigned managed identity for Azure Image Builder.

To simplify the replacement of values in the example, set the following variables first. Replace the placeholder settings to set your variables.

| Setting | Description |
|---------|-------------|
| \<Subscription ID\> | Your Azure subscription ID |
| \<Resource group\> | Resource group for custom image |

```azurecli-interactive
# Subscription ID - You can get this using `az account show | grep id` or from the Azure portal.
subscriptionID=<Subscription ID>
# Resource group - For Preview, image builder will only support creating custom images in the same Resource Group as the source managed image.
imageResourceGroup=<Resource group>
identityName="aibIdentity"

# Use *cURL* to download the a sample JSON description 
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json -o aibRoleImageCreation.json

# Create a unique role name to avoid clashes in the same Azure Active Directory domain
imageRoleDefName="Azure Image Builder Image Def"$(date +'%s')

# Update the JSON definition using stream editor
sed -i -e "s/<subscriptionID>/$subscriptionID/g" aibRoleImageCreation.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" aibRoleImageCreation.json
sed -i -e "s/Azure Image Builder Service Image Creation Role/$imageRoleDefName/g" aibRoleImageCreation.json

# Create a custom role from the sample aibRoleImageCreation.json description file.
az role definition create --role-definition ./aibRoleImageCreation.json

# Get the user-assigned managed identity id
imgBuilderCliId=$(az identity show -g $imageResourceGroup -n $identityName | grep "clientId" | cut -c16- | tr -d '",')

# Grant the custom role to the user-assigned managed identity for Azure Image Builder.
az role assignment create \
    --assignee $imgBuilderCliId \
    --role $imageRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```

### Existing VNET Azure role example

The following example creates an Azure role to use and distribute an existing VNET image. You then grant the custom role to the user-assigned managed identity for Azure Image Builder.

To simplify the replacement of values in the example, set the following variables first. Replace the placeholder settings to set your variables.

| Setting | Description |
|---------|-------------|
| \<Subscription ID\> | Your Azure subscription ID |
| \<Resource group\> | VNET resource group |

```azurecli-interactive
# Subscription ID - You can get this using `az account show | grep id` or from the Azure portal.
subscriptionID=<Subscription ID>
VnetResourceGroup=<Resource group>
identityName="aibIdentity"

# Use *cURL* to download the a sample JSON description 
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleNetworking.json -o aibRoleNetworking.json

# Create a unique role name to avoid clashes in the same domain
netRoleDefName="Azure Image Builder Network Def"$(date +'%s')

# Update the JSON definition using stream editor
sed -i -e "s/<subscriptionID>/$subscriptionID/g" aibRoleNetworking.json
sed -i -e "s/<vnetRgName>/$vnetRgName/g" aibRoleNetworking.json
sed -i -e "s/Azure Image Builder Service Networking Role/$netRoleDefName/g" aibRoleNetworking.json

# Create a custom role from the aibRoleNetworking.json description file.
az role definition create --role-definition ./aibRoleNetworking.json

# Get the user-assigned managed identity id
imgBuilderCliId=$(az identity show -g $imageResourceGroup -n $identityName | grep "clientId" | cut -c16- | tr -d '",')

# Grant the custom role to the user-assigned managed identity for Azure Image Builder.
az role assignment create \
    --assignee $imgBuilderCliId \
    --role $netRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$VnetResourceGroup
```

## Using managed identity for Azure Storage access

If you want to seemlessly authenticate with Azure Storage and use private containers, Azure Image Builder an Azure needs a user-assigned managed identity. Azure Image Builder uses the identity to authenticate with Azure Storage.

> [!NOTE]
> Azure Image Builder only uses the identity at image template submission time. The build VM does not have access to the identity during image build.

Use Azure CLI to create the user-assigned managed identity.

```azurecli
az role assignment create \
    --assignee <Image Builder client ID> \
    --role "Storage Blob Data Reader" \
    --scope /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Storage/storageAccounts/$scriptStorageAcc/blobServices/default/containers/<Storage account container>
```

In the Image Builder template, you need to provide the user-assigned managed identity.

```json
    "type": "Microsoft.VirtualMachineImages/imageTemplates",
    "apiVersion": "2019-05-01-preview",
    "location": "<Region>",
    ..
    "identity": {
    "type": "UserAssigned",
          "userAssignedIdentities": {
            "<Image Builder ID>": {}     
        }
```

Replace the following placeholder settings:

| Setting | Description |
|---------|-------------|
| \<Region\> | Template region |
| \<Resource group\> | Resource group |
| \<Storage account container\> | Storage account container name |
| \<Subscription ID\> | Azure subscription |

For more information using a user-assigned managed identity, see the [Create a Custom Image that will use an Azure User-Assigned Managed Identity to seemlessly access files Azure Storage](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage#create-a-custom-image-that-will-use-an-azure-user-assigned-managed-identity-to-seemlessly-access-files-azure-storage). The quickstart walks through how to create and configure the user-assigned managed identity to access a storage account.

## Next steps

For more information, see [Azure Image Builder overview](image-builder-overview.md).