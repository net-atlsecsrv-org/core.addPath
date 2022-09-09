---
title: Use Remote Desktop to a Linux VM in Azure 
description: Learn how to install and configure Remote Desktop (xrdp) to connect to a Linux VM in Azure using graphical tools
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''

ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux

ms.topic: article
ms.date: 09/12/2019
ms.author: cynthn

---
# Install and configure Remote Desktop to connect to a Linux VM in Azure
Linux virtual machines (VMs) in Azure are usually managed from the command line using a secure shell (SSH) connection. When new to Linux, or for quick troubleshooting scenarios, the use of remote desktop may be easier. This article details how to install and configure a desktop environment ([xfce](https://www.xfce.org)) and remote desktop ([xrdp](https://www.xrdp.org)) for your Linux VM using the Resource Manager deployment model.


## Prerequisites
This article requires an existing Ubuntu 18.04 LTS VM in Azure. If you need to create a VM, use one of the following methods:

- The [Azure CLI](quick-create-cli.md)
- The [Azure portal](quick-create-portal.md)


## Install a desktop environment on your Linux VM
Most Linux VMs in Azure do not have a desktop environment installed by default. Linux VMs are commonly managed using SSH connections rather than a desktop environment. There are various desktop environments in Linux that you can choose. Depending on your choice of desktop environment, it may consume one to 2 GB of disk space, and take 5 to 10 minutes to install and configure all the required packages.

The following example installs the lightweight [xfce4](https://www.xfce.org/) desktop environment on an Ubuntu 18.04 LTS VM. Commands for other distributions vary slightly (use `yum` to install on Red Hat Enterprise Linux and configure appropriate `selinux` rules, or use `zypper` to install on SUSE, for example).

First, SSH to your VM. The following example connects to the VM named *myvm.westus.cloudapp.azure.com* with the username of *azureuser*. Use your own values:

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

If you are using Windows and need more information on using SSH, see [How to use SSH keys with Windows](ssh-from-windows.md).

Next, install xfce using `apt` as follows:

```bash
sudo apt-get update
sudo apt-get install xfce4
```

## Install and configure a remote desktop server
Now that you have a desktop environment installed, configure a remote desktop service to listen for incoming connections. [xrdp](http://xrdp.org) is an open source Remote Desktop Protocol (RDP) server that is available on most Linux distributions, and works well with xfce. Install xrdp on your Ubuntu VM as follows:

```bash
sudo apt-get -y install xrdp
sudo systemctl enable xrdp
```

Tell xrdp what desktop environment to use when you start your session. Configure xrdp to use xfce as your desktop environment as follows:

```bash
echo xfce4-session >~/.xsession
```

Restart the xrdp service for the changes to take effect as follows:

```bash
sudo service xrdp restart
```


## Set a local user account password
If you created a password for your user account when you created your VM, skip this step. If you only use SSH key authentication and do not have a local account password set, specify a password before you use xrdp to log in to your VM. xrdp cannot accept SSH keys for authentication. The following example specifies a password for the user account *azureuser*:

```bash
sudo passwd azureuser
```

> [!NOTE]
> Specifying a password does not update your SSHD configuration to permit password logins if it currently does not. From a security perspective, you may wish to connect to your VM with an SSH tunnel using key-based authentication and then connect to xrdp. If so, skip the following step on creating a network security group rule to allow remote desktop traffic.


## Create a Network Security Group rule for Remote Desktop traffic
To allow Remote Desktop traffic to reach your Linux VM, a network security group rule needs to be created that allows TCP on port 3389 to reach your VM. For more information about network security group rules, see [What is a network security group?](../../virtual-network/security-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) You can also [use the Azure portal to create a network security group rule](../windows/nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

The following example creates a network security group rule with [az vm open-port](/cli/azure/vm#az-vm-open-port) on port *3389*. From the Azure CLI, not the SSH session to your VM, open the following network security group rule:

```azurecli
az vm open-port --resource-group myResourceGroup --name myVM --port 3389
```


## Connect your Linux VM with a Remote Desktop client
Open your local remote desktop client and connect to the IP address or DNS name of your Linux VM. Enter the username and password for the user account on your VM as follows:

![Connect to xrdp using your Remote Desktop client](./media/use-remote-desktop/remote-desktop-client.png)

After authenticating, the xfce desktop environment will load and look similar to the following example:

![xfce desktop environment through xrdp](./media/use-remote-desktop/xfce-desktop-environment.png)

If your local RDP client uses network level authentication (NLA), you may need to disable that connection setting. XRDP does not currently support NLA. You can also look at alternative RDP solutions that do support NLA, such as [FreeRDP](https://www.freerdp.com).


## Troubleshoot
If you cannot connect to your Linux VM using a Remote Desktop client, use `netstat` on your Linux VM to verify that your VM is listening for RDP connections as follows:

```bash
sudo netstat -plnt | grep rdp
```

The following example shows the VM listening on TCP port 3389 as expected:

```bash
tcp     0     0      127.0.0.1:3350     0.0.0.0:*     LISTEN     53192/xrdp-sesman
tcp     0     0      0.0.0.0:3389       0.0.0.0:*     LISTEN     53188/xrdp
```

If the *xrdp-sesman* service is not listening, on an Ubuntu VM restart the service as follows:

```bash
sudo service xrdp restart
```

Review logs in */var/log* on your Ubuntu VM for indications as to why the service may not be responding. You can also monitor the syslog during a remote desktop connection attempt to view any errors:

```bash
tail -f /var/log/syslog
```

Other Linux distributions such as Red Hat Enterprise Linux and SUSE may have different ways to restart services and alternate log file locations to review.

If you do not receive any response in your remote desktop client and do not see any events in the system log, this behavior indicates that remote desktop traffic cannot reach the VM. Review your network security group rules to ensure that you have a rule to permit TCP on port 3389. For more information, see [Troubleshoot application connectivity issues](../windows/troubleshoot-app-connection.md).


## Next steps
For more information about creating and using SSH keys with Linux VMs, see [Create SSH keys for Linux VMs in Azure](mac-create-ssh-keys.md).

For information on using SSH from Windows, see [How to use SSH keys with Windows](ssh-from-windows.md).

