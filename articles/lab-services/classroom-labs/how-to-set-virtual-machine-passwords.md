---
title: Set passwords for VMs in Azure Lab Services | Microsoft Docs
description: Learn how to set and reset passwords for virtual machines (VMs) in classroom labs of Azure Lab Services. 
services: lab-services
documentationcenter: na
author: spelluru
manager: 
editor: ''

ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/12/2019
ms.author: spelluru

---
# Set up and manage virtual machine pool 
This article shows you how to do the following tasks:

- Increase the number of virtual machines (VMs) in the lab
- Start all VMs or selected VMs 
- Reset VMs

## Update the lab capacity
To increase or decrease the lab capacity (number of virtual machines in a lab), do the following steps:

1. On the **Virtual machine pool** page, select **Lab capacity: &lt;number&gt; machines**.
2. Enter the new **number of VMs** you want in the lab. This number must be greater than or equal to the number of users registered in the lab. 
3. Then, select **Save**. 

    ![Start all button](../media/how-to-set-virtual-machine-passwords/number-of-vms-in-lab.png)
4. If you increased the capacity, you can see the VM or VMs being created. 

    ![VM being created](../media/how-to-set-virtual-machine-passwords/vm-being-created.png)

## Start VMs

### Start ot stop all VMs
1. Switch to the **Virtual machine pool** page. 
2. Select **Start all** from the toolbar. 

    ![Start all button](../media/how-to-set-virtual-machine-passwords/start-all-vms-button.png)
3. After all the VMs are started, you can stop all VMs by selecting the **Stop all** button on the toolbar. 

    ![Stop all button](../media/how-to-set-virtual-machine-passwords/stop-all-vms-button.png)

### Start selected VMs
There are two ways to start selected VMs (one or more). First way is to select the VM or VMs in the list, and then select **Start** on the toolbar. The second way is to select the VM or VMs in the list, select dropdown in the **State** column in one of the rows, and then select **Start**. 

![Start selected VMs](../media/how-to-set-virtual-machine-passwords/start-selected-vms.png)

Similarly, you can stop one or more VMs by using the drop-down list in the **State** column or **Stop** on the toolbar. 

## Reset VMs
To reset one or more VMs, select them in the list, and then select **Reset** on the toolbar. 

![Reset selected VMs](../media/how-to-set-virtual-machine-passwords/reset-vm-button.png)

On the **Reset virtual machine(s)** dialog box, select **Reset**. 

![Reset VM dialog box](../media/how-to-set-virtual-machine-passwords/reset-vms-dialog.png)



## Set password for VMs
A lab owner (teacher) can set/reset the password for VMs at the time of creating the lab (lab creation wizard) or after creating the lab on the **Template** page. 

### Set password at the time of lab creation
A lab owner (teacher) can set a password for VMs in the lab on the **Virtual machine credentials** page of the lab creation wizard.

![New lab window](../media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)

By enabling/disabling the **Use same password for all virtual machines** option on this page, a teacher can choose to use same password for all VMs in the lab or allow students to set passwords for their VMs. By default, this setting is enabled for all Windows and Linux operating system images except Ubuntu. When this setting is disabled, students will be prompted to set a password when they try to connect to the VM for the first time. 

### Reset password later

1. On the **Template** page of the lab, select **Reset password** on the toolbar. 

    ![Reset password menu on the home page](../media/how-to-set-virtual-machine-passwords/reset-password-menu-dashboard.png)
1. On the **Reset password** dialog box, enter a password, and select **Reset password**.
    
    ![Set password dialog box](../media/how-to-set-virtual-machine-passwords/set-password.png)

## Next steps
To learn about other student usage options you (as a lab owner) can configure, see the following article: [Configure student usage](how-to-configure-student-usage.md).

To learn about how students can reset passwords for their VMs, see [Set or reset password for virtual machines in classroom labs (students)](how-to-set-virtual-machine-passwords-student.md).