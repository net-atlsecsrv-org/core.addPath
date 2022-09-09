---
title: Prepare Hyper-V VMs for assessment/migration with Azure Migrate 
description: Learn how to prepare for assessment/migration of Hyper-V VMs with Azure Migrate.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: raynew
ms.custom: mvc
---

# Prepare for assessment and migration of Hyper-V VMs to Azure

This article describes how to prepare for assessment and migration of on-premises Hyper-V VMs to Azure with [Azure Migrate](migrate-services-overview.md).

[Azure Migrate](migrate-overview.md) provides a hub of tools that help you to discover, assess, and migrate apps, infrastructure, and workloads to Microsoft Azure. The hub includes Azure Migrate tools, and third-party independent software vendor (ISV) offerings.

This tutorial is the first in a series that shows you how to assess and migrate Hyper-V VMs to Azure. In this tutorial, you learn how to:

> [!div class="checklist"]
> * Prepare Azure. Set up permissions for your Azure account and resources to work with Azure Migrate.
> * Prepare on-premises Hyper-V hosts and VMs for server assessment.
> * Prepare on-premises Hyper-V hosts and VMs for server migration.


> [!NOTE]
> Tutorials show you the simplest deployment path for a scenario so that you can quickly set up a proof-of-concept. Tutorials use default options where possible, and don't show all possible settings and paths. For detailed instructions, review the How-tos for Hyper-V assessment and migration.


If you don't have an Azure subscription, create a [free account](https://azure.microsoft.com/pricing/free-trial/) before you begin.


## Prepare Azure

### Azure permissions

You need set up permissions for Azure Migrate deployment.

- Permissions for your Azure account to create an Azure Migrate project.
- Permissions for your account to register the Azure Migrate appliance. The appliance is used for Hyper-V discovery and migration. During appliance registration, Azure Migrate creates two Azure Active Directory (Azure AD) apps that uniquely identify the appliance:
    - The first app communicates with Azure Migrate service endpoints.
    - The second app accesses an Azure Key Vault that's created during registration, to store Azure AD app info and appliance configuration settings.



### Assign permissions to create project

Check you have permissions to create an Azure Migrate project.

1. In the Azure portal, open the subscription, and select **Access control (IAM)**.
2. In **Check access**, find the relevant account, and click it to view permissions.
3. You should have **Contributor** or **Owner** permissions.
    - If you just created a free Azure account, you're the owner of your subscription.
    - If you're not the subscription owner, work with the owner to assign the role.


### Assign permissions to register the appliance

You can assign permissions for Azure Migrate to create the Azure AD apps creating during appliance registration, using one of the following methods:

- A tenant/global admin can grant permissions to users in the tenant, to create and register Azure AD apps.
- A tenant/global admin can assign the Application Developer role (that has the permissions) to the account.

It's worth noting that:

- The apps don't have any other access permissions on the subscription other than those described above.
- You only need these permissions when you register a new appliance. You can remove the permissions after the appliance is set up.


#### Grant account permissions

The tenant/global admin can grant permissions as follows:

1. In Azure AD, the tenant/global admin should navigate to **Azure Active Directory** > **Users** > **User Settings**.
2. The admin should set **App registrations** to **Yes**.

    ![Azure AD permissions](./media/tutorial-prepare-hyper-v/aad.png)

> [!NOTE]
> This is a default setting that isn't sensitive. [Learn more](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).



#### Assign Application Developer role

The tenant/global admin can assign the Application Developer role to an account. [Learn more](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal).


## Prepare for Hyper-V assessment

To prepare for Hyper-V assessment, do the following:

1. Verify Hyper-V host settings.
2. Set up PowerShell remoting on each host, so that the Azure Migrate appliance can run PowerShell commands on the host, over a WinRM connection.
3. If VM disks are located in remote SMB storage, delegation of credentials is needed.
    - Enable CredSSP delegation so that the Azure Migrate appliance can act as the client, delegating credentials to a host.
    - You enable each host to act as a delegate for the appliance, as described below.
    - Later, when you set up the appliance, you will enable delegation on the appliance.
4. Review appliance requirements, and the URL/port access needed for the appliance.
5. Set up an account that the appliance will use to discover VMs.
6. Set up Hyper-V Integration Services on each VM you want to discover and assess.


You can configure these settings manually using the procedures below. Alternatively, you run the Hyper-V Prerequisites Configuration script.

### Hyper-V Prerequisites Configuration script

The script validates Hyper-V hosts and configures the settings you need to discover and assess Hyper-V VMs. Here's what it does:

- Checks that you're running the script on a supported PowerShell version.
- Verifies that you (the user running the script) have administrative privileges on the Hyper-V host.
- Allows you to create a local user account (not administrator) that is used for the Azure Migrate service to communicate with the Hyper-V host. This user account is added to these groups on the host:
    - Remote Management Users
    - Hyper-V Administrators
    - Performance Monitor Users
