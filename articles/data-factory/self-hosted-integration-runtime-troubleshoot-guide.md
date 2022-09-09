---
title: Troubleshoot self-hosted integration runtime in Azure Data Factory
description: Learn how to troubleshoot self-hosted integration runtime issues in Azure Data Factory. 
services: data-factory
author: lrtoyou1223
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 11/17/2020
ms.author: lle
---

# Troubleshoot self-hosted integration runtime

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

This article explores common troubleshooting methods for self-hosted integration runtime (IR) in Azure Data Factory.

## Gather self-hosted IR logs from Azure Data Factory

For failed activities that are running on a self-hosted IR or a shared IR, Azure Data Factory supports viewing and uploading error logs. To get the error report ID, follow the instructions here, and then enter the report ID to search for related known issues.

1. In Data Factory, select **Pipeline runs**.

1. Under **Activity runs**, in the **Error** column, select the highlighted button to display the activity logs, as shown in the following screenshot:

    ![Screenshot of the "Activity runs" section on the "All pipeline runs" pane.](media/self-hosted-integration-runtime-troubleshoot-guide/activity-runs-page.png)

    The activity logs are displayed for the failed activity run.

    ![Screenshot of the activity logs for the failed activity.](media/self-hosted-integration-runtime-troubleshoot-guide/send-logs.png) 
    
1. For further assistance, select **Send logs**.
 
   The **Share the self-hosted integration runtime (IR) logs with Microsoft** window opens.

    ![Screenshot of the "Share the self-hosted integration runtime (IR) logs with Microsoft" window.](media/self-hosted-integration-runtime-troubleshoot-guide/choose-logs.png)

1. Select which logs you want to send. 
    * For a *self-hosted IR*, you can upload logs that are related to the failed activity or all logs on the self-hosted IR node. 
    * For a *shared IR*, you can upload only logs that are related to the failed activity.

1. When the logs are uploaded, keep a record of the Report ID for later use if you need further assistance to solve the issue.

    ![Screenshot of the displayed report ID in the upload progress window for the IR logs.](media/self-hosted-integration-runtime-troubleshoot-guide/upload-logs.png)

> [!NOTE]
> Log viewing and uploading requests are executed on all online self-hosted IR instances. If any logs are missing, make sure that all the self-hosted IR instances are online. 


## Self-hosted IR general failure or error

### Out of memory issue

#### Symptoms

An OutOfMemoryException (OOM) error occurs when you try to run a lookup activity with a linked IR or a self-hosted IR.

#### Cause

A new activity can throw an OOM error if the IR machine experiences momentary high memory usage. The issue might be caused by a large volume of concurrent activity, and the error is by design.

#### Resolution

Check the resource usage and concurrent activity execution on the IR node. Adjust the internal and trigger time of activity runs to avoid too much execution on a single IR node at the same time.


### SSL/TLS certificate issue

#### Symptoms

When you try to enable a Secure Sockets Layer (SSL)/Transport Layer Security (TLS) certificate (advanced) by choosing the certificate (after you select **Self-hosted IR Configuration Manager** > **Remote access from intranet**), you get the following error:

"Remote access settings are invalid. Identity check failed for outgoing message. The expected DNS identity of the remote endpoint was 'abc.microsoft.com' but the remote endpoint provided DNS claim 'microsoft.com'. If this is a legitimate remote endpoint, you can fix the problem by explicitly specifying DNS identity 'microsoft.com' as the Identity property of EndpointAddress when creating channel proxy."

In the preceding example, the chosen certificate has "microsoft.com" appended to it.

#### Cause

This is a known issue in Windows Communication Foundation (WCF). The WCF SSL/TLS validation checks only for the last DNSName in the **Subject Alternative Name** (SAN) field. 

#### Resolution

A wildcard certificate is supported in the Azure Data Factory v2 self-hosted IR. This issue normally happens because the SSL certificate is incorrect. The last DNSName in the SAN should be valid. 

To verify and correct the DNSName, do the following: 

1. Open Management Console.
1. Under **Certificate Details**, double-check the value in both the **Subject** and **Subject Alternative Name** boxes. For example, "DNS Name= microsoft.com.com" is not a valid name.
1. Contact the certificate issuing company to have the incorrect DNSName removed.

### Concurrent jobs limit issue

#### Symptoms

When you try to increase the concurrent jobs limit from the Azure Data Factory interface, the process hangs in *Updating* status.

Example scenario: The maximum concurrent jobs value is currently set to 24, and you want to increase the count so that your jobs can run faster. The minimum value that you can enter is 3, and the maximum value is 32. You increase the value from 24 to 32 and then select the **Update** button. The process gets stuck in *Updating* status, as shown in the following screenshot. You refresh the page, and the value is still displayed as 24. It hasn't been updated to 32 as you had expected.

![Screenshot of the Nodes pane of the integration runtime, displaying the process stuck in "Updating" status.](media/self-hosted-integration-runtime-troubleshoot-guide/updating-status.png)

#### Cause

The limit on the number of concurrent jobs depends on the computer's logic core and memory. Try to adjust the value downward to a value such as 24, and then view the result.

