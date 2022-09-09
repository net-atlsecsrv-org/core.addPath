---
title: Configuration and management issues for Microsoft Azure Cloud Services FAQ| Microsoft Docs
description: This article lists the frequently asked questions about configuration and management for Microsoft Azure Cloud Services.
services: cloud-services
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/23/2018
ms.author: genli
---
# Configuration and management issues for Azure Cloud Services: Frequently asked questions (FAQs)

This article includes frequently asked questions about configuration and management issues for [Microsoft Azure Cloud Services](https://azure.microsoft.com/services/cloud-services). You can also consult the [Cloud Services VM Size page](cloud-services-sizes-specs.md) for size information.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

**Certificates**

- [Why is the certificate chain of my Cloud Service SSL certificate incomplete?](#why-is-the-certificate-chain-of-my-cloud-service-ssl-certificate-incomplete)
- [What is the purpose of the "Windows Azure Tools Encryption Certificate for Extensions"?](#what-is-the-purpose-of-the-windows-azure-tools-encryption-certificate-for-extensions)
- [How can I generate a Certificate Signing Request (CSR) without "RDP-ing" in to the instance?](#how-can-i-generate-a-certificate-signing-request-csr-without-rdp-ing-in-to-the-instance)
- [My Cloud Service Management Certificate is expiring. How to renew it?](#my-cloud-service-management-certificate-is-expiring-how-to-renew-it)
- [How to automate the installation of main SSL certificate(.pfx) and intermediate certificate(.p7b)?](#how-to-automate-the-installation-of-main-ssl-certificatepfx-and-intermediate-certificatep7b)
- [What is the purpose of the "Microsoft Azure Service Management for MachineKey" certificate?](#what-is-the-purpose-of-the-microsoft-azure-service-management-for-machinekey-certificate)

**Monitoring and logging**

- [What are the upcoming Cloud Service capabilities in the Azure portal which can help manage and monitor applications?](#what-are-the-upcoming-cloud-service-capabilities-in-the-azure-portal-which-can-help-manage-and-monitor-applications)
- [Why does IIS stop writing to the log directory?](#why-does-iis-stop-writing-to-the-log-directory)
- [How do I enable WAD logging for Cloud Services?](#how-do-i-enable-wad-logging-for-cloud-services)

**Network configuration**

- [How do I set the idle timeout for Azure load balancer?](#how-do-i-set-the-idle-timeout-for-azure-load-balancer)
- [How do I associate a static IP address to my Cloud Service?](#how-do-i-associate-a-static-ip-address-to-my-cloud-service)
- [What are the features and capabilities that Azure basic IPS/IDS and DDOS provides?](#what-are-the-features-and-capabilities-that-azure-basic-ipsids-and-ddos-provides)
- [How to enable HTTP/2 on Cloud Services VM?](#how-to-enable-http2-on-cloud-services-vm)

**Permissions**

- [Can Microsoft internal engineers remote desktop to Cloud Service instances without permission?](#can-microsoft-internal-engineers-remote-desktop-to-cloud-service-instances-without-permission)
- [I cannot remote desktop to Cloud Service VM  by using the RDP file. I get following error: An authentication error has occurred (Code: 0x80004005)](#i-cannot-remote-desktop-to-cloud-service-vm--by-using-the-rdp-file-i-get-following-error-an-authentication-error-has-occurred-code-0x80004005)

**Scaling**

- [I cannot scale beyond X instances](#i-cannot-scale-beyond-x-instances)
- [How can I configure Auto-Scale based on Memory metrics?](#how-can-i-configure-auto-scale-based-on-memory-metrics)

**Generic**

- [How do I add "nosniff" to my website?](#how-do-i-add-nosniff-to-my-website)
- [How do I customize IIS for a web role?](#how-do-i-customize-iis-for-a-web-role)
- [What is the quota limit for my Cloud Service?](#what-is-the-quota-limit-for-my-cloud-service)
- [Why does the drive on my Cloud Service VM show very little free disk space?](#why-does-the-drive-on-my-cloud-service-vm-show-very-little-free-disk-space)
- [How can I add an Antimalware extension for my Cloud Services in an automated way?](#how-can-i-add-an-antimalware-extension-for-my-cloud-services-in-an-automated-way)
- [How to enable Server Name Indication (SNI) for Cloud Services?](#how-to-enable-server-name-indication-sni-for-cloud-services)
- [How can I add tags to my Azure Cloud Service?](#how-can-i-add-tags-to-my-azure-cloud-service)
- [The Azure portal doesn't display the SDK version of my Cloud Service. How can I get that?](#the-azure-portal-doesnt-display-the-sdk-version-of-my-cloud-service-how-can-i-get-that)
- [I want to shut down the Cloud Service for several months. How to reduce the billing cost of Cloud Service without losing the IP address?](#i-want-to-shut-down-the-cloud-service-for-several-months-how-to-reduce-the-billing-cost-of-cloud-service-without-losing-the-ip-address)


## Certificates

### Why is the certificate chain of my Cloud Service SSL certificate incomplete?
    
We recommend that customers install the full certificate chain (leaf cert, intermediate certs, and root cert) instead of just the leaf certificate. When you install just the leaf certificate, you rely on Windows to build the certificate chain by walking the CTL. If intermittent network or DNS issues occur in Azure or Windows Update when Windows is trying to validate the certificate, the certificate may be considered invalid. By installing the full certificate chain, this problem can be avoided. The blog at [How to install a chained SSL certificate](https://blogs.msdn.microsoft.com/azuredevsupport/2010/02/24/how-to-install-a-chained-ssl-certificate/) shows how to do this.

### What is the purpose of the "Windows Azure Tools Encryption Certificate for Extensions"?

These certificates are automatically created whenever an extension is added to the Cloud Service. Most commonly, this is the WAD extension or the RDP extension, but it could be others, such as the Antimalware or Log Collector extension. These certificates are only used for encrypting and decrypting the private configuration for the extension. The expiration date is never checked, so it doesn’t matter if the certificate is expired. 

You can ignore these certificates. If you want to clean up the certificates, you can try deleting them all. Azure will throw an error if you try to delete a certificate that is in use.

### How can I generate a Certificate Signing Request (CSR) without "RDP-ing" in to the instance?

See the following guidance document:

[Obtaining a certificate for use with Windows Azure Web Sites (WAWS)](https://azure.microsoft.com/blog/obtaining-a-certificate-for-use-with-windows-azure-web-sites-waws/)

The CSR is just a text file. It does not have to be created from the machine where the certificate will ultimately be used. Although this document is written for an App Service, the CSR creation is generic and applies also for Cloud Services.

### My Cloud Service Management Certificate is expiring. How to renew it?

You can use following PowerShell commands to renew your Management Certificates:

    Add-AzureAccount
    Select-AzureSubscription -Current -SubscriptionName <your subscription name>
    Get-AzurePublishSettingsFile

The **Get-AzurePublishSettingsFile** will create a new management certificate in **Subscription** > **Management Certificates** in the Azure portal. The name of the new certificate looks like "YourSubscriptionNam]-[CurrentDate]-credentials".

### How to automate the installation of main SSL certificate(.pfx) and intermediate certificate(.p7b)?

You can automate this task by using a startup script (batch/cmd/PowerShell) and register that startup script in the service definition file. Add both the startup script and certificate(.p7b file) in the project folder of the same directory of the startup script.

### What is the purpose of the "Microsoft Azure Service Management for MachineKey" certificate?

This certificate is used to encrypt machine keys on Azure Web Roles. To learn more, check out [this advisory](https://docs.microsoft.com/security-updates/securityadvisories/2018/4092731).

For more information, see the following articles:
- [How to configure and run startup tasks for a Cloud Service](https://docs.microsoft.com/azure/cloud-services/cloud-services-startup-tasks)
- [Common Cloud Service startup tasks](https://docs.microsoft.com/azure/cloud-services/cloud-services-startup-tasks-common)

## Monitoring and logging

### What are the upcoming Cloud Service capabilities in the Azure portal which can help manage and monitor applications?

Ability to generate a new certificate for Remote Desktop Protocol (RDP) is coming soon. Alternatively, you can run this script:

```powershell
$cert = New-SelfSignedCertificate -DnsName yourdomain.cloudapp.net -CertStoreLocation "cert:\LocalMachine\My" -KeyLength 20 48 -KeySpec "KeyExchange"
$password = ConvertTo-SecureString -String "your-password" -Force -AsPlainText
Export-PfxCertificate -Cert $cert -FilePath ".\my-cert-file.pfx" -Password $password
```
Ability to choose blob or local for your csdef and cscfg upload location is coming soon. Using [New-AzureDeployment](/powershell/module/servicemanagement/azure/new-azuredeployment?view=azuresmps-4.0.0), you can set each location value.

Ability to monitor metrics at the instance level. Additional monitoring capabilities are available in [How to Monitor Cloud Services](cloud-services-how-to-monitor.md).

### Why does IIS stop writing to the log directory?
You have exhausted the local storage quota for writing to the log directory. To correct this, you can do one of three things:
* Enable diagnostics for IIS and have the diagnostics periodically moved to blob storage.
* Manually remove log files from the logging directory.
* Increase quota limit for local resources.

For more information, see the following documents:
* [Store and view diagnostic data in Azure Storage](cloud-services-dotnet-diagnostics-storage.md)
* [IIS Logs stop writing in Cloud Service](https://blogs.msdn.microsoft.com/cie/2013/12/21/iis-logs-stops-writing-in-cloud-service/)

### How do I enable WAD logging for Cloud Services?
You can enable Windows Azure Diagnostics (WAD) logging through following options:
1. [Enable from Visual Studio](https://docs.microsoft.com/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines#turn-on-diagnostics-in-cloud-service-projects-before-you-deploy-them)
2. [Enable through .NET code](https://docs.microsoft.com/azure/cloud-services/cloud-services-dotnet-diagnostics)
3. [Enable through Powershell](https://docs.microsoft.com/azure/cloud-services/cloud-services-diagnostics-powershell)

In order to get the current WAD settings of your Cloud Service, you can use [Get-AzureServiceDiagnosticsExtensions](https://docs.microsoft.com/azure/cloud-services/cloud-services-diagnostics-powershell#get-current-diagnostics-extension-configuration) ps cmd or you can view it through portal from “Cloud Services --> Extensions” blade.


## Network configuration

### How do I set the idle timeout for Azure load balancer?
You can specify the timeout in your service definition (csdef) file like this:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceDefinition name="mgVS2015Worker" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
  <WorkerRole name="WorkerRole1" vmsize="Small">
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" />
    </ConfigurationSettings>
    <Imports>
      <Import moduleName="RemoteAccess" />
      <Import moduleName="RemoteForwarder" />
    </Imports>
    <Endpoints>
      <InputEndpoint name="Endpoint1" protocol="tcp" port="10100"   idleTimeoutInMinutes="30" />
    </Endpoints>
  </WorkerRole>
```
See [New: Configurable Idle Timeout for Azure Load Balancer](https://azure.microsoft.com/blog/new-configurable-idle-timeout-for-azure-load-balancer/) for more information.

### How do I associate a static IP address to my Cloud Service?
To set up a static IP address, you need to create a reserved IP. This reserved IP can be associated to a new Cloud Service or to an existing deployment. See the following documents for details:
* [How to create a reserved IP address](../virtual-network/virtual-networks-reserved-public-ip.md#manage-reserved-vips)
* [Reserve the IP address of an existing Cloud Service](../virtual-network/virtual-networks-reserved-public-ip.md#reserve-the-ip-address-of-an-existing-cloud-service)
* [Associate a reserved IP to a new Cloud Service](../virtual-network/virtual-networks-reserved-public-ip.md#associate-a-reserved-ip-to-a-new-cloud-service)
* [Associate a reserved IP to a running deployment](../virtual-network/virtual-networks-reserved-public-ip.md#associate-a-reserved-ip-to-a-running-deployment)
* [Associate a reserved IP to a Cloud Service by using a service configuration file](../virtual-network/virtual-networks-reserved-public-ip.md#associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file)

### What are the features and capabilities that Azure basic IPS/IDS and DDOS provides?
Azure has IPS/IDS in datacenter physical servers to defend against threats. In addition, customers can deploy third-party security solutions, such as web application firewalls, network firewalls, antimalware, intrusion detection, prevention systems (IDS/IPS), and more. For more information, see [Protect your data and assets and comply with global security standards](https://www.microsoft.com/en-us/trustcenter/Security/AzureSecurity).

Microsoft continuously monitors servers, networks, and applications to detect threats. Azure's multipronged threat-management approach uses intrusion detection, distributed denial-of-service (DDoS) attack prevention, penetration testing, behavioral analytics, anomaly detection, and machine learning to constantly strengthen its defense and reduce risks. Microsoft Antimalware for Azure protects Azure Cloud Services and virtual machines. You have the option to deploy third-party security solutions in addition, such as web application fire walls, network firewalls, antimalware, intrusion detection and prevention systems (IDS/IPS), and more.

### How to enable HTTP/2 on Cloud Services VM?

Windows 10 and Windows Server 2016 come with support for HTTP/2 on both client and server side. If your client (browser) is connecting to the IIS server over TLS that negotiates HTTP/2 via TLS extensions, then you do not need to make any change on the server-side. This is because, over TLS, the h2-14 header specifying use of HTTP/2 is sent by default. If on the other hand your client is sending an Upgrade header to upgrade to HTTP/2, then you need to make the change below on the server side to ensure that the Upgrade works and you end up with an HTTP/2 connection. 

1. Run regedit.exe.
2. Browse to registry key: HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\HTTP\Parameters.
3. Create a new DWORD value named **DuoEnabled**.
4. Set its value to 1.
5. Restart your server.
6. Go to your **Default Web Site** and under **Bindings**, create a new TLS binding with the self-signed certificate just created. 

For more information, see:

- [HTTP/2 on IIS](https://blogs.iis.net/davidso/http2)
- [Video: HTTP/2 in Windows 10: Browser, Apps and Web Server](https://channel9.msdn.com/Events/Build/2015/3-88)
         

These steps could be automated via a startup task, so that whenever a new PaaS instance gets created, it can do the changes above in the system registry. For more information, see [How to configure and run startup tasks for a Cloud Service](cloud-services-startup-tasks.md).

 
Once this has been done, you can verify whether the HTTP/2 has been enabled or not by using one of the following methods:

- Enable Protocol version in IIS logs and look into the IIS logs. It will show HTTP/2 in the logs. 
- Enable F12 Developer Tool in Internet Explorer or Microsoft Edge and switch to the Network tab to verify the protocol. 

For more information, see [HTTP/2 on IIS](https://blogs.iis.net/davidso/http2).

## Permissions

### How can I implement role-based access for Cloud Services?
Cloud Services doesn't support the role-based access control (RBAC) model, as it's not an Azure Resource Manager based service.

See [Understand the different roles in Azure](../role-based-access-control/rbac-and-directory-admin-roles.md).

## Remote desktop

### Can Microsoft internal engineers remote desktop to Cloud Service instances without permission?
Microsoft follows a strict process that will not allow internal engineers to remote desktop into your Cloud Service without written permission (email or other written communication) from the owner or their designee.

### I cannot remote desktop to Cloud Service VM  by using the RDP file. I get following error: An authentication error has occurred (Code: 0x80004005)

This error may occur if you use the RDP file from a machine that is joined to Azure Active Directory. To resolve this issue, follow these steps:

1. Right-click the RDP file you downloaded and then select **Edit**.
2. Add "&#92;" as prefix before the username. For example, use **.\username** instead of  **username**.

## Scaling

### I cannot scale beyond X instances
Your Azure Subscription has a limit on the number of cores you can use. Scaling will not work if you have used all the cores available. For example, if you have a limit of 100 cores, this means you could have 100 A1 sized virtual machine instances for your Cloud Service, or 50 A2 sized virtual machine instances.

### How can I configure Auto-Scale based on Memory metrics?

Auto-scale based on Memory metrics for a Cloud Services is not currently supported. 

To work around this problem, you can use Application Insights. Auto-Scale supports Application Insights as a Metrics Source and can scale the role instance count based on guest metric like "Memory".  You have to configure Application Insights in your Cloud Service project package file (*.cspkg) and enable Azure Diagnostics extension on the service to implement this feat.

For more details on how to utilize a custom metric via Application Insights to configure Auto-Scale on  Cloud Services, see [Get started with auto scale by custom metric in Azure](../azure-monitor/platform/autoscale-custom-metric.md)

For more information on how to integrate Azure Diagnostics with Application Insights for Cloud Services, see [Send Cloud Service, Virtual Machine, or Service Fabric diagnostic data to Application Insights](../azure-monitor/platform/diagnostics-extension-to-application-insights.md)

For more information about to enable Application Insights for Cloud Services, see [Application Insights for Azure Cloud Services](https://docs.microsoft.com/azure/application-insights/app-insights-cloudservices)

For more information about how to enable Azure Diagnostics Logging for Cloud Services, see [Set up diagnostics for Azure Cloud Services and virtual machines](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines#turn-on-diagnostics-in-cloud-service-projects-before-you-deploy-them)

## Generic

### How do I add "nosniff" to my website?
To prevent clients from sniffing the MIME types, add a setting in your *web.config* file.

```xml
<configuration>
   <system.webServer>
      <httpProtocol>
         <customHeaders>
            <add name="X-Content-Type-Options" value="nosniff" />
         </customHeaders>
      </httpProtocol>
   </system.webServer>
</configuration>
```

You can also add this as a setting in IIS. Use the following command with the [common startup tasks](cloud-services-startup-tasks-common.md#configure-iis-startup-with-appcmdexe) article.

```cmd
%windir%\system32\inetsrv\appcmd set config /section:httpProtocol /+customHeaders.[name='X-Content-Type-Options',value='nosniff']
```

### How do I customize IIS for a web role?
Use the IIS startup script from the [common startup tasks](cloud-services-startup-tasks-common.md#configure-iis-startup-with-appcmdexe) article.

### What is the quota limit for my Cloud Service?
See [Service-specific limits](../azure-subscription-service-limits.md#subscription-limits).

### Why does the drive on my Cloud Service VM show very little free disk space?
This is expected behavior, and it shouldn't cause any issue to your application. Journaling is turned on for the %approot% drive in Azure PaaS VMs, which essentially consumes double the amount of space that files normally take up. However there are several things to be aware of that essentially turn this into a non-issue.

The %approot% drive size is calculated as \<size of .cspkg + max journal size + a margin of free space>, or 1.5 GB, whichever is larger. The size of your VM has no bearing on this calculation. (The VM size only affects the size of the temporary C: drive.) 

It is unsupported to write to the %approot% drive. If you are writing to the Azure VM, you must do so in a temporary LocalStorage resource (or other option, such as Blob storage, Azure Files, etc.). So the amount of free space on the %approot% folder is not meaningful. If you are not sure if your application is writing to the %approot% drive, you can always let your service run for a few days and then compare the "before" and "after" sizes. 

Azure will not write anything to the %approot% drive. Once the VHD is created from your .cspkg and mounted into the Azure VM, the only thing that might write to this drive is your application. 

The journal settings are non-configurable, so you can't turn it off.

### How can I add an Antimalware extension for my Cloud Services in an automated way?

You can enable Antimalware extension using PowerShell script in the Startup Task. Follow the steps in these articles to implement it: 
 
- [Create a PowerShell startup task](cloud-services-startup-tasks-common.md#create-a-powershell-startup-task)
- [Set-AzureServiceAntimalwareExtension](https://docs.microsoft.com/powershell/module/servicemanagement/azure/Set-AzureServiceAntimalwareExtension?view=azuresmps-4.0.0 )

For more information about Antimalware deployment scenarios and how to enable it from the portal, see [Antimalware Deployment Scenarios](../security/fundamentals/antimalware.md#antimalware-deployment-scenarios).

### How to enable Server Name Indication (SNI) for Cloud Services?

You can enable SNI in Cloud Services by using one of the following methods:

**Method 1: Use PowerShell**

The SNI binding can be configured using the PowerShell cmdlet **New-WebBinding** in a startup task for a Cloud Service role instance as below:
    
    New-WebBinding -Name $WebsiteName -Protocol "https" -Port 443 -IPAddress $IPAddress -HostHeader $HostHeader -SslFlags $sslFlags 
    
As described [here](https://technet.microsoft.com/library/ee790567.aspx), the $sslFlags could be one of the values as the following:

|Value|Meaning|
------|------
|0|No SNI|
|1|SNI Enabled|
|2|Non SNI binding which uses Central Certificate Store|
|3|SNI binding which uses Central Certificate store|
 
**Method 2: Use code**

The SNI binding could also be configured via code in the role startup as described on this [blog post](https://blogs.msdn.microsoft.com/jianwu/2014/12/17/expose-ssl-service-to-multi-domains-from-the-same-cloud-service/):

    
    //<code snip> 
                    var serverManager = new ServerManager(); 
                    var site = serverManager.Sites[0]; 
                    var binding = site.Bindings.Add(":443:www.test1.com", newCert.GetCertHash(), "My"); 
                    binding.SetAttributeValue("sslFlags", 1); //enables the SNI 
                    serverManager.CommitChanges(); 
    //</code snip> 
    
Using any of the approaches above, the respective certificates (*.pfx) for the specific hostnames have to be first installed on the role instances using a startup task or via code in order for the SNI binding to be effective.

### How can I add tags to my Azure Cloud Service? 

Cloud Service is a Classic resource. Only resources created through Azure Resource Manager support tags. You cannot apply tags to Classic resources such as Cloud Service. 

### The Azure portal doesn't display the SDK version of my Cloud Service. How can I get that?

We are working on bringing this feature on the Azure portal. Meanwhile, you can use following PowerShell commands to get the SDK version:

    Get-AzureService -ServiceName "<Cloud Service name>" | Get-AzureDeployment | Where-Object -Property SdkVersion -NE -Value "" | select ServiceName,SdkVersion,OSVersion,Slot

### I want to shut down the Cloud Service for several months. How to reduce the billing cost of Cloud Service without losing the IP address?

An already deployed Cloud Service gets billed for the Compute and Storage it uses. So even if you shut down the Azure VM, you will still get billed for the Storage. 

Here is what you can do to reduce your billing without losing the IP address for your service:

1. [Reserve the IP address](../virtual-network/virtual-networks-reserved-public-ip.md) before you delete the deployments.  You will only be billed for this IP address. For more information about IP address billing, see [IP addresses pricing](https://azure.microsoft.com/pricing/details/ip-addresses/).
2. Delete the deployments. Don’t delete the xxx.cloudapp.net, so that you can use it for future.
3. If you want to redeploy the Cloud Service by using the same reserve IP that you reserved in your subscription, see [Reserved IP addresses for Cloud Services and Virtual Machines](https://azure.microsoft.com/blog/reserved-ip-addresses/).

