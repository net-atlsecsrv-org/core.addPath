---
title: 'Prerequisites for Azure AD Connect cloud provisioning in Azure AD'
description: This article describes the prerequisites and hardware requirements you need for cloud provisioning.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/11/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
---

# Prerequisites for Azure AD Connect cloud provisioning
This article provides guidance on how to choose and use Azure Active Directory (Azure AD) Connect cloud provisioning as your identity solution.

## Cloud provisioning agent requirements
You need the following to use Azure AD Connect cloud provisioning:

- Domain Administrator or Enterprise Administrator credentials to create the Azure AD Connect Cloud Sync gMSA (group Managed Service Account) to run the agent service.	
- A hybrid identity administrator account for your Azure AD tenant that is not a guest user.
- An on-premises server for the provisioning agent with Windows 2012 R2 or later.  This server should be a tier 0 server based on the [Active Directory administrative tier model](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material).
- On-premises firewall configurations.

## Group Managed Service Accounts
A group Managed Service Account is a managed domain account that provides automatic password management, simplified service principal name (SPN) management,the ability to delegate the management to other administrators, and also extends this functionality over multiple servers.  Azure AD Connect Cloud Sync supports and uses a gMSA for running the agent.  You will be prompted for administrative credentials during setup, in order to create this account.  The account will appear as (domain\provAgentgMSA$).  For more information on a gMSA, see [Group Managed Service Accounts](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview) 

### Prerequisites for gMSA:
1.	The Active Directory schema in the gMSA domain's forest needs to be updated to Windows Server 2012
2.	[PowerShell RSAT modules](/windows-server/remote/remote-server-administration-tools) on a domain controller
3.	At least one domain controller in the domain must be running Windows Server 2012.
4.	A domain joined server where the agent is being installed needs to be either Windows Server 2012 or later.

For steps on how to upgrade an existing agent to use a gMSA account see [Group Managed Service Accounts](how-to-install.md#group-managed-service-accounts).

### In the Azure Active Directory admin center

1. Create a cloud-only hybrid identity administrator account on your Azure AD tenant. This way, you can manage the configuration of your tenant if your on-premises services fail or become unavailable. Learn about how to [add a cloud-only hybrid identity administrator account](../fundamentals/add-users-azure-active-directory.md). Finishing this step is critical to ensure that you don't get locked out of your tenant.
1. Add one or more [custom domain names](../fundamentals/add-custom-domain.md) to your Azure AD tenant. Your users can sign in with one of these domain names.

### In your directory in Active Directory

Run the [IdFix tool](/office365/enterprise/prepare-directory-attributes-for-synch-with-idfix) to prepare the directory attributes for synchronization.

### In your on-premises environment

 1. Identify a domain-joined host server running Windows Server 2012 R2 or greater with a minimum of 4-GB RAM and .NET 4.7.1+ runtime.

 >[!NOTE]
 > Be aware that defining a scoping filter incurs a memory cost on the host server.  If no scoping filter is used there is no extra memory cost. The 4GB minimum will support synchronization for up to 12 organizational units defined in the scoping filter. If you need to synchronize additional OUs, you will need to increase the minimum amount of memory. Use the following table as a guide:
 >
 >	
 >  | Number of OUs in Scoping Filter| minimum required memory|
 > 	| --- | --- |
 >	| 12| 4 GB|
 >	| 18|5.5 GB|
 >	| 28|10+ GB|
 >
 > 

 2. The PowerShell execution policy on the local server must be set to Undefined or RemoteSigned.

 3. If there's a firewall between your servers and Azure AD, configure the following items:
   - Ensure that agents can make *outbound* requests to Azure AD over the following ports:

        | Port number | How it's used |
        | --- | --- |
        | **80** | Downloads the certificate revocation lists (CRLs) while validating the TLS/SSL certificate.  |
        | **443** | Handles all outbound communication with the service. |
        |**8082**|Required for installation and if you want to configure HIS administration API.  This port can be removed once the agent is installed and if you are not planning on using the API.   |
        | **8080** (optional) | Agents report their status every 10 minutes over port 8080, if port 443 is unavailable. This status is displayed in the Azure AD portal. |
   
     
   - If your firewall enforces rules according to the originating users, open these ports for traffic from Windows services that run as a network service.
   - If your firewall or proxy allows you to specify safe suffixes, add connections to \*.msappproxy.net and \*.servicebus.windows.net. If not, allow access to the [Azure datacenter IP ranges](https://www.microsoft.com/download/details.aspx?id=41653), which are updated weekly.
   - Your agents need access to login.windows.net and login.microsoftonline.com for initial registration. Open your firewall for those URLs as well.
   - For certificate validation, unblock the following URLs: mscrl.microsoft.com:80, crl.microsoft.com:80, ocsp.msocsp.com:80, and www\.microsoft.com:80. These URLs are used for certificate validation with other Microsoft products, so you might already have these URLs unblocked.

>[!NOTE]
> Installing the cloud provisioning agent on Windows Server Core is not supported.




### Additional requirements
- [Microsoft .NET Framework 4.7.1](https://www.microsoft.com/download/details.aspx?id=56116) 

#### TLS requirements

>[!NOTE]
>Transport Layer Security (TLS) is a protocol that provides for secure communications. Changing the TLS settings affects the entire forest. For more information, see [Update to enable TLS 1.1 and TLS 1.2 as default secure protocols in WinHTTP in Windows](https://support.microsoft.com/help/3140245/update-to-enable-tls-1-1-and-tls-1-2-as-default-secure-protocols-in-wi).

The Windows server that hosts the Azure AD Connect cloud provisioning agent must have TLS 1.2 enabled before you install it.

To enable TLS 1.2, follow these steps.

1. Set the following registry keys:
	
    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319] "SchUseStrongCrypto"=dword:00000001
    ```

1. Restart the server.

## Known limitations
The following are known limitations:

### Delta Synchronization

- Group scope filtering for delta sync does not support more than 1500 members.
- When you delete a group that's used as part of a group scoping filter, users who are members of the group, don't get deleted. 
- When you rename the OU or group that's in scope, delta sync will not remove the users.

### Provisioning Logs
- Provisioning logs do not clearly differentiate between create and update operations.  You may see a create operation for an update and an update operation for a create.

### Group re-naming or OU re-naming
- If you rename a group or OU in AD that's in scope for a given configuration, the cloud provisioning job will not be able to recognize the name change in AD. The job won't go into quarantine and will remain healthy.


## Next steps 

- [What is provisioning?](what-is-provisioning.md)
- [What is Azure AD Connect cloud provisioning?](what-is-cloud-provisioning.md)