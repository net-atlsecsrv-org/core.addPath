---
title: Create and manage claimable VMs in Azure DevTest Labs | Microsoft Docs
description: Learn how to use the Azure portal to add a claimable virtual machine in Azure DevTest Labs and see the processes to follows to claim/unclaim a virtual machine.
ms.topic: article
ms.date: 06/26/2020
---

# Create and manage claimable VMs in Azure DevTest Labs
You add a claimable VM to a lab in a similar manner to how you [add a standard VM](devtest-lab-add-vm.md) – from a *base* that is either a [custom image](devtest-lab-create-template.md), [formula](devtest-lab-manage-formulas.md), or [Marketplace image](devtest-lab-configure-marketplace-images.md). This tutorial walks you through using the Azure portal to add a claimable VM to a lab in DevTest Labs, and shows the processes a user follows to claim and unclaim the VM.

## Steps to add a claimable VM to a lab in Azure DevTest Labs
1. Sign in to the [Azure portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Select **All Services**, and then select **DevTest Labs** in the **DEVOPS** section. If you select * (star) next to **DevTest Labs** in the **DEVOPS** section. This action adds **DevTest Labs** to the left navigational menu so that you can access it easily the next time. Then, you can select **DevTest Labs** on the left navigational menu.

    ![All services - select DevTest Labs](./media/devtest-lab-create-lab/all-services-select.png)
1. From the list of labs, select the lab in which you want to create the VM.
2. On the lab's **Overview** page, select **+ Add**.

    ![Add VM button](./media/devtest-lab-add-vm/devtestlab-home-blade-add-vm.png)
1. On the **Choose a base** page, select a marketplace image for the VM.
1. On the **Basic Settings** tab of the **Virtual machine** page, do the following actions:
    1. Enter a name for the VM in the **Virtual machine name** text box. The text box is pre-filled for you with a unique auto-generated name. The name corresponds to the user name within your email address followed by a unique 3-digit number. This feature saves you the time to think of a machine name and type it every time you create a machine. You can override this auto-filled field with a name of your choice if you wish to. To override the auto-filled name for the VM, enter a name in the **Virtual machine name** text box.
    2. Enter a **User Name** that is granted administrator privileges on the virtual machine. The **user name** for the machine is pre-filled with a unique auto-generated name. The name corresponds to the user name within your email address. This feature saves you the time to decide on a username every time you create a new machine. Again, you can override this auto-filled field with a username of your choice if you wish to. To override the auto-filled value for user name, enter a value in the **User Name** text box. This user is granted **administrator** privileges on the virtual machine.
    3. If you are creating first VM in the lab, enter a **password** for the user. To save this password as a default password in the Azure key vault associated with the lab, select **Save as default password**. The default password is saved in the key vault with the name: **VmPassword**. When you try to create subsequent VMs in the lab, **VmPassword** is automatically selected for the **password**. To override the value, clear the **Use a saved secret** check box, and enter a password.

        You can also save secrets in the key vault first and then use it while creating a VM in the lab. For more information, see [Store secrets in a key vault](devtest-lab-store-secrets-in-key-vault.md). To use the password stored in the key vault, select **Use a saved secret**, and specify a key value that corresponds to your secret (password).
    4. In the **More options** section, select **Change size**. Select one of the predefined items that specify the processor cores, RAM size, and the hard drive size of the VM to create.
    5. Select **Add or Remove Artifacts**. Select and configure the artifacts that you want to add to the base image.
    **Note:** If you're new to DevTest Labs or configuring artifacts, refer to the [Add an existing artifact to a VM](./devtest-lab-add-vm.md#add-an-existing-artifact-to-a-vm) section, and then return here when finished.
2. Switch to the **Advanced Settings** tab at the top, and do the following actions:
    1. To change the virtual network that the VM is in, select **Change VNet**.
    2. To change the subnet, select **Change subnet**.
    3. Specify whether the IP address of the VM is **public, private, or shared**.
    4. To automatically delete the VM, specify the **expiration date and time**.
    5. To make the VM claimable by a lab user, select **Yes** for **Make this machine claimable** option.
    6. Specify the number of the **instances of VM** that you want to make it available to your lab users.
3. Select **Create** to add the specified VM to the lab.

   The lab page displays the status of the VM's creation - first as **Creating**, then as **Running** after the VM has been started.

> [!NOTE]
> If you deploy lab VMs through [Azure Resource Manager templates](devtest-lab-create-environment-from-arm.md), you can create claimable VMs by setting the **allowClaim** property to true in the properties section.


## Using a claimable VM

A user can claim any VM from the list of "Claimable virtual machines" by doing one of these steps:

* From the list of "Claimable virtual machines" at the bottom of the lab's "Overview" pane, right-click on one of the VMs in the list and choose **Claim machine**.

  ![Request a specific claimable VM.](./media/devtest-lab-add-vm/devtestlab-claim-VM.png)


* At the top of the "Overview" pane, choose **Claim any**. A random virtual machine is assigned from the list of claimable VMs.

  ![Request any claimable VM.](./media/devtest-lab-add-vm/devtestlab-claim-any.png)


After a user claims a VM, DevTest Labs will start the machine and move it up into lab user's list of "My virtual machines". This means the lab user will now have owner privileges on this machine. The time required for this step may vary depending on start up times as well as any other custom actions being performed during the claim event. Once claimed, the machine is no longer available in the claimable pool.  

## Unclaim a VM

When a user is finished using a claimed VM and wants to make it available to someone else, they can return the claimed VM to the list of claimable virtual machines by doing one of these steps:

- From the list of "My virtual machines", right-click on one of the VMs in the list – or select its ellipsis (...) – and choose **Unclaim**.

  ![Unclaim a VM on the list of VM.](./media/devtest-lab-add-vm/devtestlab-unclaim-VM2.png)

- In the list of "My virtual machines", select a VM to open its management pane, then select **Unclaim** from the top menu bar.

  ![Unclaim a VM on the VM's management pane.](./media/devtest-lab-add-vm/devtestlab-unclaim-VM.png)

When a user unclaims a VM, they no longer have owner permissions for that specific lab VM and it is available to be claimed by any other lab user in the state that it was returned to the pool. 

### Transferring the data disk
If a claimable VM has a data disk attached to it and a user unclaims it, the data disk stays with the VM. When another user then claims that VM, that new user claims the data disk as well as the VM.

This is known as "transferring the data disk". The data disk then becomes available in the new user's list of **My data disks** for them to manage.

![Unclaim data disks.](./media/devtest-lab-add-vm/devtestlab-unclaim-datadisks.png)



## Next steps
* Once it is created, you can connect to the VM by selecting **Connect** on its management pane.
* Explore the [DevTest Labs Azure Resource Manager quickStart template gallery](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates).
