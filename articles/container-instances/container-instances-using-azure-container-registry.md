---
title: Deploy to Azure Container Instances from Azure Container Registry
description: Learn how to deploy containers in Azure Container Instances using container images in an Azure container registry.
services: container-instances
author: dlepow
manager: gwallace

ms.service: container-instances
ms.topic: article
ms.date: 01/04/2019
ms.author: danlep
ms.custom: mvc
---

# Deploy to Azure Container Instances from Azure Container Registry

[Azure Container Registry](../container-registry/container-registry-intro.md) is an Azure-based, managed container registry service used to store private Docker container images. This article describes how to deploy container images stored in an Azure container registry to Azure Container Instances.

## Prerequisites

**Azure container registry**: You need an Azure container registry--and at least one container image in the registry--to complete the steps in this article. If you need a registry, see [Create a container registry using the Azure CLI](../container-registry/container-registry-get-started-azure-cli.md).

**Azure CLI**: The command-line examples in this article use the [Azure CLI](/cli/azure/) and are formatted for the Bash shell. You can [install the Azure CLI](/cli/azure/install-azure-cli) locally, or use the [Azure Cloud Shell][cloud-shell-bash].

## Configure registry authentication

In any production scenario, access to an Azure container registry should be provided by using [service principals](../container-registry/container-registry-auth-service-principal.md). Service principals allow you to provide [role-based access control](../container-registry/container-registry-roles.md) to your container images. For example, you can configure a service principal with pull-only access to a registry.

In the following section, you create an Azure key vault and a service principal, and store the service principal's credentials in the vault. 

### Create key vault

If you don't already have a vault in [Azure Key Vault](../key-vault/key-vault-overview.md), create one with the Azure CLI using the following commands.

Update the `RES_GROUP` variable with the name of an existing resource group in which to create the key vault, and `ACR_NAME` with the name of your container registry. Specify a name for your new key vault in `AKV_NAME`. The vault name must be unique within Azure and must be 3-24 alphanumeric characters in length, begin with a letter, end with a letter or digit, and cannot contain consecutive hyphens.

```azurecli
RES_GROUP=myresourcegroup # Resource Group name
ACR_NAME=myregistry       # Azure Container Registry registry name
AKV_NAME=mykeyvault       # Azure Key Vault vault name

az keyvault create -g $RES_GROUP -n $AKV_NAME
```

### Create service principal and store credentials

You now need to create a service principal and store its credentials in your key vault.

The following command uses [az ad sp create-for-rbac][az-ad-sp-create-for-rbac] to create the service principal, and [az keyvault secret set][az-keyvault-secret-set] to store the service principal's **password** in the vault.

```azurecli
# Create service principal, store its password in AKV (the registry *password*)
az keyvault secret set \
  --vault-name $AKV_NAME \
  --name $ACR_NAME-pull-pwd \
  --value $(az ad sp create-for-rbac \
                --name http://$ACR_NAME-pull \
                --scopes $(az acr show --name $ACR_NAME --query id --output tsv) \
                --role acrpull \
                --query password \
                --output tsv)
```

The `--role` argument in the preceding command configures the service principal with the *acrpull* role, which grants it pull-only access to the registry. To grant both push and pull access, change the `--role` argument to *acrpush*.

Next, store the service principal's *appId* in the vault, which is the **username** you pass to Azure Container Registry for authentication.

```azurecli
# Store service principal ID in AKV (the registry *username*)
az keyvault secret set \
    --vault-name $AKV_NAME \
    --name $ACR_NAME-pull-usr \
    --value $(az ad sp show --id http://$ACR_NAME-pull --query appId --output tsv)
```

You've created an Azure Key Vault and stored two secrets in it:

* `$ACR_NAME-pull-usr`: The service principal ID, for use as the container registry **username**.
* `$ACR_NAME-pull-pwd`: The service principal password, for use as the container registry **password**.

You can now reference these secrets by name when you or your applications and services pull images from the registry.

## Deploy container with Azure CLI

Now that the service principal credentials are stored in Azure Key Vault secrets, your applications and services can use them to access your private registry.

First get the registry's login server name by using the [az acr show][az-acr-show] command. The login server name is all lowercase and similar to `myregistry.azurecr.io`.

```azurecli
ACR_LOGIN_SERVER=$(az acr show --name $ACR_NAME --resource-group $RES_GROUP --query "loginServer" --output tsv)
```

