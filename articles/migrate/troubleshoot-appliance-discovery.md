---
title: Troubleshoot Azure Migrate appliance deployment and discovery
description: Get help with deploying the Azure Migrate appliance and discovering machines.
author: musa-57
ms.manager: abhemraj
ms.author: hamusa
ms.topic: troubleshooting
ms.date: 01/02/2020
---


# Troubleshoot the Azure Migrate appliance and discovery

This article helps you troubleshoot issues when deploying the [Azure Migrate](migrate-services-overview.md) appliance, and using the appliance to discover on-premises machines.


## What's supported?

[Review](migrate-appliance.md) the appliance support requirements.


## "Invalid OVF manifest entry"

If you receive the error "The provided manifest file is invalid: Invalid OVF manifest entry", do the following:

1. Verify that the Azure Migrate appliance OVA file is downloaded correctly by checking its hash value. [Learn more](https://docs.microsoft.com/azure/migrate/tutorial-assessment-vmware). If the hash value doesn't match, download the OVA file again and retry the deployment.
2. If deployment still fails, and you're using the VMware vSphere client to deploy the OVF file, try deploying it through the vSphere web client. If deployment still fails, try using a different web browser.
3. If you're using the vSphere web client and trying to deploy it on vCenter Server 6.5 or 6.7, try to deploy the OVA directly on the ESXi host:
   - Connect to the ESXi host directly (instead of vCenter Server) with the web client (https://<*host IP Address*>/ui).
   - In **Home** > **Inventory**, select **File** > **Deploy OVF template**. Browse to the OVA and complete the deployment.
4. If the deployment still fails, contact Azure Migrate support.

## Can't connect to the internet

This can happen if the appliance machine is behind a proxy.

- Make sure you provide the authorization credentials if the proxy needs them.
- If you're using a URL-based firewall proxy to control outbound connectivity, add [these URLs](migrate-appliance.md#url-access) to an allow list.
- If you're using an intercepting proxy to connect to the internet, import the proxy certificate onto the appliance VM using [these steps](https://docs.microsoft.com/azure/migrate/concepts-collector).

## Can't sign into Azure from the appliance web app

The error "Sorry, but we're having trouble signing you in" appears if you're using the incorrect Azure account to sign into Azure. This error occurs for a couple of reasons:

- If you sign into the appliance web application for the public cloud, using user account credentials for the Government cloud portal.
- If you sign into the appliance web application for the government cloud using user account credentials for the private cloud portal.

Ensure you're using the correct credentials.

##  Date/time synchronization error

An error about date and time synchronization (802) indicates that the server clock might be out of synchronization with the current time by more than five minutes. Change the clock time on the collector VM to match the current time:

1. Open an admin command prompt on the VM.
2. To check the time zone, run **w32tm /tz**.
3. To synchronize the time, run **w32tm /resync**.


## "UnableToConnectToServer"

If you get this connection error, you might be unable to connect to vCenter Server *Servername*.com:9443. The error details indicate that there's no endpoint listening at `https://\*servername*.com:9443/sdk` that can accept the message.

- Check whether you're running the latest version of the appliance. If you're not, upgrade the appliance to the [latest version](https://docs.microsoft.com/azure/migrate/concepts-collector).
- If the issue still occurs in the latest version, the appliance might be unable to resolve the specified vCenter Server name, or the specified port might be wrong. By default, if the port is not specified, the collector will try to connect to port number 443.

    1. Ping *Servername*.com from the appliance.
    2. If step 1 fails, try to connect to the vCenter server using the IP address.
    3. Identify the correct port number to connect to vCenter Server.
    4. Verify that vCenter Server is up and running.


## Error 60052/60039: Appliance might not be registered

- Error 60052, "The appliance might not be registered successfully to the Azure Migrate project" occurs if the Azure account used to register the appliance has insufficient permissions.
    - Make sure that the Azure user account used to register the appliance has at least Contributor permissions on the subscription.
    - [Learn more](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance---vmware) about required Azure roles and permissions.
- Error 60039, "The appliance might not be registered successfully to the Azure Migrate project" can occur if registration fails because the Azure Migrate project used to the register the appliance can't be found.
    - In the Azure portal and check whether the project exists in the resource group.
    - If the project doesn't exist, create a new Azure Migrate project in your resource group and register the appliance again. [Learn how to](https://docs.microsoft.com/azure/migrate/how-to-add-tool-first-time#create-a-project-and-add-a-tool) create a new project.

## Error 60030/60031: Key Vault management operation failed

If you receive the error 60030 or 60031, "An Azure Key Vault management operation failed", do the following:
- Make sure the Azure user account used to register the appliance has at least Contributor permissions on the subscription.
- Make sure the account has access to the key vault specified in the error message, and then retry the operation.
- If the issue persists, contact Microsoft support.
- [Learn more](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance---vmware) about the required Azure roles and permissions.

## Error 60028: Discovery couldn't be initiated

Error 60028: "Discovery couldn't be initiated because of an error. The operation failed for the specified list of hosts or clusters" indicates that discovery couldn't be started on the hosts listed in the error because of a problem in accessing or retrieving VM information. The rest of the hosts were successfully added.

- Add the hosts listed in the error again, using the **Add host** option.
- If there's a validation error, review the remediation guidance to fix the errors, and then try the **Save and start discovery** option again.

## Error 60025: Azure AD operation failed 
Error 60025: "An Azure AD operation failed. The error occurred while creating or updating the Azure AD application" occurs when the Azure user account used to initiate the discovery is different from the account used to register the appliance. Do one of the following:

- Ensure that the user account initiating the discovery is same as the one used to register the appliance.
- Provide Azure Active Directory application access permissions to the user account for which the discovery operation is failing.
- Delete the resource group previously created for the Azure Migrate project. Create another resource group to start again.
- [Learn more](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance---vmware) about Azure Active Directory application permissions.


## Error 50004: Can't connect to host or cluster

Error 50004: "Can't connect to a host or cluster because the server name can't be resolved. WinRM error code: 0x803381B9" might occur if the Azure DNS service for the appliance can't resolve the cluster or host name you provided.

- If you see this error on the cluster, cluster FQDN.
- You might also see this error for hosts in a cluster. This indicates that the appliance can connect to the cluster, but the cluster returns host names that aren't FQDNs. To resolve this error, update the hosts file on the appliance by adding a mapping of the IP address and host names:
    1. Open Notepad as an admin.
    2. Open the C:\Windows\System32\Drivers\etc\hosts file.
    3. Add the IP address and host name in a row. Repeat for each host or cluster where you see this error.
    4. Save and close the hosts file.
    5. Check whether the appliance can connect to the hosts, using the appliance management app. After 30 minutes, you should see the latest information for these hosts in the Azure portal.

## Discovered VMs not in portal

If discovery state is "Discovery in progress", but don't yet see the VMs in the portal, wait a few minutes:
- It takes around 15 minutes for a VMware VM .
- It takes around two minutes for each added host for Hyper-V VM discovery.

If you wait and the state doesn't change, select **Refresh** on the **Servers** tab. This should show the count of the discovered servers in Azure Migrate: Server Assessment and Azure Migrate: Server Migration.

If this doesn't work and you're discovering VMware servers:

- Verify that the vCenter account you specified has permissions set correctly, with access to at least one VM.
- Azure Migrate can't discovered VMware VMs if the vCenter account has access granted at vCenter VM folder level. [Learn more](set-discovery-scope.md) about scoping discovery.

## VM data not in portal

If discovered VMs don't appear in the portal or if the VM data is outdated, wait a few minutes. It takes up to 30 minutes for changes in discovered VM configuration data to appear in the portal. It may take a few hours for changes in application data to appear. If there's no data after this time, try refreshing, as follows

1. In **Servers** > **Azure Migrate Server Assessment**, select **Overview**.
2. Under **Manage**, select **Agent Health**.
3. Select **Refresh agent**.
4. Wait for the refresh operation to complete. You should now see up-to-date information.

## Deleted VMs appear in portal

If you delete VMs and they still appear in the portal, wait 30 minutes. If they still appear, refresh as described above.

## Error: The file uploaded is not in the expected format
Some tools have regional settings that create the CSV file with semi-colon as a delimiter. Please change the settings to ensure the delimiter is a comma.

## I imported a CSV but I see "Discovery is in progress"
This status appears if your CSV upload failed due to a validation failure. Try to import the CSV again. You can download the error report of the previous upload and follow the remediation guidance in the file to fix the errors. The error report can be downloaded from the 'Import Details' section on 'Discover machines' page.

## Do not see application details even after updating guest credentials
The application discovery runs once every 24 hours. If you would like to see the details immediately, refresh as follows. This may take a few minutes depending on the no. of VMs discovered.

1. In **Servers** > **Azure Migrate Server Assessment**, select **Overview**.
2. Under **Manage**, select **Agent Health**.
3. Select **Refresh agent**.
4. Wait for the refresh operation to complete. You should now see up-to-date information.

## Unable to export application inventory
Ensure the user downloading the inventory from the portal has Contributor privileges on the subscription.

## Common app discovery errors

Azure Migrate supports discovery of applications, roles, and features, using Azure Migrate: Server Assessment. App discovery is currently supported for VMware only. [Learn more](how-to-discover-applications.md) about the requirements and steps for setting up app discovery.

Typical app discovery errors are summarized in the table. 

**Error** | **Cause** | **Action**
--- | --- | --- | ---
10000: "Unable to discover the applications installed on the server" | This might occur if the machine operating system isn't Windows or Linux. | Only use app discovery for Windows/Linux.
10001: "Unable to retrieve the applications installed the server" | Internal error - some missing files in appliance. | Contact Microsoft Support.
10002: "Unable to retrieve the applications installed the server" | The discovery agent on the appliance might not be working properly. | If the issue doesn't resolve itself within 24 hours, contact support.
10003 "Unable to retrieve the applications installed the server" | The discovery agent on the appliance might not be working properly. | If the issue doesn't resolve itself within 24 hours, contact support.
10004: "Unable to discover installed applications for <Windows/Linux> machines" |  Credentials to access <Windows/Linux> machines weren't provided in the appliance.| Add a credential to the appliance that has access to the <Windows/Linux> machines.
10005: "Unable to access the on-premises server" | The access credentials might be wrong. | Update the appliance credentials make sure you can access the relevant machine with them. 
10006: "Unable to access the on-premises server" | This might occur if the machine operating system isn't Windows or Linux.|  Only use app discovery for Windows/Linux.
10007: "Unable to process the metadata retrieved" | This internal error occurred while trying to deserialize JSON | Contact Microsoft Support for a resolution
9000/9001/9002: "Unable to discover the applications installed on the server" | VMware tools might not be installed or is corrupted. | Install/reinstall VMware tools on the relevant machine, and check it's running.
9003: Unable to discover the applications installed on the server" | This might occur if the machine operating system isn't Windows or Linux. | Only use app discovery for Windows/Linux.
9004: "Unable to discover the applications installed on the server" | This might happen if the VM is powered off. | For discovery, make sure the VM is on.
9005: "Unable to discover the applications installed on the VM | This might occur if the machine operating system isn't Windows or Linux. | Only use app discovery for Windows/Linux.
9006/9007: "Unable to retrieve the applications installed the server" | The discovery agent on the appliance might not be working properly. | If the issue doesn't resolve itself within 24 hours, contact support
9008: "Unable to retrieve the applications installed the server". | Might be an internal error.  | Tf the issue doesn't resolve itself within 24 hours, contact support.
9009: "Unable to retrieve the applications installed the server" | Can occur if the Windows User Account Control (UAC) settings on the server are restrictive, and prevent discovery of installed applications. | Search for 'User Account Control' settings on the server, and configure the UAC setting on the server to one of the lower two levels.
9010: "VM is powered off" | VM is powered off.  | Ensure the VM is powered on.
9011: "File to download from guest is not found on the guest VM" | The issue can occur due to an internal error. | The issue should automatically get resolved in 24 hours. If the issue still persists, please contact Microsoft Support.
9012: "Result file contents are empty" | The issue can occur due to an internal error. | The issue should automatically get resolved in 24 hours. If the issue still persists, please contact Microsoft Support.
9013: "A new temporary profile is created for every login to the VMware VM" | A new temporary profile is created for every login into the VM | Ensure the user name provided in the guest VM credentials is in UPN format.
9014: "Unable to retrieve metadata from guest VM file system" | There was an issue connecting to the ESXi host | Ensure the appliance can connect to port 443 on the ESXi host running the VM
9015: "Unable to connect to VMware VMs due to insufficient privileges on vCenter" | Guest Operations role is not enabled on the vCenter user account | Ensure Guest Operations role is enabled on the vCenter user account.
9016: "Unable to connect to VMware VMs as the guest operations agent is out of data" | VMware tools is not properly installed or is not up to date. | Ensure the VMware  tools is properly installed and up to date.
9017: "File with discovered metadata is not found on the VM" | The issue can occur due to an internal error. | Contact Microsoft Support for a resolution.
9018: "PowerShell is not installed in the Guest VMs" | PowerShell is not available in the guest VM. | Install PowerShell in the guest VM.
9019: "Unable to discover due to guest VM operation failures" | VMware guest operation failed on the VM. | Ensure that the VM credentials are valid and user name provided in the guest VM credentials is in UPN format.
9020: "File creation permission is denied" | The role associated to the user or the group policy is restricting the user to create the file in the folder | Check if the guest user provided has create permission for the file in the folder. See **Notifications** in Server Assessment for the name of the folder.
9021: "File create permission is denied in folder System Temp Path" | VMware tool version on the VM is unsupported | Upgrade your VMware tool version above 10.2.0.
9022: "Get WMI object access is denied" | The role associated to the user or the group policy is restricting the user to access WMI object. | Please contact Microsoft Support.
9023: "SystemRoot environment variable value is empty" | Not known | Please contact Microsoft Support.
9024: "TEMP environment variable value is empty" | Not known | Please contact Microsoft Support.
9025: "PowerShell is corrupt in the Guest VMs" | Not known | Reinstall PowerShell in the guest VM and check if PowerShell can be run on the guest VM.
8084: "Unable to discover applications due to VMware error: <Exception from VMware>" | The Azure Migrate appliance uses VMware APIs to discover applications. This issue can happen if an exception is thrown by vCenter Server while trying to discover applications. The fault message from VMware is displayed in the error message shown in portal. | Search for the message in the [VMware documentation](https://pubs.vmware.com/vsphere-51/topic/com.vmware.wssdk.apiref.doc/index-faults.html), and follow the steps to fix. If you can't fix, contact Microsoft support.



## Next steps
Set up an appliance for [VMware](how-to-set-up-appliance-vmware.md), [Hyper-V](how-to-set-up-appliance-hyper-v.md), or [physical servers](how-to-set-up-appliance-physical.md).
