---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin

# Used by articles that register native client applications in the B2C tenant.

---

This quickstart uses a pre-created Azure key vault. You can create a key vault by following the steps in the [Azure CLI quickstart](/azure/key-vault/general/quick-create-cli), [Azure PowerShell quickstart](/azure/key-vault/general/quick-create-powershell), or [Azure portal quickstart](/azure/key-vault/general/quick-create-portal). 

Alternatively, you can simply run the Azure CLI or Azure PowerShell commands below.

> [!Important]
> Each key vault must have a unique name. Replace <your-unique-keyvault-name> with the name of your key vault in the following examples.

```azurecli
az group create --name "myResourceGroup" -l "EastUS"

az keyvault create --name "<your-unique-keyvault-name>" -g "myResourceGroup"
```

```azurepowershell
New-AzResourceGroup -Name myResourceGroup -Location EastUS

New-AzKeyVault -Name "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup" -Location "EastUS"
```