Execute the following [az container create][az-container-create] command to deploy a container instance. The command uses the service principal's credentials stored in Azure Key Vault to authenticate to your container registry, and assumes you've previously pushed the [aci-helloworld](container-instances-quickstart.md) image to your registry. Update the `--image` value if you'd like to use a different image from your registry.

```azurecli
az container create \
    --name aci-demo \
    --resource-group $RES_GROUP \
    --image $ACR_LOGIN_SERVER/aci-helloworld:v1 \
    --registry-login-server $ACR_LOGIN_SERVER \
    --registry-username $(az keyvault secret show --vault-name $AKV_NAME -n $ACR_NAME-pull-usr --query value -o tsv) \
    --registry-password $(az keyvault secret show --vault-name $AKV_NAME -n $ACR_NAME-pull-pwd --query value -o tsv) \
    --dns-name-label aci-demo-$RANDOM \
    --query ipAddress.fqdn
```

The `--dns-name-label` value must be unique within Azure, so the preceding command appends a random number to the container's DNS name label. The output from the command displays the container's fully qualified domain name (FQDN), for example:

```console
$ az container create --name aci-demo --resource-group $RES_GROUP --image $ACR_LOGIN_SERVER/aci-helloworld:v1 --registry-login-server $ACR_LOGIN_SERVER --registry-username $(az keyvault secret show --vault-name $AKV_NAME -n $ACR_NAME-pull-usr --query value -o tsv) --registry-password $(az keyvault secret show --vault-name $AKV_NAME -n $ACR_NAME-pull-pwd --query value -o tsv) --dns-name-label aci-demo-$RANDOM --query ipAddress.fqdn
"aci-demo-25007.eastus.azurecontainer.io"
```

Once the container has started successfully, you can navigate to its FQDN in your browser to verify the application is running successfully.

## Deploy with Azure Resource Manager template

You can specify the properties of your Azure Container Registry in an Azure Resource Manager template by including the `imageRegistryCredentials` property in the container group definition:

```JSON
"imageRegistryCredentials": [
  {
    "server": "imageRegistryLoginServer",
    "username": "imageRegistryUsername",
    "password": "imageRegistryPassword"
  }
]
```

For details on referencing Azure Key Vault secrets in a Resource Manager template, see [Use Azure Key Vault to pass secure parameter value during deployment](../azure-resource-manager/resource-manager-keyvault-parameter.md).

## Deploy with Azure portal

If you maintain container images in an Azure container registry, you can easily create a container in Azure Container Instances using the Azure portal. When using the portal to deploy a container instance from a container registry, you must enable the registry's [admin account](../container-registry/container-registry-authentication.md#admin-account). The admin account is designed for a single user to access the registry, mainly for testing purposes. 

1. In the Azure portal, navigate to your container registry.

1. To confirm that the admin account is enabled, select **Access keys**, and under **Admin user** select **Enable**.

1. Select **Repositories**, then select the repository that you want to deploy from, right-click the tag for the container image you want to deploy, and select **Run instance**.

    !["Run instance" in Azure Container Registry in the Azure portal][acr-runinstance-contextmenu]

1. Enter a name for the container and a name for the resource group. You can also change the default values if you wish.

    ![Create menu for Azure Container Instances][acr-create-deeplink]

1. Once the deployment completes, you can navigate to the container group from the notifications pane to find its IP address and other properties.

    ![Details view for Azure Container Instances container group][aci-detailsview]

## Next steps

For more information about Azure Container Registry authentication, see [Authenticate with an Azure container registry](../container-registry/container-registry-authentication.md).

<!-- IMAGES -->
[acr-create-deeplink]: ./media/container-instances-using-azure-container-registry/acr-create-deeplink.png
[aci-detailsview]: ./media/container-instances-using-azure-container-registry/aci-detailsview.png
[acr-runinstance-contextmenu]: ./media/container-instances-using-azure-container-registry/acr-runinstance-contextmenu.png

<!-- LINKS - External -->
[cloud-shell-bash]: https://shell.azure.com/bash
[cloud-shell-try-it]: https://shell.azure.com/powershell

<!-- LINKS - Internal -->
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-container-create]: /cli/azure/container#az-container-create
[az-keyvault-secret-set]: /cli/azure/keyvault/secret#az-keyvault-secret-set
