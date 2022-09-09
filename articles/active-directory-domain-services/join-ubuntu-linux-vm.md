---
title: Join an Ubuntu VM to Azure AD Domain Services | Microsoft Docs
description: Learn how to configure and join an Ubuntu Linux virtual machine to an Azure AD Domain Services managed domain.
services: active-directory-ds
author: iainfoulds
manager: daveba

ms.assetid: 804438c4-51a1-497d-8ccc-5be775980203
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 01/22/2020
ms.author: iainfou

---
# Join an Ubuntu Linux virtual machine to an Azure AD Domain Services managed domain

To let users sign in to virtual machines (VMs) in Azure using a single set of credentials, you can join VMs to an Azure Active Directory Domain Services (AD DS) managed domain. When you join a VM to an Azure AD DS managed domain, user accounts and credentials from the domain can be used to sign in and manage servers. Group memberships from the Azure AD DS managed domain are also applied to let you control access to files or services on the VM.

This article shows you how to join an Ubuntu Linux VM to an Azure AD DS managed domain.

## Prerequisites

To complete this tutorial, you need the following resources and privileges:

* An active Azure subscription.
    * If you don't have an Azure subscription, [create an account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* An Azure Active Directory tenant associated with your subscription, either synchronized with an on-premises directory or a cloud-only directory.
    * If needed, [create an Azure Active Directory tenant][create-azure-ad-tenant] or [associate an Azure subscription with your account][associate-azure-ad-tenant].
* An Azure Active Directory Domain Services managed domain enabled and configured in your Azure AD tenant.
    * If needed, the first tutorial [creates and configures an Azure Active Directory Domain Services instance][create-azure-ad-ds-instance].
* A user account that's a part of the Azure AD DS managed domain.

## Create and connect to an Ubuntu Linux VM

