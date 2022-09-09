---
title: Use proximity placement groups 
description: Learn about creating and using proximity placement groups for virtual machines in Azure. 
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 10/30/2019
ms.author: cynthn

---

# Deploy VMs to proximity placement groups using Azure CLI

To get VMs as close as possible, achieving the lowest possible latency, you should deploy them within a [proximity placement group](co-location.md#proximity-placement-groups).

A proximity placement group is a logical grouping used to make sure that Azure compute resources are physically located close to each other. Proximity placement groups are useful for workloads where low latency is a requirement.


## Create the proximity placement group
Create a proximity placement group using [`az ppg create`](/cli/azure/ppg#az-ppg-create). 

```azurecli-interactive
az group create --name myPPGGroup --location westus
az ppg create \
   -n myPPG \
   -g myPPGGroup \
   -l westus \
   -t standard 
```

## List proximity placement groups

You can list all of your proximity placement groups using [az ppg list](/cli/azure/ppg#az-ppg-list).

```azurecli-interactive
az ppg list -o table
```

## Create a VM

Create a VM within the proximity placement group using [new az vm](/cli/azure/vm#az-vm-create).

```azurecli-interactive
az vm create \
   -n myVM \
   -g myPPGGroup \
   --image UbuntuLTS \
   --ppg myPPG  \
   --generate-ssh-keys \
   --size Standard_D1_v2  \
   -l westus
```

You can see the VM in the proximity placement group using [az ppg show](/cli/azure/ppg#az-ppg-show).

```azurecli-interactive
az ppg show --name myppg --resource-group myppggroup --query "virtualMachines"
```

## Availability Sets
You can also create an  availability set in your proximity placement group. Use the same `--ppg` parameter with [az vm availability-set create](/cli/azure/vm/availability-set#az-vm-availability-set-create) to create an availability set and all of the VMs in the availability set will also be created in the same proximity placement group.

## Scale sets

You can also create a scale set in your proximity placement group. Use the same `--ppg` parameter with [az vmss create](/cli/azure/vmss?view=azure-cli-latest#az-vmss-create) to create a scale set and all of the instances will be created in the same proximity placement group.

## Next steps

Learn more about the [Azure CLI](/cli/azure/ppg) commands for proximity placement groups.