- Checks that the host is running a supported version of Hyper-V, and the Hyper-V role.
- Enables the WinRM service, and opens ports 5985 (HTTP) and 5986 (HTTPS) on the host (needed for metadata collection).
- Enables PowerShell remoting on the host.
- Checks that the Hyper-V integration service is enabled on all VMs managed by the host.
- Enables CredSSP on the host if needed.

Run the script as follows:

1. Make sure you have PowerShell version 4.0 or later installed on the Hyper-V host.
2. Download the script from the [Microsoft Download Center](https://aka.ms/migrate/script/hyperv). The script is cryptographically signed by Microsoft.
3. Validate the script integrity using either MD5 or SHA256 hash files. Hashtag values are below. Run this command to generate the hash for the script:
    ```
    C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]
    ```
    Example usage:
    ```
    C:\>CertUtil -HashFile C:\Users\Administrators\Desktop\ MicrosoftAzureMigrate-Hyper-V.ps1
    SHA256
    ```

4.	After validating the script integrity, run the script on each Hyper-V host with this PowerShell command:
    ```
    PS C:\Users\Administrators\Desktop> MicrosoftAzureMigrate-Hyper-V.ps1
    ```

#### Hashtag values

Hash values are:

| **Hash** | **Value** |
| --- | --- |
| **MD5** | 0ef418f31915d01f896ac42a80dc414e |
| **SHA256** | 0ad60e7299925eff4d1ae9f1c7db485dc9316ef45b0964148a3c07c80761ade2 |

### Verify Hyper-V host settings

1. Verify [Hyper-V host requirements](migrate-support-matrix-hyper-v.md#assessment-hyper-v-host-requirements) for server assessment.
2. Make sure the [required ports](migrate-support-matrix-hyper-v.md#assessment-port-requirements) are open on Hyper-V hosts.

### Enable PowerShell remoting on hosts

Set up PowerShell remoting on each host, as follows:

1. On each host, open a PowerShell console as admin.
2. Run this command:

    ```
    Enable-PSRemoting -force
    ```

### Enable CredSSP on hosts

If the host has VMs with disks are located on SMB shares, complete this step on the host.

- You can run this command remotely on all Hyper-V hosts.
- If you add new host nodes on a cluster they are automatically added for discovery, but you need to manually enable CredSSP on the new nodes if needed.

Enable as follows:

1. Identify Hyper-V hosts running Hyper-V VMs with disks on SMB shares.
2. Run the following command on each identified Hyper-V host:

    ```
    Enable-WSManCredSSP -Role Server -Force
    ```

When you set up the appliance you finish setting up CredSSP by [enabling it on the appliance](tutorial-assess-hyper-v.md#delegate-credentials-for-smb-vhds). This is described in the next tutorial in this series.


### Verify appliance settings

Before setting up the Azure Migrate appliance and beginning assessment in the next tutorial, prepare for appliance deployment.

1. [Verify](migrate-support-matrix-hyper-v.md#assessment-appliance-requirements) appliance requirements.
2. [Review](migrate-support-matrix-hyper-v.md#assessment-appliance-url-access) the Azure URLs that the appliance will need to access.
3. Review the data that the appliance will collect during discovery and assessment.
4. [Note](migrate-support-matrix-hyper-v.md#assessment-port-requirements) port access requirements for the appliance.


### Set up an account for VM discovery

Azure Migrate needs permissions to discover on-premises VMs.

- Set up a domain or local user account with administrator permissions on the Hyper-V hosts/cluster.

    - You need a single account for all hosts and clusters that you want to include in the discovery.
    - The account can be  a local or domain account. We recommend it has Administrator permissions on the Hyper-V hosts or clusters.
    - Alternatively, if you don't want to assign Administrator permissions, the following permissions are needed:
        - Remote Management Users
        - Hyper-V Administrators
        - Performance Monitor Users

### Enable Integration Services on VMs

Integration Services should be enabled on each VM so that Azure Migrate can capture operating system information on the VM.

On VMs that you want to discover and assess, enable [Hyper-V Integration Services](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services) on each VM.

## Prepare for Hyper-V migration

1. [Review](migrate-support-matrix-hyper-v.md#migration-hyper-v-host-requirements) Hyper-V host requirements for migration.
2. [Review](migrate-support-matrix-hyper-v.md#migration-hyper-v-vm-requirements) the requirements for Hyper-V VMs that you want to migrate to Azure.
3. [Note](migrate-support-matrix-hyper-v.md#migration-hyper-v-host-url-access) the Azure URLs to which Hyper-V hosts and clusters need access for VM migration.

## Next steps

In this tutorial, you:

> [!div class="checklist"]
> * Set up Azure account permissions.
> * Prepared Hyper-V hosts and VMs for assessment and migration.

Continue to the next tutorial to create an Azure Migrate project, and assess Hyper-V VMs for migration to Azure

> [!div class="nextstepaction"]
> [Assess Hyper-V VMs](./tutorial-assess-hyper-v.md)