If you have an existing Ubuntu Linux VM in Azure, connect to it using SSH, then continue on to the next step to [start configuring the VM](#configure-the-hosts-file).

If you need to create an Ubuntu Linux VM, or want to create a test VM for use with this article, you can use one of the following methods:

* [Azure portal](../virtual-machines/linux/quick-create-portal.md)
* [Azure CLI](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

When you create the VM, pay attention to the virtual network settings to make sure that the VM can communicate with the Azure AD DS managed domain:

* Deploy the VM into the same, or a peered, virtual network in which you have enabled Azure AD Domain Services.
* Deploy the VM into a different subnet than your Azure AD Domain Services instance.

Once the VM is deployed, follow the steps to connect to the VM using SSH.

## Configure the hosts file

To make sure that the VM host name is correctly configured for the managed domain, edit the */etc/hosts* file and set the hostname:

```console
sudo vi /etc/hosts
```

In the *hosts* file, update the *localhost* address. In the following example:

* *aaddscontoso.com* is the DNS domain name of your Azure AD DS managed domain.
* *ubuntu* is the hostname of your Ubuntu VM that you're joining to the managed domain.

Update these names with your own values:

```console
127.0.0.1 ubuntu.aaddscontoso.com ubuntu
```

When done, save and exit the *hosts* file using the `:wq` command of the editor.

## Install required packages

The VM needs some additional packages to join the VM to the Azure AD DS managed domain. To install and configure these packages, update and install the domain-join tools using `apt-get`

During the Kerberos installation, the *krb5-user* package prompts for the realm name in ALL UPPERCASE. For example, if the name of your Azure AD DS managed domain is *aaddscontoso.com*, enter *AADDSCONTOSO.COM* as the realm. The installation writes the `[realm]` and `[domain_realm]` sections in */etc/krb5.conf* configuration file. Make sure that you specify the realm an ALL UPPERCASE:

```console
sudo apt-get update
sudo apt-get install krb5-user samba sssd sssd-tools libnss-sss libpam-sss ntp ntpdate realmd adcli
```

## Configure Network Time Protocol (NTP)

For domain communication to work correctly, the date and time of your Ubuntu VM must synchronize with the Azure AD DS managed domain. Add your Azure AD DS managed domain's NTP hostname to the */etc/ntp.conf* file.

1. Open the *ntp.conf* file with an editor:

    ```console
    sudo vi /etc/ntp.conf
    ```

1. In the *ntp.conf* file, create a line to add your Azure AD DS managed domain's DNS name. In the following example, an entry for *aaddscontoso.com* is added. Use your own DNS name:

    ```console
    server aaddscontoso.com
    ```

    When done, save and exit the *ntp.conf* file using the `:wq` command of the editor.

1. To make sure that the VM is synchronized with the Azure AD DS managed domain, the following steps are needed:

    * Stop the NTP server
    * Update the date and time from the managed domain
    * Start the NTP service

    Run the following commands to complete these steps. Use your own DNS name with the `ntpdate` command:

    ```console
    sudo systemctl stop ntp
    sudo ntpdate aaddscontoso.com
    sudo systemctl start ntp
    ```

## Join VM to the managed domain

Now that the required packages are installed on the VM and NTP is configured, join the VM to the Azure AD DS managed domain.

1. Use the `realm discover` command to discover the Azure AD DS managed domain. The following example discovers the realm *AADDSCONTOSO.COM*. Specify your own Azure AD DS managed domain name in ALL UPPERCASE:

    ```console
    sudo realm discover AADDSCONTOSO.COM
    ```

   If the `realm discover` command can't find your Azure AD DS managed domain, review the following troubleshooting steps:

    * Make sure that the domain is reachable from the VM. Try `ping aaddscontoso.com` to see if a positive reply is returned.
    * Check that the VM is deployed to the same, or a peered, virtual network in which the Azure AD DS managed domain is available.
    * Confirm that the DNS server settings for the virtual network have been updated to point to the domain controllers of the Azure AD DS managed domain.

1. Now initialize Kerberos using the `kinit` command. Specify a user that's a part of the Azure AD DS managed domain. If needed, [add a user account to a group in Azure AD](../active-directory/fundamentals/active-directory-groups-members-azure-portal.md).

    Again, the Azure AD DS managed domain name must be entered in ALL UPPERCASE. In the following example, the account named `contosoadmin@aaddscontoso.com` is used to initialize Kerberos. Enter your own user account that's a part of the Azure AD DS managed domain:

    ```console
    kinit contosoadmin@AADDSCONTOSO.COM
    ```

1. Finally, join the machine to the Azure AD DS managed domain using the `realm join` command. Use the same user account that's a part of the Azure AD DS managed domain that you specified in the previous `kinit` command, such as `contosoadmin@AADDSCONTOSO.COM`:

    ```console
    sudo realm join --verbose AADDSCONTOSO.COM -U 'contosoadmin@AADDSCONTOSO.COM' --install=/
    ```

It takes a few moments to join the VM to the Azure AD DS managed domain. The following example output shows the VM has successfully joined to the Azure AD DS managed domain:

```output
Successfully enrolled machine in realm
```

If your VM can't successfully complete the domain-join process, make sure that the VM's network security group allows outbound Kerberos traffic on TCP + UDP port 464 to the virtual network subnet for your Azure AD DS managed domain.

If you received the error *Unspecified GSS failure.  Minor code may provide more information (Server not found in Kerberos database)*, open the file */etc/krb5.conf* and add the following code in `[libdefaults]` section and try again:

```console
rdns=false
```

## Update the SSSD configuration

One of the packages installed in a previous step was for System Security Services Daemon (SSSD). When a user tries to sign in to a VM using domain credentials, SSSD relays the request to an authentication provider. In this scenario, SSSD uses Azure AD DS to authenticate the request.

1. Open the *sssd.conf* file with an editor:

    ```console
    sudo vi /etc/sssd/sssd.conf
    ```

1. Comment out the line for *use_fully_qualified_names* as follows:

    ```console
    # use_fully_qualified_names = True
    ```

    When done, save and exit the *sssd.conf* file using the `:wq` command of the editor.

1. To apply the change, restart the SSSD service:

    ```console
    sudo service sssd restart
    ```

## Configure user account and group settings

With the VM joined to the Azure AD DS managed domain and configured for authentication, there are a few user configuration options to complete. These configuration changes include allowing password-based authentication, and automatically creating home directories on the local VM when domain users first sign in.

### Allow password authentication for SSH

By default, users can only sign in to a VM using SSH public key-based authentication. Password-based authentication fails. When you join the VM to an Azure AD DS managed domain, those domain accounts need to use password-based authentication. Update the SSH configuration to allow password-based authentication as follows.

1. Open the *sshd_conf* file with an editor:

    ```console
    sudo vi /etc/ssh/sshd_config
    ```

1. Update the line for *PasswordAuthentication* to *yes*:

    ```console
    PasswordAuthentication yes
    ```

    When done, save and exit the *sshd_conf* file using the `:wq` command of the editor.

1. To apply the changes and let users sign in using a password, restart the SSH service:

    ```console
    sudo systemctl restart ssh
    ```

### Configure automatic home directory creation

To enable automatic creation of the home directory when a user first signs in, complete the following steps:

1. Open the */etc/pam.d/common-session* file in an editor:

    ```console
    sudo vi /etc/pam.d/common-session
    ```

1. Add the following line in this file below the line `session optional pam_sss.so`:

    ```console
    session required pam_mkhomedir.so skel=/etc/skel/ umask=0077
    ```

    When done, save and exit the *common-session* file using the `:wq` command of the editor.

### Grant the 'AAD DC Administrators' group sudo privileges

To grant members of the *AAD DC Administrators* group administrative privileges on the Ubuntu VM, you add an entry to the */etc/sudoers*. Once added, members of the *AAD DC Administrators* group can use the `sudo` command on the Ubuntu VM.

1. Open the *sudoers* file for editing:

    ```console
    sudo visudo
    ```

1. Add the following entry to the end of */etc/sudoers* file:

    ```console
    # Add 'AAD DC Administrators' group members as admins.
    %AAD\ DC\ Administrators ALL=(ALL) NOPASSWD:ALL
    ```

    When done, save and exit the editor using the `Ctrl-X` command.

## Sign in to the VM using a domain account

To verify that the VM has been successfully joined to the Azure AD DS managed domain, start a new SSH connection using a domain user account. Confirm that a home directory has been created, and that group membership from the domain is applied.

1. Create a new SSH connection from your console. Use a domain account that belongs to the managed domain using the `ssh -l` command, such as `contosoadmin@aaddscontoso.com` and then enter the address of your VM, such as *ubuntu.aaddscontoso.com*. If you use the Azure Cloud Shell, use the public IP address of the VM rather than the internal DNS name.

    ```console
    ssh -l contosoadmin@AADDSCONTOSO.com ubuntu.aaddscontoso.com
    ```

1. When you've successfully connected to the VM, verify that the home directory was initialized correctly:

    ```console
    pwd
    ```

    You should be in the */home* directory with your own directory that matches the user account.

1. Now check that the group memberships are being resolved correctly:

    ```console
    id
    ```

    You should see your group memberships from the Azure AD DS managed domain.

1. If you signed in to the VM as a member of the *AAD DC Administrators* group, check that you can correctly use the `sudo` command:

    ```console
    sudo apt-get update
    ```

## Next steps

If you have problems connecting the VM to the Azure AD DS managed domain or signing in with a domain account, see [Troubleshooting domain join issues](join-windows-vm.md#troubleshoot-domain-join-issues).

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