> [!TIP] 
> -	To learn more about logic core count and to determine your machine's logic core count, see [Four ways to find the number of cores in your CPU on Windows 10](https://www.top-password.com/blog/find-number-of-cores-in-your-cpu-on-windows-10/).
> -	To learn how to calculate the math.log, go to the [Logarithm calculator](https://www.rapidtables.com/calc/math/Log_Calculator.html).


### Self-hosted IR high availability (HA) SSL certificate issue

#### Symptoms

The self-hosted IR work node has reported the following error:

"Failed to pull shared states from primary node net.tcp://abc.cloud.corp.Microsoft.com:8060/ExternalService.svc/. Activity ID: XXXXX The X.509 certificate CN=abc.cloud.corp.Microsoft.com, OU=test, O=Microsoft chain building failed. The certificate that was used has a trust chain that cannot be verified. Replace the certificate or change the certificateValidationMode. The revocation function was unable to check revocation because the revocation server was offline."

#### Cause

When you handle cases that are related to an SSL/TLS handshake, you might encounter some issues related to certificate chain verification. 

#### Resolution

- Here's a quick, intuitive way to troubleshoot an X.509 certificate chain build failure:
 
    1. Export the certificate, which needs to be verified. To do so, do the following:
    
       a. In Windows, select **Start**, start typing **certificates**, and then select **Manage computer certificates**.
       
       b. In File Explorer, on the left pane, search for the certificate that you want to check, right-click it, and then select **All tasks** > **Export**.
    
        ![Screenshot of the "All Tasks" > "Export" control for a certificate on the "Manage computer certificates" pane.](media/self-hosted-integration-runtime-troubleshoot-guide/export-tasks.png)

    2. Copy the exported certificate to the client machine. 
    3. On the client side, in a Command Prompt window, run the following command. Be sure to replace *\<certificate path>* and *\<output txt file path>* with the actual paths.
    
        ```
        Certutil -verify -urlfetch    <certificate path>   >     <output txt file path> 
        ```

        For example:

        ```
        Certutil -verify -urlfetch c:\users\test\desktop\servercert02.cer > c:\users\test\desktop\Certinfo.txt
        ```
    4. Check for errors in the output TXT file. You can find the error summary at the end of the TXT file.

        For example: 

        ![Screenshot of an error summary at the end of the TXT file.](media/self-hosted-integration-runtime-troubleshoot-guide/error-summary.png)

        If you don't see an error at the end of the log file, as shown in the following screenshot, you can consider that the certificate chain has been built successfully on the client machine.
        
        ![Screenshot of a log file showing no errors.](media/self-hosted-integration-runtime-troubleshoot-guide/log-file.png)      

- If an AIA (Authority Information Access), CDP (CRL Distribution Point), or OCSP (Online Certificate Status Protocol) file name extension is configured in the certificate file, you can check it in a more intuitive way:
 
    1. Get this information by checking the certificate details, as shown in the following screenshot:
    
        ![Screenshot of certificate details.](media/self-hosted-integration-runtime-troubleshoot-guide/certificate-detail.png)
    
    1. Run the following command. Be sure to replace *\<certificate path>* with the actual path of the certificate.
    
        ```
          Certutil   -URL    <certificate path> 
        ```
    
        The URL Retrieval tool opens. 
        
    1. To verify certificates with AIA, CDP, and OCSP file name extensions, select **Retrieve**.

        ![Screenshot of the URL Retrieval Tool and the Retrieve button.](media/self-hosted-integration-runtime-troubleshoot-guide/retrieval-button.png)
 
        You've built the certificate chain successfully if the certificate status from AIA is *Verified* and the certificate status from CDP or OCSP is *Verified*.

        If you fail when you try to retrieve AIA or CDP, work with your network team to get the client machine ready to connect to the target URL. It will be enough if either the HTTP path or the Lightweight Directory Access Protocol (LDAP) path can be verified.

### Self-hosted IR could not load file or assembly

#### Symptoms

You get the following error message:

"Could not load file or assembly 'XXXXXXXXXXXXXXXX, Version=4.0.2.0, Culture=neutral, PublicKeyToken=XXXXXXXXX' or one of its dependencies. The system cannot find the file specified. Activity ID: 92693b45-b4bf-4fc8-89da-2d3dc56f27c3"
 
Here is a more specific error message: 

"Could not load file or assembly 'System.ValueTuple, Version=4.0.2.0, Culture=neutral, PublicKeyToken=XXXXXXXXX' or one of its dependencies. The system cannot find the file specified. Activity ID: 92693b45-b4bf-4fc8-89da-2d3dc56f27c3"

#### Cause

In Process Monitor, you can view the following result:

[![Screenshot of the Paths list in Process Monitor.](media/self-hosted-integration-runtime-troubleshoot-guide/process-monitor.png)](media/self-hosted-integration-runtime-troubleshoot-guide/process-monitor.png#lightbox)

> [!TIP] 
> In Process Monitor, you can set filters as shown in following screenshot.
>
> The preceding error message says that the DLL System.ValueTuple is not located in the related *Global Assembly Cache* (GAC) folder, in the *C:\Program Files\Microsoft Integration Runtime\4.0\Gateway* folder, or in the *C:\Program Files\Microsoft Integration Runtime\4.0\Shared* folder.
>
> Basically, the process loads the DLL first from the *GAC* folder, then from the *Shared* folder, and finally from the *Gateway* folder. Therefore, you can load the DLL from any path that's helpful.

<br>

![Screenshot of the "Process Monitor Filter" page, listing the filters for the DLL.](media/self-hosted-integration-runtime-troubleshoot-guide/set-filters.png)

#### Resolution

You'll find the *System.ValueTuple.dll* file in the *C:\Program Files\Microsoft Integration Runtime\4.0\Gateway\DataScan* folder. To resolve the issue, copy the *System.ValueTuple.dll* file to the *C:\Program Files\Microsoft Integration Runtime\4.0\Gateway* folder.

You can use the same method to resolve other missing file or assembly issues.

#### More information about this issue

The reason why you see the *System.ValueTuple.dll* under *%windir%\Microsoft.NET\assembly* and *%windir%\assembly* is that this is a .NET behavior. 

In the following error, you can clearly see that the *System.ValueTuple* assembly is missing. This issue arises when the application tries to check the *System.ValueTuple.dll* assembly.
 
"\<LogProperties>\<ErrorInfo>[{"Code":0,"Message":"The type initializer for 'Npgsql.PoolManager' threw an exception.","EventType":0,"Category":5,"Data":{},"MsgId":null,"ExceptionType":"System.TypeInitializationException","Source":"Npgsql","StackTrace":"","InnerEventInfos":[{"Code":0,"Message":"Could not load file or assembly 'System.ValueTuple, Version=4.0.2.0, Culture=neutral, PublicKeyToken=XXXXXXXXX' or one of its dependencies. The system cannot find the file specified.","EventType":0,"Category":5,"Data":{},"MsgId":null,"ExceptionType":"System.IO.FileNotFoundException","Source":"Npgsql","StackTrace":"","InnerEventInfos":[]}]}]\</ErrorInfo>\</LogProperties>"
 
For more information about GAC, see [Global Assembly Cache](https://docs.microsoft.com/dotnet/framework/app-domains/gac).


### Self-hosted integration runtime Authentication Key is missing

#### Symptoms

The self-hosted integration runtime suddenly goes offline without an Authentication Key, and the Event Log displays the following error message: 

"Authentication Key is not assigned yet"

![Screenshot of the integration runtime event pane showing that the Authentication Key is not yet assigned.](media/self-hosted-integration-runtime-troubleshoot-guide/key-missing.png)

#### Cause

- The self-hosted IR node or logical self-hosted IR in the Azure portal was deleted.
- A clean uninstall was performed.

#### Resolution

If neither of the preceding causes applies, you can go to the *%programdata%\Microsoft\Data Transfer\DataManagementGateway* folder to see whether the *Configurations* file has been deleted. If it was deleted, follow the instructions in the Netwrix article [Detect who deleted a file from your Windows file servers](https://www.netwrix.com/how_to_detect_who_deleted_file.html).

![Screenshot of the event log details pane for checking the Configurations file.](media/self-hosted-integration-runtime-troubleshoot-guide/configurations-file.png)


### Can't use self-hosted IR to bridge two on-premises datastores

#### Symptoms

After you create self-hosted IRs for both the source and destination datastores, you want to connect the two IRs to finish a copy activity. If the datastores are configured in different virtual networks, or the datastores can't understand the gateway mechanism, you receive either of the following errors: 

* "The driver of source cannot be found in destination IR"
* "The source cannot be accessed by the destination IR"
 
#### Cause

The self-hosted IR is designed as a central node of a copy activity, not a client agent that needs to be installed for each datastore.
 
In this case, you should create the linked service for each datastore with the same IR, and the IR should be able to access both datastore through the network. It doesn't matter whether the IR is installed at the source datastore or the destination datastore, or on a third machine. If two linked services are created with different IRs but used in the same copy activity, the destination IR is used, and you need to install the drivers for both datastores on the destination IR machine.

#### Resolution

Install drivers for both the source and destination datastores on the destination IR, and make sure that it can access the source datastore.
 
If the traffic can't pass through the network between two datastores (for example, they're configured in two virtual networks), you might not finish copying in one activity even with the IR installed. If you can't finish copying in a single activity, you can create two copy activities with two IRs, each in a VENT: 
* Copy one IR from datastore 1 to Azure Blob Storage
* Copy another IR from Azure Blob Storage to ddatastore 2. 

This solution could simulate the requirement to use the IR to create a bridge that connects two disconnected datastores.


### Credential sync issue causes credential loss from HA

#### Symptoms

If the data source credential "XXXXXXXXXX" is deleted from the current integration runtime node with payload, you receive the following error message:

"When you delete the link service on Azure portal, or the task has the wrong payload, please create new link service with your credential again."

#### Cause

Your self-hosted IR is built in HA mode with two nodes, but the nodes aren't in a credentials sync state. This means that the credentials stored in the dispatcher node aren't synced to other worker nodes. If any failover happens from the dispatcher node to the worker node, and the credentials exist only in the previous dispatcher node, the task will fail when you're trying to access credentials, and you'll receive the preceding error.

#### Resolution

The only way to avoid this issue is to make sure that the two nodes are in credentials sync state. If they aren't in sync, you have to reenter the credentials for the new dispatcher.


### Can't choose the certificate because the private key is missing

#### Symptoms

* You've imported a PFX file to the certificate store.
* When you selected the certificate through the IR Configuration Manager UI, you received the following error message:

   "Failed to change intranet communication encryption mode. It is likely that certificate '\<*certificate name*>' may not have a private key that is capable of key exchange or the process may not have access rights for the private key. Please see inner exception for detail."

    ![Screenshot of the Integration Runtime Configuration Manager Settings pane, displaying a "private key missing" error message.](media/self-hosted-integration-runtime-troubleshoot-guide/private-key-missing.png)

#### Cause

- The user account has a low privilege level and can't access the private key.
- The certificate was generated as a signature but not as a key exchange.

#### Resolution

* To operate the UI, use an account with appropriate privileges for accessing the private key.  
* Import the certificate by running the following command:
    
    ```
    certutil -importpfx FILENAME.pfx AT_KEYEXCHANGE
    ```

## Self-hosted IR setup

### Integration runtime registration error 

#### Symptoms

You might occasionally want to run a self-hosted IR in a different account for either of the following reasons:
- Company policy disallows the service account.
- Some authentication is required.

After you change the service account on the service pane, you might find that the integration runtime stops working, and you get the following error message:

"The Integration Runtime (Self-hosted) node has encountered an error during registration. Cannot connect to the Integration Runtime (Self-hosted) Host Service."

![Screenshot of the Integration Runtime Configuration Manager window, showing an IR registration error.](media/self-hosted-integration-runtime-troubleshoot-guide/ir-registration-error.png)

#### Cause

Many resources are granted only to the service account. When you change the service account to another account, the permissions of all dependent resources remain unchanged.

#### Resolution

Go to the integration runtime event log to check the error.

![Screenshot of the IR event log, showing that a runtime error has occurred.](media/self-hosted-integration-runtime-troubleshoot-guide/ir-event-log.png)

* If the error in the event log is "UnauthorizedAccessException," do the following:

    1. Check the *DIAHostService* logon service account in the Windows service panel.

        ![Screenshot of the Logon service account properties pane.](media/self-hosted-integration-runtime-troubleshoot-guide/logon-service-account.png)

    1. Check to see whether the logon service account has read/write permissions for the *%programdata%\Microsoft\DataTransfer\DataManagementGateway* folder.

        - By default, if the service logon account hasn't been changed, it should have read/write permissions.

            ![Screenshot of the service permissions pane.](media/self-hosted-integration-runtime-troubleshoot-guide/service-permission.png)

        - If you've changed the service logon account, mitigate the issue by doing the following:
 
            a. Perform a clean uninstallation of the current self-hosted IR.   
            b. Install the self-hosted IR bits.  
            c. Change the service account by doing the following:  

             i. Go to the self-hosted IR installation folder, and then switch to the *Microsoft Integration Runtime\4.0\Shared* folder.  
             ii. Open a Command Prompt window by using elevated privileges. Replace *\<user>* and *\<password>* with your own username and password, and then run the following command:   
                `dmgcmd.exe -SwitchServiceAccount "<user>" "<password>"`  
             iii. If you want to change to the LocalSystem account, be sure to use the correct format for this account: `dmgcmd.exe -SwitchServiceAccount "NT Authority\System" ""`  
                Do *not* use this format: `dmgcmd.exe -SwitchServiceAccount "LocalSystem" ""`     
             iv. Optionally, because Local System has higher privileges than Administrator, you can also directly change it in "Services".  
             v. You can use a local/domain user for the IR service logon account.            

            d. Register the integration runtime.

* If the error is "Service 'Integration Runtime Service' (DIAHostService) failed to start. Verify that you have sufficient privileges to start system services," do the following:

    1. Check the *DIAHostService* logon service account in the Windows service panel.
    
        ![Screenshot of the "Log On" pane for the service account.](media/self-hosted-integration-runtime-troubleshoot-guide/logon-service-account.png)

    1. Check to see whether the logon service account has **Log on as a service** permissions to start the Windows service:

        ![Screenshot of the "Log on as service" properties pane.](media/self-hosted-integration-runtime-troubleshoot-guide/logon-as-service.png)

#### More information

If neither of the preceding two resolution patterns applies in your case, try to collect the following Windows event logs: 
- Applications and Services Logs > Integration Runtime
- Windows Logs > Application

### Can't find the Register button to register a self-hosted IR    

#### Symptoms

When you register a self-hosted IR, the **Register** button isn't displayed on the Configuration Manager pane.

![Screenshot of the Configuration Manager pane, displaying a message that the integration runtime node is not registered.](media/self-hosted-integration-runtime-troubleshoot-guide/no-register-button.png)

#### Cause

As of the release of Integration Runtime 3.0, the **Register** button on existing integration runtime nodes has been removed to enable a cleaner and more secure environment. If a node has been registered to an integration runtime, whether it's online or not, re-register it to another integration runtime by uninstalling the previous node, and then install and register the node.

#### Resolution

1. In Control Panel, uninstall the existing integration runtime.

    > [!IMPORTANT] 
    > In the following process, select **Yes**. Do not keep data during the uninstallation process.

    ![Screenshot of the "Yes" button for deleting all user data from the integration runtime.](media/self-hosted-integration-runtime-troubleshoot-guide/delete-data.png)

1. If you don't have the integration runtime installer MSI file, go to [download center](https://www.microsoft.com/en-sg/download/details.aspx?id=39717) to download the latest integration runtime.
1. Install the MSI file, and register the integration runtime.


### Unable to register the self-hosted IR because of localhost    

#### Symptoms

You're unable to register the self-hosted IR on a new machine when you use get_LoopbackIpOrName.

**Debug:**
A runtime error has occurred.
The type initializer for 'Microsoft.DataTransfer.DIAgentHost.DataSourceCache' threw an exception.
A non-recoverable error occurred during a database lookup.
 
**Exception detail:**
System.TypeInitializationException: The type initializer for 'Microsoft.DataTransfer.DIAgentHost.DataSourceCache' threw an exception. ---> System.Net.Sockets.SocketException: A non-recoverable error occurred during a database lookup at System.Net.Dns.GetAddrInfo(String name).

#### Cause

The issue usually occurs when the localhost is being resolved.

#### Resolution

Use localhost IP address 127.0.0.1 to host the file and resolve the issue.

### Self-hosted setup failed    

#### Symptoms

You're unable to uninstall an existing IR, install a new IR, or upgrade an existing IR to a new IR.

#### Cause

The integration runtime installation depends on the Windows Installer service. You might experience installation problems for the following reasons:
- Insufficient available disk space.
- Lack of permissions.
- The Windows NT service is locked.
- CPU utilization is too high.
- The MSI file is hosted in a slow network location.
- Some system files or registries were touched unintentionally.

### The IR service account failed to fetch certificate access

#### Symptoms

When you install a self-hosted IR via Microsoft Integration Runtime Configuration Manager, a certificate with a trusted certificate authority (CA) is generated. The certificate couldn't be applied to encrypt communication between two nodes, and the following error message is displayed: 

"Failed to change Intranet communication encryption mode: Failed to grant Integration Runtime service account the access of to the certificate '\<*certificate name*>'. Error code 103"

![Screenshot displaying the error message "... Failed to grant Integration Runtime service account certificate access".](media/self-hosted-integration-runtime-troubleshoot-guide/integration-runtime-service-account-certificate-error.png)

#### Cause

The certificate is using key storage provider (KSP) storage, which is not supported yet. To date, self-hosted IR supports only cryptographic service provider (CSP) storage.

#### Resolution

We recommend that you use CSP certificates in this case.

**Solution 1** 

To import the certificate, run the following command:

`Certutil.exe -CSP "CSP or KSP" -ImportPFX FILENAME.pfx`

![Screenshot of the certutil command for importing the certificate.](media/self-hosted-integration-runtime-troubleshoot-guide/use-certutil.png)

**Solution 2** 

To convert the certificate, run the following commands:

`openssl pkcs12 -in .\xxxx.pfx -out .\xxxx_new.pem -password pass: <EnterPassword>`
`openssl pkcs12 -export -in .\xxxx_new.pem -out xxxx_new.pfx`

Before and after conversion:

![Screenshot of the result before the certificate conversion.](media/self-hosted-integration-runtime-troubleshoot-guide/before-certificate-change.png)

![Screenshot of the result after the certificate conversion.](media/self-hosted-integration-runtime-troubleshoot-guide/after-certificate-change.png)

### Self-hosted integration runtime version 5.x
For the upgrade to version 5.x of the Azure Data Factory self-hosted integration runtime, we require **.NET Framework Runtime 4.7.2** or later. On the download page, you'll find download links for the latest 4.x version and the latest two 5.x versions. 

For Azure Data Factory v2 customers:
- If automatic update is on and you've already upgraded your .NET Framework Runtime to 4.7.2 or later, the self-hosted integration runtime will be automatically upgraded to the latest 5.x version.
- If automatic update is on and you haven't upgraded your .NET Framework Runtime to 4.7.2 or later, the self-hosted integration runtime won't be automatically upgraded to the latest 5.x version. The self-hosted integration runtime will stay in the current 4.x version. You can see a warning for a .NET Framework Runtime upgrade in the portal and the self-hosted integration runtime client.
- If automatic update is off and you've already upgraded your .NET Framework Runtime to 4.7.2 or later, you can manually download the latest 5.x and install it on your machine.
- If automatic update is off and you haven't upgraded your .NET Framework Runtime to 4.7.2 or later. When you try to manually install self-hosted integration runtime 5.x and register the key, you will be required to upgrade your .NET Framework Runtime version first.


For Azure Data Factory v1 customers:
- Self-hosted integration runtime 5.X doesn’t support Azure Data Factory v1.
- The self-hosted integration runtime will be automatically upgraded to the latest version of 4.x. And the latest version of 4.x won't expire. 
- If you try to manually install self-hosted integration runtime 5.x and register the key, you'll be notified that self-hosted integration runtime 5.x doesn’t support Azure Data Factory v1.


## Self-hosted IR connectivity issues

### Self-hosted integration runtime can't connect to the cloud service

#### Symptoms

When you attempt to register the self-hosted integration runtime, Configuration Manager displays the following error message:

"The Integration Runtime (Self-hosted) node has encountered an error during registration."

![Screenshot of the "The Integration Runtime (Self-hosted) node has encountered an error during registration" message.](media/self-hosted-integration-runtime-troubleshoot-guide/unable-to-connect-to-cloud-service.png)

#### Cause 

The self-hosted IR can't connect to the Azure Data Factory service back end. This issue is usually caused by network settings in the firewall.

#### Resolution

1. Check to see whether the integration runtime service is running. If it is, go to step 2.
    
   ![Screenshot showing that the self-hosted IR service is running.](media/self-hosted-integration-runtime-troubleshoot-guide/integration-runtime-service-running-status.png)
    
1. If no proxy is configured on the self-hosted IR, which is the default setting, run the following PowerShell command on the machine where the self-hosted integration runtime is installed:

    ```powershell
    (New-Object System.Net.WebClient).DownloadString("https://wu2.frontend.clouddatahub.net/")
    ```
        
   > [!NOTE]     
   > The service URL might vary, depending on the location of your data factory instance. To find the service URL, select **ADF UI** > **Connections** > **Integration runtimes** > **Edit Self-hosted IR** > **Nodes** > **View Service URLs**.
            
    The following is the expected response:
            
    ![Screenshot of the PowerShell command response.](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)
            
1. If you don't receive the response you had expected, use one of the following methods, as appropriate:
            
    * If you receive a "Remote name could not be resolved" message, there's a Domain Name System (DNS) issue. Contact your network team to fix the issue.
    * If you receive an "ssl/tls cert is not trusted" message, [check the certificate](https://wu2.frontend.clouddatahub.net/) to see whether it's trusted on the machine, and then install the public certificate by using Certificate Manager. This action should mitigate the issue.
    * Go to **Windows** > **Event viewer (logs)** > **Applications and Services Logs** > **Integration Runtime**, and check for any failure that's caused by DNS, a firewall rule, or company network settings. If you find such a failure, forcibly close the connection. Because every company has its own customized network settings, contact your network team to troubleshoot these issues.

1. If "proxy" has been configured on the self-hosted integration runtime, verify that your proxy server can access the service endpoint. For a sample command, see [PowerShell, web requests, and proxies](https://stackoverflow.com/questions/571429/powershell-web-requests-and-proxies).    
                
    ```powershell
    $user = $env:username
    $webproxy = (get-itemproperty 'HKCU:\Software\Microsoft\Windows\CurrentVersion\Internet
    Settings').ProxyServer
    $pwd = Read-Host "Password?" -assecurestring
    $proxy = new-object System.Net.WebProxy
    $proxy.Address = $webproxy
    $account = new-object System.Net.NetworkCredential($user,[Runtime.InteropServices.Marshal]::PtrToStringAuto([Runtime.InteropServices.Marshal]::SecureStringToBSTR($pwd)), "")
    $proxy.credentials = $account
    $url = "https://wu2.frontend.clouddatahub.net/"
    $wc = new-object system.net.WebClient
    $wc.proxy = $proxy
    $webpage = $wc.DownloadData($url)
    $string = [System.Text.Encoding]::ASCII.GetString($webpage)
    $string
    ```

The following is the expected response:
            
![Screenshot of the expected Powershell command response.](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)

> [!NOTE] 
> Proxy considerations:
> * Check to see whether the proxy server needs to be put on the Safe Recipients list. If so, make sure [these domains](./data-movement-security-considerations.md#firewall-requirements-for-on-premisesprivate-network) are on the Safe Recipients list.
> * Check to see whether SSL/TLS certificate "wu2.frontend.clouddatahub.net/" is trusted on the proxy server.
> * If you're using Active Directory authentication on the proxy, change the service account to the user account that can access the proxy as "Integration Runtime Service."

### Error message: Self-hosted integration runtime node/logical self-hosted IR is in Inactive/ "Running (Limited)" state

#### Cause 

The self-hosted integrated runtime node might have a status of **Inactive**, as shown in the following screenshot:

![Screenshot of self-hosted integrated runtime node with inactive status](media/self-hosted-integration-runtime-troubleshoot-guide/inactive-self-hosted-ir-node.png)

This behavior occurs when nodes can't communicate with each other.

#### Resolution

1. Log in to the node-hosted virtual machine (VM). Under **Applications and Services Logs** > **Integration Runtime**, open Event Viewer, and filter the error logs.

1. Check to see whether an error log contains the following error: 
    
    ```
    System.ServiceModel.EndpointNotFoundException: Could not connect to net.tcp://xxxxxxx.bwld.com:8060/ExternalService.svc/WorkerManager. The connection attempt lasted for a time span of 00:00:00.9940994. TCP error code 10061: No connection could be made because the target machine actively refused it 10.2.4.10:8060. 
    System.Net.Sockets.SocketException: No connection could be made because the target machine actively refused it. 
    10.2.4.10:8060
    at System.Net.Sockets.Socket.DoConnect(EndPoint endPointSnapshot, SocketAddress socketAddress)
    at System.Net.Sockets.Socket.Connect(EndPoint remoteEP)
    at System.ServiceModel.Channels.SocketConnectionInitiator.Connect(Uri uri, TimeSpan timeout)
    ```
       
1. If you see this error, run the following command in a Command Prompt window: 

   ```
   telnet 10.2.4.10 8060
   ```
   
1. If you receive the "Could not open connection to the host" command-line error that's shown in the following screenshot, contact your IT department for help to fix this issue. After you can successfully telnet, contact Microsoft Support if you still have issues with the integration runtime node status.
        
   ![Screenshot of the "Could not open connection to the host" command-line error.](media/self-hosted-integration-runtime-troubleshoot-guide/command-line-error.png)
        
1. Check to see whether the error log contains the following entry:

    ```
    Error log: Cannot connect to worker manager: net.tcp://xxxxxx:8060/ExternalService.svc/ No DNS entries exist for host azranlcir01r1. No such host is known Exception detail: System.ServiceModel.EndpointNotFoundException: No DNS entries exist for host xxxxx. ---> System.Net.Sockets.SocketException: No such host is known at System.Net.Dns.GetAddrInfo(String name) at System.Net.Dns.InternalGetHostByName(String hostName, Boolean includeIPv6) at System.Net.Dns.GetHostEntry(String hostNameOrAddress) at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri) --- End of inner exception stack trace --- Server stack trace: at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri)
    ```
    
1. To resolve the issue, try one or both of the following methods:
    - Put all the nodes in the same domain.
    - Add the IP to host mapping in all the hosted VM's host files.

### Connectivity issue between the self-hosted IR and your data factory instance or the self-hosted IR and the data source or sink

To troubleshoot the network connectivity issue, you should know 
how to collect the network trace, understand how to use it, and [analyze the Microsoft Network Monitor (Netmon) trace](#analyze-the-netmon-trace) before applying the Netmon Tools in real cases from the self-hosted IR.

#### Symptoms

You might occasionally need to troubleshoot certain connectivity issues between the self-hosted IR and your data factory instance, as shown in the following screenshot, or between the self-hosted IR and the data source or sink. 

![Screenshot of a "Processed HTTP request failed" message](media/self-hosted-integration-runtime-troubleshoot-guide/http-request-error.png)

In either instance, you might encounter the following errors:

* "Copy failed with error:Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Cannot connect to SQL Server: 'IP address'"

* "One or more errors occurred. An error occurred while sending the request. The underlying connection was closed: An unexpected error occurred on a receive. Unable to read data from the transport connection: An existing connection was forcibly closed by the remote host. An existing connection was forcibly closed by the remote host Activity ID."

#### Resolution

When you encounter the preceding errors, troubleshoot them by following the instructions in this section.

- Collect a Netmon trace for analysis: 

    1. You can set the filter to see a reset from the server to the client side. In the following example screenshot, you can see that the server side is the Data Factory server.

        ![Screenshot of the Data factory server.](media/self-hosted-integration-runtime-troubleshoot-guide/data-factory-server.png)

    1. When you get the reset package, you can find the conversation by following Transmission Control Protocol (TCP).

        ![Screenshot of the TCP conversation.](media/self-hosted-integration-runtime-troubleshoot-guide/find-conversation.png)

    1. Get the conversation between the client and the Data Factory server below by removing the filter.

        ![Screenshot of conversation details.](media/self-hosted-integration-runtime-troubleshoot-guide/get-conversation.png)

- An analysis of the Netmon trace you've collected shows that the Time to Live (TTL)) total is 64. According to the values mentioned in the [IP Time to Live (TTL) and Hop Limit Basics](https://packetpushers.net/ip-time-to-live-and-hop-limit-basics/) article, extracted in the following list, you can see that it's the Linux System that resets the package and causes the disconnection.

    Default TTL and Hop Limit values vary between different operating systems, as listed here:
    - Linux kernel 2.4 (circa 2001): 255 for TCP, User Datagram Protocol (UDP), and Internet Control Message Protocol (ICMP)
    - Linux kernel 4.10 (2015): 64 for TCP, UDP, and ICMP
    - Windows XP (2001): 128 for TCP, UDP, and ICMP
    - Windows 10 (2015): 128 for TCP, UDP, and ICMP
    - Windows Server 2008: 128 for TCP, UDP, and ICMP
    - Windows Server 2019 (2018): 128 for TCP, UDP, and ICMP
    - macOS (2001): 64 for TCP, UDP, and ICMP

    ![Screenshot showing a TTL value of 61.](media/self-hosted-integration-runtime-troubleshoot-guide/ttl-61.png)
    
    In the preceding example, the TTL is shown as 61 instead of 64, because when the network package reaches its destination, it needs to go through various hops, such as routers or network devices. The number of routers or network devices is deducted to produce the final TTL.
    
    In this case, you can see that a reset can be sent from the Linux System with TTL 64.

-  To confirm where the reset device might come from, check the fourth hop from self-hosted IR.
 
    *Network package from Linux System A with TTL 64 -> B TTL 64 minus 1 = 63 -> C TTL 63 minus 1 = 62 -> TTL 62 minus 1 = 61 self-hosted IR*

- In an ideal situation, the TTL hops number would be 128, which means that the Windows operating system is running your data factory instance. As shown in the following example, *128 minus 107 = 21 hops*, which means that 21 hops for the package were sent from the data factory instance to the self-hosted IR during the TCP 3 handshake.
 
    ![Screenshot showing a TTL value of 107.](media/self-hosted-integration-runtime-troubleshoot-guide/ttl-107.png)

    Therefore, you need to engage the network team to check to see what the fourth hop is from the self-hosted IR. If it's the firewall, as with the Linux System, check any logs to see why that device resets the package after the TCP 3 handshake. 
    
    If you're unsure where to investigate, try to get the Netmon trace from both the self-hosted IR and the firewall during the problematic time. This approach will help you figure out which device might have reset the package and caused the disconnection. In this case, you also need to engage your network team to move forward.

### Analyze the Netmon trace

> [!NOTE] 
> The following instructions apply to the Netmon trace. Because Netmon trace is currently out of support, you can use Wireshark for this purpose.

When you try to telnet **8.8.8.8 888** with the collected Netmon trace, you should see the trace in the following screenshots:

![Screenshot showing "Could not open connection to the host on port 888" error message.](media/self-hosted-integration-runtime-troubleshoot-guide/netmon-trace-1.png)

![Screenshot showing a description of the Netmon trace.](media/self-hosted-integration-runtime-troubleshoot-guide/netmon-trace-2.png)
 

The preceding images show that you couldn't make a TCP connection to the **8.8.8.8** server side on port **888**, so you see two **SynReTransmit** additional packages there. Because source **SELF-HOST2** couldn't connect to **8.8.8.8** with the first package, it will keep trying to make the connection.

> [!TIP]
> To make this connection, try the following solution:
> 1. Select **Load Filter** > **Standard Filter** > **Addresses** > **IPv4 Addresses**.
> 1. To apply the filter, enter **IPv4.Address == 8.8.8.8**, and then select **Apply**. You should then see the communication from the local machine to destination **8.8.8.8**.

![Screenshot showing filter addresses.](media/self-hosted-integration-runtime-troubleshoot-guide/filter-addresses-1.png)
        
![Screenshot showing more filter addresses.](media/self-hosted-integration-runtime-troubleshoot-guide/filter-addresses-2.png)

Successful scenarios are shown in the following examples: 

- If you can telnet **8.8.8.8 53** without any issues, there's a successful TCP 3 handshake, and the session finishes with a TCP 4 handshake.

    ![Screenshot showing a successful connection scenario.](media/self-hosted-integration-runtime-troubleshoot-guide/good-scenario-1.png)
     
    ![Screenshot showing details of a successful connection scenario.](media/self-hosted-integration-runtime-troubleshoot-guide/good-scenario-2.png)

- The preceding TCP 3 handshake produces the following workflow:

    ![Diagram of a TCP 3 handshake workflow.](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-3-handshake-workflow.png)
 
- The TCP 4 handshake to finish the session is illustrated by the following workflows:

    ![Screenshot of TCP 4 handshake details.](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-4-handshake.png)

    ![Diagram of a TCP 4 handshake workflow.](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-4-handshake-workflow.png) 

### Microsoft email notification about updating your network configuration

You might receive the following email notification, which recommends that you update your network configuration to allow communication with new IP addresses for Azure Data Factory by 8 November 2020:

   ![Screenshot of Microsoft email notification requesting update of network configuration.](media/self-hosted-integration-runtime-troubleshoot-guide/email-notification.png)

#### Determine whether this notification affects you

This notification applies to the following scenarios:

##### Scenario 1: Outbound communication from a self-hosted integration runtime that's running on-premises behind a corporate firewall

How to determine whether you're affected:

- You *are not* affected if you're defining firewall rules based on fully qualified domain names (FQDNs) that use the approach described in [Set up a firewall configuration and allow list for IP addresses](data-movement-security-considerations.md#firewall-configurations-and-allow-list-setting-up-for-ip-address-of-gateway).

- You *are* affected if you're explicitly enabling the allow list for outbound IPs on your corporate firewall.

   If you're affected, take the following action: by November 8, 2020, notify your network infrastructure team to update your network configuration to use the latest data factory IP addresses. To download the latest IP addresses, go to [Discover service tags by using downloadable JSON files](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files).

##### Scenario 2: Outbound communication from a self-hosted integration runtime that's running on an Azure VM inside a customer-managed Azure virtual network

How to determine whether you're affected:

- Check to see whether you have any outbound network security group (NSG) rules in a private network that contains self-hosted integration runtime. If there are no outbound restrictions, you aren't affected.

- If you have outbound rule restrictions, check to see whether you're using service tags. If you're using service tags, you're not affected. There's no need to change or add anything, because the new IP range is under your existing service tags. 

  ![Screenshot of a destination check showing DataFactory as the destination.](media/self-hosted-integration-runtime-troubleshoot-guide/destination-check.png)

- You *are* affected if you're explicitly enabling the allow list for outbound IP addresses on your NSG rules setting on the Azure virtual network.

   If you're affected, take the following action: by November 8, 2020, notify your network infrastructure team to update the NSG rules on your Azure virtual network configuration to use the latest data factory IP addresses. To download the latest IP addresses, go to [Discover service tags by using downloadable JSON files](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files).

##### Scenario 3: Outbound communication from SSIS Integration Runtime in a customer-managed Azure virtual network

How to determine whether you're affected:

- Check to see whether you have any outbound NSG rules in a private network that contains SQL Server Integration Services (SSIS) Integration Runtime. If there are no outbound restrictions, you aren't affected.

- If you have outbound rule restrictions, check to see whether you're using service tags. If you're using service tags, you're not affected. There's no need to change or add anything because the new IP range is under your existing service tags.

- You *are* affected if you're explicitly enabling the allow list for outbound IP addresses on your NSG rules setting on the Azure virtual network.

  If you're affected, take the following action: by November 8, 2020, notify your network infrastructure team to update the NSG rules on your Azure virtual network configuration to use the latest data factory IP addresses. To download the latest IP addresses, go to [Discover service tags by using downloadable JSON files](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files).

### Couldn't establish a trust relationship for the SSL/TLS secure channel 

#### Symptoms

The self-hosted IR couldn't connect to the Azure Data Factory service.

When you check the self-hosted IR event log or the client notification logs in the CustomLogEvent table, you'll find the following error message:

"The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel. The remote certificate is invalid according to the validation procedure."

The simplest way to check the server certificate of the Data Factory service is to open the Data Factory service URL in your browser. For example, open the [check server certificate link](https://eu.frontend.clouddatahub.net/) on the machine where the self-hosted IR is installed, and then view the server certificate information.

  ![Screenshot of the check server certificate pane of the Azure Data Factory service.](media/self-hosted-integration-runtime-troubleshoot-guide/server-certificate.png)

  ![Screenshot of the window for checking the server certification path.](media/self-hosted-integration-runtime-troubleshoot-guide/certificate-path.png)

#### Cause

There are two possible reasons for this issue:

- Reason 1: The root CA of the Data Factory service server certificate isn't trusted on the machine where the self-hosted IR is installed. 
- Reason 2: You're using a proxy in your environment, the server certificate of the Data Factory service is replaced by the proxy, and the replaced server certificate isn't trusted by the machine where the self-hosted IR is installed.

#### Resolution

- For reason 1: Make sure that the Data Factory server certificate and its certificate chain are trusted by the machine where the self-hosted IR is installed.
- For reason 2: Either trust the replaced root CA on the self-hosted IR machine, or configure the proxy not to replace the Data Factory server certificate.

For more information about trusting certificates on Windows, see [Installing the trusted root certificate](/skype-sdk/sdn/articles/installing-the-trusted-root-certificate).

#### Additional information
We've rolled out a new SSL certificate, which is signed from DigiCert. Check to see whether the DigiCert Global Root G2 is in the trusted root CA.

  ![Screenshot showing the DigiCert Global Root G2 folder in the Trusted Root Certification Authorities directory.](media/self-hosted-integration-runtime-troubleshoot-guide/trusted-root-ca-check.png)

If it isn't in the trusted root CA, [download it here](http://cacerts.digicert.com/DigiCertGlobalRootG2.crt ). 


## Self-hosted IR sharing

### Sharing a self-hosted IR from a different tenant is not supported 

#### Symptoms

You might notice other data factories (on different tenants) as you're attempting to share the self-hosted IR from the Azure Data Factory UI, but you can't share it across data factories that are on different tenants.

#### Cause

The self-hosted IR can't be shared across tenants.

## Next steps

For more help with troubleshooting, try the following resources:

*  [Data Factory blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory feature requests](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure videos](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft Q&A page](/answers/topics/azure-data-factory.html)
*  [Stack overflow forum for Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter information about Data Factory](https://twitter.com/hashtag/DataFactory)
*  [Mapping data flows performance guide](concepts-data-flow-performance.md)
