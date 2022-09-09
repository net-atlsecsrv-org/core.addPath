---
title: Overview of cloud-init support for Linux virtual machines in Azure 
description: Overview of cloud-init capabilities in Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: danielsollondon
manager: gwallace
editor: ''
tags: azure-resource-manager

ms.assetid: 195c22cd-4629-4582-9ee3-9749493f1d72
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 10/11/2019
ms.author: danis

---
# Cloud-init support for virtual machines in Azure
This article explains the support that exists for [cloud-init](https://cloudinit.readthedocs.io) to configure a virtual machine (VM) or virtual machine scale sets at provisioning time in Azure. These cloud-init scripts run on first boot once the resources have been provisioned by Azure.  

## Cloud-init overview
[Cloud-init](https://cloudinit.readthedocs.io) is a widely used approach to customize a Linux VM as it boots for the first time. You can use cloud-init to install packages and write files, or to configure users and security. Because cloud-init is called during the initial boot process, there are no additional steps or required agents to apply your configuration.  For more information on how to properly format your `#cloud-config` files, see the [cloud-init documentation site](https://cloudinit.readthedocs.io/en/latest/topics/format.html#cloud-config-data).  `#cloud-config` files are text files encoded in base64.

Cloud-init also works across distributions. For example, you don't use **apt-get install** or **yum install** to install a package. Instead you can define a list of packages to install. Cloud-init automatically uses the native package management tool for the distro you select.

We are actively working with our endorsed Linux distro partners in order to have cloud-init enabled images available in the Azure marketplace. These images will make your cloud-init deployments and configurations work seamlessly with VMs and virtual machine scale sets. The following table outlines the current cloud-init enabled images availability on the Azure platform:

| Publisher | Offer | SKU | Version | cloud-init ready |
|:--- |:--- |:--- |:--- |:--- |
|Canonical |UbuntuServer |18.04-LTS |latest |yes | 
|Canonical |UbuntuServer |16.04-LTS |latest |yes | 
|Canonical |UbuntuServer |14.04.5-LTS |latest |yes |
|CoreOS |CoreOS |Stable |latest |yes |
|OpenLogic 7.7 |CentOS |7-CI |7.7.20190920 |preview |
|Oracle 7.7 |Oracle-Linux |77-ci |7.7.01|preview |
|RedHat 7.6 |RHEL |7-RAW-CI |7.6.2019072418 |yes |
|RedHat 7.7 |RHEL |7-RAW-CI |7.7.2019081601 |preview |
	
Currently Azure Stack does not support the provisioning of RHEL 7.x and CentOS 7.x using cloud-init.

* For RHEL 7.6, cloud-init package, the supported package is: *18.2-1.el7_6.2* 
* For RHEL 7.7 (preview), cloud-init package, the preview package is: *18.5-3.el7*
* For CentOS 7.7 (preview), cloud-init package, the preview package is: *18.5-3.el7.centos*
* For Oracle 7.7 (preview), cloud-init package, the preview package is: *18.5-3.0.1.el7*

## What is the difference between cloud-init and the Linux Agent (WALA)?
WALA is an Azure platform-specific agent used to provision and configure VMs, and handle Azure extensions. We are enhancing the task of configuring VMs to use cloud-init instead of the Linux Agent in order to allow existing cloud-init customers to use their current cloud-init scripts.  If you have existing investments in cloud-init scripts for configuring Linux systems, there are **no additional settings required** to enable them. 

If you do not include the Azure CLI `--custom-data` switch at provisioning time, WALA takes the minimal VM provisioning parameters required to provision the VM and complete the deployment with the defaults.  If you reference the cloud-init `--custom-data` switch, whatever is contained in your custom data (individual settings or full script) overrides the WALA defaults. 

WALA configurations of VMs are time-constrained to work within the maximum VM provisioning time.  Cloud-init configurations applied to VMs do not have time constraints and will not cause a deployment to fail by timing out. 

## Deploying a cloud-init enabled Virtual Machine
Deploying a cloud-init enabled virtual machine is as simple as referencing a cloud-init enabled distribution during deployment.  Linux distribution maintainers have to choose to enable and integrate cloud-init into their base Azure published images. Once you have confirmed the image you want to deploy is cloud-init enabled, you can use the Azure CLI to deploy the image. 

The first step in deploying this image is to create a resource group with the [az group create](/cli/azure/group) command. An Azure resource group is a logical container into which Azure resources are deployed and managed. 

The following example creates a resource group named *myResourceGroup* in the *eastus* location.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```
The next step is to create a file in your current shell, named *cloud-init.txt* and paste the following configuration. For this example, create the file in the Cloud Shell not on your local machine. You can use any editor you wish. Enter `sensible-editor cloud-init.txt` to create the file and see a list of available editors. Choose #1 to use the **nano** editor. Make sure that the whole cloud-init file is copied correctly, especially the first line:

```yaml
#cloud-config
package_upgrade: true
packages:
  - httpd
```
Press `ctrl-X` to exit the file, type `y` to save the file and press `enter` to confirm the file name on exit.

The final step is to create a VM with the [az vm create](/cli/azure/vm) command. 

The following example creates a VM named *centos74* and creates SSH keys if they do not already exist in a default key location. To use a specific set of keys, use the `--ssh-key-value` option.  Use the `--custom-data` parameter to pass in your cloud-init config file. Provide the full path to the *cloud-init.txt* config if you saved the file outside of your present working directory. The following example creates a VM named *centos74*:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS-CI:7-CI:latest \
  --custom-data cloud-init.txt \
  --generate-ssh-keys 
```

When the VM has been created, the Azure CLI shows information specific to your deployment. Take note of the `publicIpAddress`. This address is used to access the VM.  It takes some time for the VM to be created, the packages to install, and the app to start. There are background tasks that continue to run after the Azure CLI returns you to the prompt. You can SSH into the VM and use the steps outlined in the Troubleshooting section to view the cloud-init logs. 

## Troubleshooting cloud-init
Once the VM has been provisioned, cloud-init will run through all the modules and script defined in `--custom-data` in order to configure the VM.  If you need to troubleshoot any errors or omissions from the configuration, you need to search for the module name (`disk_setup` or `runcmd` for example) in the cloud-init log - located in **/var/log/cloud-init.log**.

> [!NOTE]
> Not every module failure results in a fatal cloud-init overall configuration failure. For example, using the `runcmd` module, if the script fails, cloud-init will still report provisioning succeeded because the runcmd module executed.

For more details of cloud-init logging, refer to the [cloud-init documentation](https://cloudinit.readthedocs.io/en/latest/topics/logging.html) 

## Next steps
For cloud-init examples of configuration changes, see the following documents:
 
- [Add an additional Linux user to a VM](cloudinit-add-user.md)
- [Run a package manager to update existing packages on first boot](cloudinit-update-vm.md)
- [Change VM local hostname](cloudinit-update-vm-hostname.md) 
- [Install an application package, update configuration files and inject keys](tutorial-automate-vm-deployment.md)
