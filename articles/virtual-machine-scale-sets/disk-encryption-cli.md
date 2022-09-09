---
title: Encrypt disks for Azure scale sets with Azure CLI
description: Learn how to use Azure CLI to encrypt VM instances and attached disks in a Windows virtual machine scale set
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 10/15/2019
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurecli

---
# Encrypt OS and attached data disks in a virtual machine scale set with the Azure CLI

The Azure CLI is used to create and manage Azure resources from the command line or in scripts. This quickstart shows you how to use the Azure CLI to create and encrypt a virtual machine scale set. For more information on applying Azure Disk encryption to a virtual machine scale set, see [Azure Disk Encryption for Virtual Machine Scale Sets](disk-encryption-overview.md).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- This article requires version 2.0.31 or later of the Azure CLI. If using Azure Cloud Shell, the latest version is already installed.

## Create a scale set

Before you can create a scale set, create a resource group with [az group create](/cli/azure/group). The following example creates a resource group named *myResourceGroup* in the *eastus* location:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Now create a virtual machine scale set with [az vmss create](/cli/azure/vmss). The following example creates a scale set named *myScaleSet* that is set to automatically update as changes are applied, and generates SSH keys if they do not exist in *~/.ssh/id_rsa*. A 32Gb data disk is attached to each VM instance, and the Azure [Custom Script Extension](../virtual-machines/extensions/custom-script-linux.md) is used to prepare the data disks with [az vmss extension set](/cli/azure/vmss/extension):

```azurecli-interactive
# Create a scale set with attached data disk
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --admin-username azureuser \
  --generate-ssh-keys \
  --data-disk-sizes-gb 32

# Prepare the data disk for use with the Custom Script Extension
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroup \
  --vmss-name myScaleSet \
  --settings '{"fileUris":["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/prepare_vm_disks.sh"],"commandToExecute":"./prepare_vm_disks.sh"}'
```

It takes a few minutes to create and configure all the scale set resources and VMs.

## Create an Azure key vault enabled for disk encryption

Azure Key Vault can store keys, secrets, or passwords that allow you to securely implement them in your applications and services. Cryptographic keys are stored in Azure Key Vault using software-protection, or you can import or generate your keys in Hardware Security Modules (HSMs) certified to FIPS 140-2 level 2 standards. These cryptographic keys are used to encrypt and decrypt virtual disks attached to your VM. You retain control of these cryptographic keys and can audit their use.

Define your own unique *keyvault_name*. Then, create a KeyVault with [az keyvault create](/cli/azure/ext/keyvault-preview/keyvault#ext-keyvault-preview-az-keyvault-create) in the same subscription and region as the scale set, and set the *--enabled-for-disk-encryption* access policy.

```azurecli-interactive
# Provide your own unique Key Vault name
keyvault_name=myuniquekeyvaultname

# Create Key Vault
az keyvault create --resource-group myResourceGroup --name $keyvault_name --enabled-for-disk-encryption
```

### Use an existing Key Vault

This step is only required if you have an existing Key Vault that you wish to use with disk encryption. Skip this step if you created a Key Vault in the previous section.

Define your own unique *keyvault_name*. Then, updated your KeyVault with [az keyvault update](/cli/azure/ext/keyvault-preview/keyvault#ext-keyvault-preview-az-keyvault-update) and set the *--enabled-for-disk-encryption* access policy.

```azurecli-interactive
# Provide your own unique Key Vault name
keyvault_name=myuniquekeyvaultname

# Create Key Vault
az keyvault update --name $keyvault_name --enabled-for-disk-encryption
```

## Enable encryption

To encrypt VM instances in a scale set, first get some information on the Key Vault resource ID with [az keyvault show](/cli/azure/ext/keyvault-preview/keyvault#ext-keyvault-preview-az-keyvault-show). These variables are used to then start the encryption process with [az vmss encryption enable](/cli/azure/vmss/encryption#az-vmss-encryption-enable):

```azurecli-interactive
# Get the resource ID of the Key Vault
vaultResourceId=$(az keyvault show --resource-group myResourceGroup --name $keyvault_name --query id -o tsv)

# Enable encryption of the data disks in a scale set
az vmss encryption enable \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --disk-encryption-keyvault $vaultResourceId \
    --volume-type DATA
```

It may take a minute or two for the encryption process to start.

As the scale set is upgrade policy on the scale set created in an earlier step is set to *automatic*, the VM instances automatically start the encryption process. On scale sets where the upgrade policy is to manual, start the encryption policy on the VM instances with [az vmss update-instances](/cli/azure/vmss#az-vmss-update-instances).

### Enable encryption using KEK to wrap the key

You can also use a Key Encryption Key for added security when encrypting the virtual machine scale set.

```azurecli-interactive
# Get the resource ID of the Key Vault
vaultResourceId=$(az keyvault show --resource-group myResourceGroup --name $keyvault_name --query id -o tsv)

# Enable encryption of the data disks in a scale set
az vmss encryption enable \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --disk-encryption-keyvault $vaultResourceId \
    --key-encryption-key myKEK \
    --key-encryption-keyvault $vaultResourceId \
    --volume-type DATA
```

> [!NOTE]
>  The syntax for the value of disk-encryption-keyvault parameter is the full identifier string:</br>
/subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br></br>
> The syntax for the value of the key-encryption-key parameter is the full URI to the KEK as in:</br>
https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id]

## Check encryption progress

To check on the status of disk encryption, use [az vmss encryption show](/cli/azure/vmss/encryption#az-vmss-encryption-show):

```azurecli-interactive
az vmss encryption show --resource-group myResourceGroup --name myScaleSet
```

When VM instances are encrypted, the status code reports *EncryptionState/encrypted*, as shown in the following example output:

```console
[
  {
    "disks": [
      {
        "encryptionSettings": null,
        "name": "myScaleSet_myScaleSet_0_disk2_3f39c2019b174218b98b3dfae3424e69",
        "statuses": [
          {
            "additionalProperties": {},
            "code": "EncryptionState/encrypted",
            "displayStatus": "Encryption is enabled on disk",
            "level": "Info",
            "message": null,
            "time": null
          }
        ]
      }
    ],
    "id": "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/virtualMachines/0",
    "resourceGroup": "MYRESOURCEGROUP"
  }
]
```

## Disable encryption

If you no longer wish to use encrypted VM instances disks, you can disable encryption with [az vmss encryption disable](/cli/azure/vmss/encryption?view=azure-cli-latest#az-vmss-encryption-disable) as follows:

```azurecli-interactive
az vmss encryption disable --resource-group myResourceGroup --name myScaleSet
```

## Next steps

- In this article, you used the Azure CLI to encrypt a virtual machine scale set. You can also use [Azure PowerShell](disk-encryption-powershell.md) or [Azure Resource Manager templates](disk-encryption-azure-resource-manager.md).
- If you wish to have Azure Disk Encryption applied after another extension is provisioned, you can use [extension sequencing](virtual-machine-scale-sets-extension-sequencing.md). 
- An end-to-end batch file example for Linux scale set data disk encryption can be found [here](https://gist.githubusercontent.com/ejarvi/7766dad1475d5f7078544ffbb449f29b/raw/03e5d990b798f62cf188706221ba6c0c7c2efb3f/enable-linux-vmss.bat). This example creates a resource group, Linux scale set, mounts a 5-GB data disk, and encrypts the virtual machine scale set.
