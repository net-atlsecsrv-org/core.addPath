---
title: Azure Security Center frequently asked questions (FAQ) | Microsoft Docs
description: This FAQ answers questions about Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/19/2019
ms.author: memildin

---
# Azure Security Center frequently asked questions (FAQ)
This FAQ answers questions about Azure Security Center, a service that helps you prevent, detect, and respond to threats with increased visibility into and control over the security of your Microsoft Azure resources.

> [!NOTE]
> Security Center uses the Microsoft Monitoring Agent to collect and store data. To learn more, see [Azure Security Center Platform Migration](security-center-platform-migration.md).
>
>

## General questions
### What is Azure Security Center?
Azure Security Center helps you prevent, detect, and respond to threats with increased visibility into and control over the security of your Azure resources. It provides integrated security monitoring and policy management across your subscriptions, helps detect threats that might otherwise go unnoticed, and works with a broad ecosystem of security solutions.

### How do I get Azure Security Center?
Azure Security Center is enabled with your Microsoft Azure subscription and accessed from the [Azure portal](https://azure.microsoft.com/features/azure-portal/). ([Sign in to the portal](https://portal.azure.com), select **Browse**, and scroll to **Security Center**).  

## Billing
### How does billing work for Azure Security Center?
Security Center is offered in two tiers:

The **Free tier** provides visibility into the security state of your Azure resources, basic security policy, security recommendations, and integration with security products and services from partners.

The **Standard tier** adds advanced threat detection capabilities, including threat intelligence, behavioral analysis, anomaly detection, security incidents, and threat attribution reports. You can start a Standard tier free trial. To upgrade, select [Pricing Tier](https://docs.microsoft.com/azure/security-center/security-center-pricing) in the security policy. To learn more, see the [pricing page](https://azure.microsoft.com/pricing/details/security-center/).

### How can I track who in my organization performed pricing tier changes in Azure Security Center
Azure Subscriptions may have multiple administrators with permissions to change the pricing tier. To find out which user performed a pricing tier change, use the Azure Activity Log. For more information, see [here](https://techcommunity.microsoft.com/t5/Security-Identity/Tracking-Changes-in-the-Pricing-Tier-for-Azure-Security-Center/td-p/390832).

## Permissions
Azure Security Center uses [Role-Based Access Control (RBAC)](../role-based-access-control/role-assignments-portal.md), which provides [built-in roles](../role-based-access-control/built-in-roles.md) that can be assigned to users, groups, and services in Azure.

Security Center assesses the configuration of your resources to identify security issues and vulnerabilities. In Security Center, you only see information related to a resource when you are assigned the role of Owner, Contributor, or Reader for the subscription or resource group that a resource belongs to.

See [Permissions in Azure Security Center](security-center-permissions.md) to learn more about roles and allowed actions in Security Center.

## Data collection, agents, and workspaces
Security Center collects data from your Azure virtual machines (VMs), Virtual machine scale sets, IaaS containers, and non-Azure computers (including on-premises) to monitor for security vulnerabilities and threats. Data is collected using the Microsoft Monitoring Agent, which reads various security-related configurations and event logs from the machine and copies the data to your workspace for analysis.

### Am I billed for Azure Monitor logs on the workspaces created by Security Center?
No. Workspaces created by Security Center, while configured for Azure Monitor logs per node billing, do not incur Azure Monitor logs charges. Security Center billing is always based on your Security Center security policy and the solutions installed on a workspace:

- **Free tier** – Security Center enables the 'SecurityCenterFree' solution on the default workspace. You won't be billed for the Free tier.
- **Standard tier** – Security Center enables the 'Security' solution on the default workspace.

For more information on pricing, see [Security Center pricing](https://azure.microsoft.com/pricing/details/security-center/).

> [!NOTE]
> The log analytics pricing tier of workspaces created by Security Center does not affect Security Center billing.
>
>

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

### What qualifies a VM for automatic provisioning of the Microsoft Monitoring Agent installation?
Windows or Linux IaaS VMs qualify if:

- The Microsoft Monitoring Agent extension is not currently installed on the VM.
- The VM is in running state.
- The Windows or Linux [Azure Virtual Machine Agent](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) is installed.
- The VM is not used as an appliance such as web application firewall or next generation firewall.

### Can I delete the default workspaces created by Security Center?
**Deleting the default workspace is not recommended.** Security Center uses the default workspaces to store security data from your VMs.  If you delete a workspace, Security Center is unable to collect this data and some security recommendations and alerts are unavailable.

To recover, remove the Microsoft Monitoring Agent on the VMs connected to the deleted workspace. Security Center reinstalls the agent and creates new default workspaces.

### How can I use my existing Log Analytics workspace?

You can select an existing Log Analytics workspace to store data collected by Security Center. To use your existing Log Analytics workspace:

- The workspace must be associated with your selected Azure subscription.
- At a minimum, you must have read permissions to access the workspace.

To select an existing Log Analytics workspace:

1. Under **Security policy – Data Collection**, select **Use another workspace**.

   ![Use another workspace][5]

2. From the pull-down menu, select a workspace to store collected data.

   > [!NOTE]
   > In the pull down menu, only workspaces that you have access to and are in your Azure subscription are shown.
   >
   >

3. Select **Save**.
4. After selecting **Save**, you will be asked if you would like to reconfigure monitored VMs.

   - Select **No** if you want the new workspace settings to **apply on new VMs only**. The new workspace settings only apply to new agent installations; newly discovered VMs that do not have the Microsoft Monitoring Agent installed.
   - Select **Yes** if you want the new workspace settings to **apply on all VMs**. In addition, every VM connected to a Security Center created workspace is reconnected to the new target workspace.

   > [!NOTE]
   > If you select Yes, you must not delete the workspace(s) created by Security Center until all VMs have been reconnected to the new target workspace. This operation fails if a workspace is deleted too early.
   >
   >

   - Select **Cancel** to cancel the operation.

### What if the Microsoft Monitoring Agent was already installed as an extension on the VM?<a name="mmaextensioninstalled"></a>
When the Monitoring Agent is installed as an extension, the extension configuration allows reporting to only a single workspace. Security Center does not override existing connections to user workspaces. Security Center will store security data from a VM in a workspace that is already connected, provided that the "Security" or "SecurityCenterFree" solution has been installed on it. Security Center may upgrade the extension version to the latest version in this process.

For more information, see [Automatic provisioning in cases of a pre-existing agent installation](security-center-enable-data-collection.md#preexisting).


### What if I had a Microsoft Monitoring Agent is directly installed on the machine but not as an extension (Direct Agent)?<a name="directagentinstalled"></a>
If the Microsoft Monitoring Agent is installed directly on the VM (not as an Azure extension), Security Center will install the Microsoft Monitoring Agent extension, and may upgrade the Microsoft Monitoring agent to the latest version.
The agent installed will continue to report to its already configured workspace(s), and in addition will report to the workspace configured in Security Center (Multi-homing is supported on Windows machines).
IF the configured workspace is a user workspace (not Security Center's default workspace), you will need to install the "Security/"SecurityCenterFree" solution on it for Security Center to start processing events from VMs and computers reporting to that workspace.

For Linux machines, Agent multi-homing is not yet supported - hence, if an existing agent installation is detected, automatic provisioning will not occur and the machine's configuration will not be altered.

For existing machines on subscriptions onboarded to Security Center before March 17 2019, when an existing agent will be detected, the Microsoft Monitoring Agent extension will not be installed and the machine will not be affected. For these machines, see the "Resolve monitoring agent health issues on your machines" recommendation to resolve the agent installation issues on these machines

 For more information, see the next section [What happens if a System Center Operations Manager or OMS direct agent is already installed on my VM?](#scomomsinstalled)

### What happens if a System Center Operations Manager agent is already installed on my VM?<a name="scomomsinstalled"></a>
Security center will install the Microsoft Monitoring Agent extension side by side to the existing System Center Operations Manager agent. The existing agent will continue to report to the System Center Operations Manager server normally. Note that the Operations Manager agent and Microsoft Monitoring Agent share common run-time libraries, which will be updated to the latest version during this process. Note - If version 2012 of the Operations Manager agent is installed, do not turn on automatic provisioning (manageability capabilities can be lost when the Operations Manager server is also version 2012).

### What is the impact of removing these extensions?
If you remove the Microsoft Monitoring Extension, Security Center is not able to collect security data from the VM and some security recommendations and alerts are unavailable. Within 24 hours, Security Center determines that the VM is missing the extension and reinstalls the extension.

### How do I stop the automatic agent installation and workspace creation?
You can turn off automatic provisioning for your subscriptions in the security policy but this is not recommended. Turning off automatic provisioning limits Security Center recommendations and alerts. To disable automatic provisioning:

1. If your subscription is configured for the Standard tier, open the security policy for that subscription and select the **Free** tier.

   ![Pricing tier][1]

2. Next, turn off automatic provisioning by selecting **Off** on the **Security policy – Data collection** page.
   ![Data collection][2]

### Should I opt out of the automatic agent installation and workspace creation?

> [!NOTE]
> Be sure to review sections [What are the implications of opting out?](#what-are-the-implications-of-opting-out-of-automatic-provisioning) and [recommended steps when opting out](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning) if you choose to opt out of automatic provisioning.
>
>

You may want to opt out of automatic provisioning if the following applies to you:

- Automatic agent installation by Security Center applies to the entire subscription. You cannot apply automatic installation to a subset of VMs. If there are critical VMs that cannot be installed with the Microsoft Monitoring Agent, then you should opt out of automatic provisioning.
- Installation of the Microsoft Monitoring Agent (MMA) extension updates the agent’s version. This applies to a direct agent and a System Center Operations Manager agent (in the latter, the Operations Manager and MMA share common runtime libraries - which will be updated in the process). If the installed Operations Manager agent is version 2012 and is upgraded, manageability capabilities can be lost when the Operations Manager server is also version 2012. Consider opting out of automatic provisioning if the installed Operations Manager agent is version 2012.
- If you have a custom workspace external to the subscription (a centralized workspace), then you should opt out of automatic provisioning. You can manually install the Microsoft Monitoring Agent extension and connect it your workspace without Security Center overriding the connection.
- If you want to avoid creation of multiple workspaces per subscription and you have your own custom workspace within the subscription, then you have two options:

   1. You can opt out of automatic provisioning. After migration, set the default workspace settings as described in [How can I use my existing Log Analytics workspace?](#how-can-i-use-my-existing-log-analytics-workspace)
   2. Or, you can allow the migration to complete, the Microsoft Monitoring Agent to be installed on the VMs, and the VMs connected to the created workspace. Then, select your own custom workspace by setting the default workspace setting with opting in to reconfiguring the already installed agents. For more information, see [How can I use my existing Log Analytics workspace?](#how-can-i-use-my-existing-log-analytics-workspace)

### What are the implications of opting out of automatic provisioning?
When migration is complete, Security Center can't collect security data from the VM and some security recommendations and alerts are unavailable. If you opt out, install the Microsoft Monitoring Agent manually. See [recommended steps when opting out](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning).

### What are the recommended steps when opting out of automatic provisioning?

Manually install the Microsoft Monitoring Agent extension so Security Center can collect security data from your VMs and provide recommendations and alerts. See [agent installation for Windows VM](../virtual-machines/extensions/oms-windows.md) or [agent installation for Linux VM](../virtual-machines/extensions/oms-linux.md) for guidance on installation.

You can connect the agent to any existing custom workspace or Security Center created workspace. If a custom workspace does not have the ‘Security’ or 'SecurityCenterFree' solutions enabled, then you will need to apply a solution. To apply, select the custom workspace or subscription and apply a pricing tier via the **Security policy – Pricing tier** page.

   ![Pricing tier][1]

Security Center will enable the correct solution on the workspace based on the selected pricing tier.

### How do I remove OMS extensions installed by Security Center?<a name="remove-oms"></a>
You can manually remove the Microsoft Monitoring Agent. This is not recommended as it limits Security Center recommendations and alerts.

> [!NOTE]
> If data collection is enabled, Security Center will reinstall the agent after you remove it.  You need to disable data collection before manually removing the agent. See How do I stop the automatic agent installation and workspace creation? for instructions on disabling data collection.
>
>

To manually remove the agent:

1.	In the portal, open **Log Analytics**.
2.	On the Log Analytics page, select a workspace:
3.	Select the VMs that you don’t want to monitor and select **Disconnect**.

   ![Remove the agent][3]

> [!NOTE]
> If a Linux VM already has a non-extension OMS agent, removing the extension removes the agent as well and the customer has to reinstall it.
>
>
### How do I disable data collection?
Automatic provisioning is off by default. You can disable automatic provisioning from resources at any time by turning off this setting in the security policy. Automatic provisioning is highly recommended in order to get security alerts and recommendations about system updates, OS vulnerabilities, and endpoint protection.

To disable data collection, [Sign in to the Azure portal](https://portal.azure.com), select **Browse**, select **Security Center**, and select **Select policy**. Select the subscription that you wish to disable automatic provisioning. When you select a subscription **Security policy - Data collection** opens. Under **Auto provisioning**, select **Off**.

### How do I enable data collection?
You can enable data collection for your Azure subscription in the Security policy. To enable data collection. [Sign in to the Azure portal](https://portal.azure.com), select **Browse**, select **Security Center**, and select **Security policy**. Select the subscription that you wish to enable automatic provisioning. When you select a subscription **Security policy - Data collection** opens. Under **Auto provisioning**, select **On**.

### What happens when data collection is enabled?
When automatic provisioning is enabled, Security Center provisions the Microsoft Monitoring Agent on all supported Azure VMs and any new ones that are created. Automatic provisioning is recommended but manual agent installation is also available. [Learn how to install the Microsoft Monitoring Agent extension](../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension). 

The agent enables the process creation event 4688 and the *CommandLine* field inside event 4688. New processes created on the VM are recorded by EventLog and monitored by Security Center’s detection services. For more information on the details recorded for each new process, see [description fields in 4688](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=4688#fields). The agent also collects the 4688 events created on the VM and stores them in search.

The agent also enables data collection for [Adaptive Application Controls](security-center-adaptive-application.md), Security Center configures a local AppLocker policy in Audit mode to allow all applications. This policy will cause AppLocker to generate events, which are then collected and leveraged by Security Center. It is important to note that this policy will not be configured on any machines on which there is already a configured AppLocker policy. 

When Security Center detects suspicious activity on the VM, the customer is notified by email if [security contact information](security-center-provide-security-contact-details.md) has been provided. An alert is also visible in Security Center’s security alerts dashboard.

### Will Security Center work using an OMS gateway?
Yes. Azure Security Center leverages Azure Monitor to collect data from Azure VMs and servers, using the Microsoft Monitoring Agent.
To collect the data, each VM and server must connect to the Internet using HTTPS. The connection can be direct, using a proxy, or through the [OMS Gateway](../azure-monitor/platform/gateway.md).

### Does the Monitoring Agent impact the performance of my servers?
The agent consumes a nominal amount of system resources and should have little impact on the performance. For more information on performance impact and the agent and extension, see the [planning and operations guide](security-center-planning-and-operations-guide.md#data-collection-and-storage).

### Where is my data stored?
Data collected from this agent is stored in either an existing Log Analytics workspace associated with your subscription or a new workspace. For more information, see [Data Security](security-center-data-security.md).

## Existing Azure Monitor logs customers<a name="existingloganalyticscust"></a>

### Does Security Center override any existing connections between VMs and workspaces?
If a VM already has the Microsoft Monitoring Agent installed as an Azure extension, Security Center does not override the existing workspace connection. Instead, Security Center uses the existing workspace. The VM will be protected provided that  the "Security" or "SecurityCenterFree" solution has been installed on the workspace to which it is reporting. 

A Security Center solution is installed on the workspace selected in the Data Collection screen if not present already, and the solution is applied only to the relevant VMs. When you add a solution, it's automatically deployed by default to all Windows and Linux agents connected to your Log Analytics workspace. [Solution Targeting](../operations-management-suite/operations-management-suite-solution-targeting.md) allows you to apply a scope to your solutions.

If the Microsoft Monitoring Agent is installed directly on the VM (not as an Azure extension), Security Center does not install the Microsoft Monitoring Agent and security monitoring is limited.

### Does Security Center install solutions on my existing Log Analytics workspaces? What are the billing implications?
When Security Center identifies that a VM is already connected to a workspace you created, Security Center enables solutions on this workspace according to your pricing tier. The solutions are applied only to the relevant Azure VMs, via [solution targeting](../operations-management-suite/operations-management-suite-solution-targeting.md), so the billing remains the same.

- **Free tier** – Security Center installs the 'SecurityCenterFree' solution on the workspace. You won't be billed for the Free tier.
- **Standard tier** – Security Center installs the 'Security' solution on the workspace.

   ![Solutions on default workspace][4]

### I already have workspaces in my environment, can I use them to collect security data?
If a VM already has the Microsoft Monitoring Agent installed as an Azure extension, Security Center uses the existing connected workspace. A Security Center solution is installed on the workspace if not present already, and the solution is applied only to the relevant VMs via [solution targeting](../operations-management-suite/operations-management-suite-solution-targeting.md).

When Security Center installs the Microsoft Monitoring Agent on VMs, it uses the default workspace(s) created by Security Center.

### I already have security solution on my workspaces. What are the billing implications?
The Security & Audit solution is used to enable Security Center Standard tier features for Azure VMs. If the Security & Audit solution is already installed on a workspace, Security Center uses the existing solution. There is no change in billing.

## Using Azure Security Center
### What is a security policy?
A security policy defines the set of controls that are recommended for resources within the specified subscription. In Azure Security Center, you define policies for your Azure subscriptions according to your company's security requirements and the type of applications or sensitivity of the data in each subscription.

The security policies enabled in Azure Security Center drive security recommendations and monitoring. To learn more about security policies, see [Security health monitoring in Azure Security Center](security-center-monitoring.md).

### Who can modify a security policy?
To modify a security policy, you must be a Security Administrator or an Owner or Contributor of that subscription.

To learn how to configure a security policy, see [Setting security policies in Azure Security Center](tutorial-security-policy.md).

### What is a security recommendation?
Azure Security Center analyzes the security state of your Azure resources. When potential security vulnerabilities are identified, recommendations are created. The recommendations guide you through the process of configuring the needed control. Examples are:

* Provisioning of anti-malware to help identify and remove malicious software
* [Network security groups](../virtual-network/security-overview.md) and rules to control traffic to virtual machines
* Provisioning of a web application firewall to help defend against attacks targeting your web applications
* Deploying missing system updates
* Addressing OS configurations that do not match the recommended baselines

Only recommendations that are enabled in Security Policies are shown here.

### How can I see the current security state of my Azure resources?
The **Security Center Overview** page shows the overall security posture of your environment broken down by Compute, Networking, Storage & data, and Applications. Each resource type has an indicator showing if any potential security vulnerabilities have been identified. Clicking each tile displays a list of security issues identified by Security Center, along with an inventory of the resources in your subscription.

### What triggers a security alert?
Azure Security Center automatically collects, analyzes, and fuses log data from your Azure resources, the network, and partner solutions like antimalware and firewalls. When threats are detected, a security alert is created. Examples include detection of:

* Compromised virtual machines communicating with known malicious IP addresses
* Advanced malware detected using Windows error reporting
* Brute force attacks against virtual machines
* Security alerts from integrated partner security solutions such as Anti-Malware or Web Application Firewalls

### Why did secure scores values change? <a name="secure-score-faq"></a>
As of February 2019, Security Center adjusted the score of a few recommendations, in order to better fit their severity. As a result of this adjustment, there may be changes in overall secure score values.  For more information about secure score, see [Secure score calculation](security-center-secure-score.md).

### What's the difference between threats detected and alerted on by Microsoft Security Response Center versus Azure Security Center?
The Microsoft Security Response Center (MSRC) performs select security monitoring of the Azure network and infrastructure and receives threat intelligence and abuse complaints from third parties. When MSRC becomes aware that customer data has been accessed by an unlawful or unauthorized party or that the customer’s use of Azure does not comply with the terms for Acceptable Use, a security incident manager notifies the customer. Notification typically occurs by sending an email to the security contacts specified in Azure Security Center or the Azure subscription owner if a security contact is not specified.

Security Center is an Azure service that continuously monitors the customer’s Azure environment and applies analytics to automatically detect a wide range of potentially malicious activity. These detections are surfaced as security alerts in the Security Center dashboard.

### Which Azure resources are monitored by Azure Security Center?
Azure Security Center monitors the following Azure resources:

* Virtual machines (VMs) (including [Cloud Services](../cloud-services/cloud-services-choose-me.md))
* Virtual machine scale sets
* Azure Virtual Networks
* Azure SQL service
* Azure Storage account
* Azure Web Apps (in [App Service Environment](../app-service/environment/intro.md))
* Partner solutions integrated with your Azure subscription such as a web application firewall on VMs and on App Service Environment

In addition, non-Azure (including on-premises) computers can also be monitored by Azure Security Center (Both [Windows computers](./quick-onboard-windows-computer.md) and [Linux computers](./quick-onboard-linux-computer.md) are supported)

## Virtual Machines
### What types of virtual machines are supported?
Monitoring and recommendations are available for virtual machines (VMs) created using both the [classic and Resource Manager deployment models](../azure-classic-rm.md).

See [Supported platforms in Azure Security Center](security-center-os-coverage.md) for a list of supported platforms.

### Why doesn't Azure Security Center recognize the antimalware solution running on my Azure VM?
Azure Security Center has visibility into antimalware installed through Azure extensions. For example, Security Center is not able to detect antimalware that was pre-installed on an image you provided or if you installed antimalware on your virtual machines using your own processes (such as configuration management systems).

### Why do I get the message "Missing Scan Data" for my VM?
This message appears when there is no scan data for a VM. It can take some time (less than an hour) for scan data to populate after Data Collection is enabled in Azure Security Center. After the initial population of scan data, you may receive this message because there is no scan data at all or there is no recent scan data. Scans do not populate for a VM in a stopped state. This message could also appear if scan data has not populated recently (in accordance with the retention policy for the Windows agent, which has a default value of 30 days).

### How often does Security Center scan for operating system vulnerabilities, system updates, and endpoint protection issues?
Below are the latency times for Security Center scans of vulnerabilities, updates, and issues:

- Operating system security configurations – data is updated within 48 hours
- System updates – data is updated within 24 hours
- Endpoint Protection issues – data is updated within 8 hours

Security Center typically scans for new data every hour, and refreshes the recommendations accordingly. 

> [!NOTE]
> Security Center uses the Microsoft Monitoring Agent to collect and store data. To learn more, see [Azure Security Center Platform Migration](security-center-platform-migration.md).
>
>

### Why do I get the message "VM Agent is Missing?"
The VM Agent must be installed on VMs to enable Data Collection. The VM Agent is installed by default for VMs that are deployed from the Azure Marketplace. For information on how to install the VM Agent on other VMs, see the blog post [VM Agent and Extensions](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/).


<!--Image references-->
[1]: ./media/security-center-platform-migration-faq/pricing-tier.png
[2]: ./media/security-center-platform-migration-faq/data-collection.png
[3]: ./media/security-center-platform-migration-faq/remove-the-agent.png
[4]: ./media/security-center-platform-migration-faq/solutions.png
[5]: ./media/security-center-platform-migration-faq/use-another-workspace.png
