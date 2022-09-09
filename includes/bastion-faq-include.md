---
 title: include file
 description: include file
 services: bastion
 author: cherylmc
 ms.service: bastion
 ms.topic: include
 ms.date: 10/15/2019
 ms.author: cherylmc
 ms.custom: include file
---


### <a name="regions"></a>Which regions are available?

[!INCLUDE [region](bastion-regions-include.md)]

### <a name="publicip"></a>Do I need a public IP on my virtual machine?

You do NOT need a public IP on the Azure Virtual Machine that you are connecting to with the Azure Bastion service. The Bastion service will open the RDP/SSH session/connection to your virtual machine over the private IP of your virtual machine, within your virtual network.

### <a name="rdpssh"></a>Do I need an RDP or SSH client?

You do not need an RDP or SSH client to access the RDP/SSH to your Azure virtual machine in your Azure portal. Use the [Azure portal](https://portal.azure.com) to let you get RDP/SSH access to your virtual machine directly in the browser.

### <a name="agent"></a>Do I need an agent running in the Azure virtual machine?

You don't need to install an agent or any software on your browser or on your Azure virtual machine. The Bastion service is agentless and does not require any additional software for RDP/SSH.

### <a name="browsers"></a>Which browsers are supported?

Use the Microsoft Edge browser or Google Chrome on Windows. For Apple Mac, use Google Chrome browser. Microsoft Edge Chromium is also supported on both Windows and Mac, respectively.

### <a name="roles"></a>Are any roles required to access a virtual machine?

In order to make a connection, the following roles are required:

* Reader role on the virtual machine
* Reader role on the NIC with private IP of the virtual machine
* Reader role on the Azure Bastion resource

### <a name="pricingpage"></a>What is the pricing?

For more information, see the [pricing page](https://aka.ms/BastionHostPricing).

### <a name="session"></a>Why do I get "Your session has expired" error message before the Bastion session starts?

A session should be initiated only from the Azure portal. Sign in to the Azure portal and begin your session again. If you go to the URL directly from another browser session or tab, this error is expected. It helps ensure that your session is more secure and that the session can be accessed only through the Azure portal.

### <a name="keyboard"></a>What keyboard layouts are supported during the Bastion remote session?

Azure Bastion currently supports en-us-qwerty keyboard layout inside the VM.  Support for other locales for keyboard layout is work in progress.

### <a name="udr"></a>Is user-defined routing (UDR) supported on an Azure Bastion subnet?

No. UDR is not supported on an Azure Bastion subnet.
For scenarios that include both Azure Bastion and Azure Firewall/Network Virtual Appliance (NVA) in the same virtual network, you don’t need to force traffic from an Azure Bastion subnet to Azure Firewall because the communication between Azure Bastion and your VMs is private. For more details, see [Accessing VMs behind Azure Firewall with Bastion](https://azure.microsoft.com/blog/accessing-virtual-machines-behind-azure-firewall-with-azure-bastion/).

### <a name="filetransfer"></a>Is file-transfer supported with Azure Bastion RDP session?

We are working hard to add new features. As of now, file transfer is not supported but is part of our roadmap. Please feel free to share your feedback about new features on the [Azure Bastion Feedback page](https://feedback.azure.com/forums/217313-networking?category_id=367303).
