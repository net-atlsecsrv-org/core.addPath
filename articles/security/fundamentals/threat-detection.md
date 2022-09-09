---
title: Azure advanced threat detection | Microsoft Docs
description: Learn about Azure AD Identity Protection and its capabilities.
services: security
documentationcenter: na
author: UnifyCloud
manager: barbkess
editor: TomSh

ms.assetid:
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh

---

# Azure advanced threat detection

Azure offers built in advanced threat detection functionality through services such as Azure Active Directory (Azure AD), Azure Monitor logs, and Azure Security Center. This collection of security services and capabilities provides a simple and fast way to understand what is happening within your Azure deployments.

Azure provides a wide array of options to configure and customize security to meet the requirements of your app deployments. This article discusses how to meet these requirements.

## Azure Active Directory Identity Protection

[Azure AD Identity Protection](../../active-directory/identity-protection/overview.md) is an [Azure Active Directory Premium P2](../../active-directory/active-directory-whatis.md) edition feature that provides an overview of the risk detections and potential vulnerabilities that can affect your organization’s identities. Identity Protection uses existing Azure AD anomaly-detection capabilities that are available through [Azure AD Anomalous Activity Reports](../../active-directory/active-directory-reporting-azure-portal.md), and introduces new risk detection types that can detect real time anomalies.

![Azure AD Identity Protection diagram](./media/threat-detection/azure-threat-detection-fig1.png)

Identity Protection uses adaptive machine learning algorithms and heuristics to detect anomalies and risk detections that might indicate that an identity has been compromised. Using this data, Identity Protection generates reports and alerts so that you can investigate these risk detections and take appropriate remediation or mitigation action.

Azure Active Directory Identity Protection is more than a monitoring and reporting tool. Based on risk detections, Identity Protection calculates a user risk level for each user, so that you can configure risk-based policies to automatically protect the identities of your organization.

These risk-based policies, in addition to other [Conditional Access controls](../../active-directory/active-directory-conditional-access-azure-portal.md) that are provided by Azure Active Directory and [EMS](../../active-directory/active-directory-conditional-access-azure-portal.md), can automatically block or offer adaptive remediation actions that include password resets and multi-factor authentication enforcement.

### Identity Protection capabilities

Azure Active Directory Identity Protection is more than a monitoring and reporting tool. To protect your organization's identities, you can configure risk-based policies that automatically respond to detected issues when a specified risk level has been reached. These policies, in addition to other Conditional Access controls provided by Azure Active Directory and EMS, can either automatically block or initiate adaptive remediation actions including password resets and multi-factor authentication enforcement.

Examples of some of the ways that Azure Identity Protection can help secure your accounts and identities include:

[Detecting risk detections and risky accounts](../../active-directory/identity-protection/overview.md)
-	Detect six risk detection types using machine learning and heuristic rules.
-	Calculate user risk levels.
-	Provide custom recommendations to improve overall security posture by highlighting vulnerabilities.

[Investigating risk detections](../../active-directory/identity-protection/overview.md)
-	Send notifications for risk detections.
-	Investigate risk detections using relevant and contextual information.
-	Provide basic workflows to track investigations.
-	Provide easy access to remediation actions such as password reset.

[Risk-based, conditional-access policies](../../active-directory/identity-protection/overview.md)
-	Mitigate risky sign-ins by blocking sign-ins or requiring multi-factor authentication challenges.
-	Block or secure risky user accounts.
-	Require users to register for multi-factor authentication.

### Azure AD Privileged Identity Management

With [Azure Active Directory Privileged Identity Management (PIM)](../../active-directory/privileged-identity-management/pim-configure.md), you can manage, control, and monitor access within your organization. This feature includes access to resources in Azure AD and other Microsoft online services, such as Office 365 or Microsoft Intune.

![Azure AD Privileged Identity Management diagram](./media/threat-detection/azure-threat-detection-fig2.png)

PIM helps you:

-	Get alerts and reports about Azure AD administrators and just-in-time (JIT) administrative access to Microsoft online services, such as Office 365 and Intune.

-	Get reports about administrator access history and changes in administrator assignments.

-	Get alerts about access to a privileged role.

## Azure Monitor logs

[Azure Monitor logs](../../azure-monitor/index.yml) is a Microsoft cloud-based IT management solution that helps you manage and protect your on-premises and cloud infrastructure. Because Azure Monitor logs is implemented as a cloud-based service, you can have it up and running quickly with minimal investment in infrastructure services. New security features are delivered automatically, saving ongoing maintenance and upgrade costs.

In addition to providing valuable services on its own, Azure Monitor logs can integrate with System Center components, such as [System Center Operations Manager](https://blogs.technet.microsoft.com/cbernier/2013/10/23/monitoring-windows-azure-with-system-center-operations-manager-2012-get-me-started/), to extend your existing security management investments into the cloud. System Center and Azure Monitor logs can work together to provide a full hybrid management experience.

### Holistic security and compliance posture

The [Log Analytics Security and Audit dashboard](../../security-center/security-center-intro.md) provides a comprehensive view into your organization’s IT security posture, with built-in search queries for notable issues that require your attention. The Security and Audit dashboard is the home screen for everything related to security in Azure Monitor logs. It provides high-level insight into the security state of your computers. You can also view all events from the past 24 hours, 7 days, or any other custom timeframe.

Azure Monitor logs help you quickly and easily understand the overall security posture of any environment, all within the context of IT Operations, including software update assessment, antimalware assessment, and configuration baselines. Security log data is readily accessible to streamline the security and compliance audit processes.

![The Log Analytics Security and Audit dashboard](./media/threat-detection/azure-threat-detection-fig3.jpg)

The Log Analytics Security and Audit dashboard is organized into four major categories:

-	**Security Domains**: Lets you further explore security records over time; access malware assessments; update assessments; view network security, identity, and access information; view computers with security events; and quickly access the Azure Security Center dashboard.

-	**Notable Issues**: Lets you quickly identify the number of active issues and the severity of the issues.

-	**Detections (Preview)**: Lets you identify attack patterns by displaying security alerts as they occur against your resources.

-	**Threat Intelligence**: Lets you identify attack patterns by displaying the total number of servers with outbound malicious IP traffic, the malicious threat type, and a map of the IPs locations.

-	**Common security queries**: Lists the most common security queries that you can use to monitor your environment. When you select any query, the Search pane opens and displays the results for that query.

### Insight and analytics
At the center of [Azure Monitor logs](../../log-analytics/log-analytics-queries.md) is the repository, which is hosted by Azure.

![Insight and analytics diagram](./media/threat-detection/azure-threat-detection-fig4.png)

You collect data into the repository from connected sources by configuring data sources and adding solutions to your subscription.

![The Azure Monitor logs dashboard](./media/threat-detection/azure-threat-detection-fig5.png)

Data sources and solutions each create separate record types with their own set of properties, but you can still analyze them together in queries to the repository. You can use the same tools and methods to work with a variety of data that's collected by various sources.


Most of your interaction with Azure Monitor logs is through the Azure portal, which runs in any browser and provides you with access to configuration settings and multiple tools to analyze and act on collected data. From the portal, you can use:
* [Log searches](../../log-analytics/log-analytics-queries.md) where you construct queries to analyze collected data.
* [Dashboards](../../azure-monitor/learn/tutorial-logs-dashboards.md), which you can customize with graphical views of your most valuable searches.
* [Solutions](../../monitoring/monitoring-solutions.md), which provide additional functionality and analysis tools.

![Analysis tools](./media/threat-detection/azure-threat-detection-fig6.png)

Solutions add functionality to Azure Monitor logs. They primarily run in the cloud and provide analysis of data that's collected in the log analytics repository. Solutions might also define new record types to be collected that can be analyzed with log searches or by using an additional user interface that the solution provides in the log analytics dashboard.

The Security and Audit dashboard is an example of these types of solutions.

### Automation and control: Alert on security configuration drifts

Azure Automation automates administrative processes with runbooks that are based on PowerShell and run in the cloud. Runbooks can also be executed on a server in your local data center to manage local resources. Azure Automation provides configuration management with PowerShell Desired State Configuration (DSC).

![Azure Automation diagram](./media/threat-detection/azure-threat-detection-fig7.png)

You can create and manage DSC resources that are hosted in Azure and apply them to cloud and on-premises systems. By doing so, you can define and automatically enforce their configuration or get reports on drift to help ensure that security configurations remain within policy.

## Azure Security Center

Azure Security Center helps protect your Azure resources. It provides integrated security monitoring and policy management across your Azure subscriptions. Within the service, you can define polices against both your Azure subscriptions and [resource groups](../../azure-resource-manager/management/manage-resources-portal.md) for greater granularity.

![Azure Security Center diagram](./media/threat-detection/azure-threat-detection-fig8.png)

Microsoft security researchers are constantly on the lookout for threats. They have access to an expansive set of telemetry gained from Microsoft’s global presence in the cloud and on-premises. This wide-reaching and diverse collection of datasets enables Microsoft to discover new attack patterns and trends across its on-premises consumer and enterprise products, as well as its online services.

Thus, Security Center can rapidly update its detection algorithms as attackers release new and increasingly sophisticated exploits. This approach helps you keep pace with a fast-moving threat environment.

![Security Center threat detection](./media/threat-detection/azure-threat-detection-fig9.jpg)

Security Center threat detection works by automatically collecting security information from your Azure resources, the network, and connected partner solutions. It analyzes this information, correlating information from multiple sources, to identify threats.

Security alerts are prioritized in Security Center along with recommendations on how to remediate the threat.

Security Center employs advanced security analytics, which go far beyond signature-based approaches. Breakthroughs in big data and [machine learning](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) technologies are used to evaluate events across the entire cloud fabric. Advanced analytics can detect threats that would be impossible to identify through manual approaches and predicting the evolution of attacks. These security analytics types are covered in the next sections.

### Threat intelligence

Microsoft has access to an immense amount of global threat intelligence.

Telemetry flows in from multiple sources, such as Azure, Office 365, Microsoft CRM online, Microsoft Dynamics AX, outlook.com, MSN.com, the Microsoft Digital Crimes Unit (DCU), and Microsoft Security Response Center (MSRC).

![Threat intelligence findings](./media/threat-detection/azure-threat-detection-fig10.jpg)

Researchers also receive threat intelligence information that is shared among major cloud service providers, and they subscribe to threat intelligence feeds from third parties. Azure Security Center can use this information to alert you to threats from known bad actors. Some examples include:

-	**Harnessing the power of machine learning**: Azure Security Center has access to a vast amount of data about cloud network activity, which can be used to detect threats targeting your Azure deployments.

-	**Brute force detection**: Machine learning is used to create a historical pattern of remote access attempts, which allows it to detect brute force attacks against Secure Shell (SSH), Remote Desktop Protocol (RDP), and SQL ports.

-	**Outbound DDoS and botnet detection**: A common objective of attacks that target cloud resources is to use the compute power of these resources to execute other attacks.

-	**New behavioral analytics servers and VMs**: After a server or virtual machine is compromised, attackers employ a wide variety of techniques to execute malicious code on that system while avoiding detection, ensuring persistence, and obviating security controls.

-	**Azure SQL Database Threat Detection**: Threat detection for Azure SQL Database, which identifies anomalous database activities that indicate unusual and potentially harmful attempts to access or exploit databases.

### Behavioral analytics

Behavioral analytics is a technique that analyzes and compares data to a collection of known patterns. However, these patterns are not simple signatures. They are determined through complex machine learning algorithms that are applied to massive datasets.

![Behavioral analytics findings](./media/threat-detection/azure-threat-detection-fig11.jpg)

The patterns are also determined through careful analysis of malicious behaviors by expert analysts. Azure Security Center can use behavioral analytics to identify compromised resources based on analysis of virtual machine logs, virtual network device logs, fabric logs, crash dumps, and other sources.

In addition, patterns are correlated with other signals to check for supporting evidence of a widespread campaign. This correlation helps to identify events that are consistent with established indicators of compromise.

Some examples include:
-	**Suspicious process execution**: Attackers employ several techniques to execute malicious software without detection. For example, an attacker might give malware the same names as legitimate system files but place these files in an alternate location, use a name that is similar to that of a benign file, or mask the file’s true extension. Security Center models process behaviors and monitor process executions to detect outliers such as these.

-	**Hidden malware and exploitation attempts**: Sophisticated malware can evade traditional antimalware products by either never writing to disk or encrypting software components stored on disk. However, such malware can be detected by using memory analysis, because the malware must leave traces in memory to function. When software crashes, a crash dump captures a portion of memory at the time of the crash. By analyzing the memory in the crash dump, Azure Security Center can detect techniques used to exploit vulnerabilities in software, access confidential data, and surreptitiously persist within a compromised machine without affecting the performance of your machine.

-	**Lateral movement and internal reconnaissance**: To persist in a compromised network and locate and harvest valuable data, attackers often attempt to move laterally from the compromised machine to others within the same network. Security Center monitors process and login activities to discover attempts to expand an attacker’s foothold within the network, such as remote command execution, network probing, and account enumeration.

-	**Malicious PowerShell scripts**: PowerShell can be used by attackers to execute malicious code on target virtual machines for various purposes. Security Center inspects PowerShell activity for evidence of suspicious activity.

-	**Outgoing attacks**: Attackers often target cloud resources with the goal of using those resources to mount additional attacks. Compromised virtual machines, for example, might be used to launch brute force attacks against other virtual machines, send spam, or scan open ports and other devices on the internet. By applying machine learning to network traffic, Security Center can detect when outbound network communications exceed the norm. When spam is detected, Security Center also correlates unusual email traffic with intelligence from Office 365 to determine whether the mail is likely nefarious or the result of a legitimate email campaign.

### Anomaly detection

Azure Security Center also uses anomaly detection to identify threats. In contrast to behavioral analytics (which depends on known patterns derived from large data sets), anomaly detection is more “personalized” and focuses on baselines that are specific to your deployments. Machine learning is applied to determine normal activity for your deployments, and then rules are generated to define outlier conditions that could represent a security event. Here’s an example:

-	**Inbound RDP/SSH brute force attacks**: Your deployments might have busy virtual machines with many logins each day and other virtual machines that have few, if any, logins. Azure Security Center can determine baseline login activity for these virtual machines and use machine learning to define around the normal login activities. If there is any discrepancy with the baseline defined for login related characteristics, an alert might be generated. Again, machine learning determines what is significant.

### Continuous threat intelligence monitoring

Azure Security Center operates with security research and data science teams throughout the world that continuously monitor for changes in the threat landscape. This includes the following initiatives:

-	**Threat intelligence monitoring**: Threat intelligence includes mechanisms, indicators, implications, and actionable advice about existing or emerging threats. This information is shared in the security community, and Microsoft continuously monitors threat intelligence feeds from internal and external sources.

-	**Signal sharing**: Insights from security teams across the broad Microsoft portfolio of cloud and on-premises services, servers, and client endpoint devices are shared and analyzed.

-	**Microsoft security specialists**: Ongoing engagement with teams across Microsoft that work in specialized security fields, such as forensics and web attack detection.

-	**Detection tuning**: Algorithms are run against real customer data sets, and security researchers work with customers to validate the results. True and false positives are used to refine machine learning algorithms.

These combined efforts culminate in new and improved detections, which you can benefit from instantly. There’s no action for you to take.

## Advanced threat detection features: Other Azure services

### Virtual machines: Microsoft antimalware

[Microsoft antimalware](antimalware.md) for Azure is a single-agent solution for applications and tenant environments, designed to run in the background without human intervention. You can deploy protection based on the needs of your application workloads, with either basic secure-by-default or advanced custom configuration, including antimalware monitoring. Azure antimalware is a security option for Azure virtual machines that's automatically installed on all Azure PaaS virtual machines.

#### Microsoft antimalware core features

Here are the features of Azure that deploy and enable Microsoft antimalware for your applications:

-	**Real-time protection**: Monitors activity in cloud services and on virtual machines to detect and block malware execution.

-	**Scheduled scanning**: Periodically performs targeted scanning to detect malware, including actively running programs.

-	**Malware remediation**: Automatically acts on detected malware, such as deleting or quarantining malicious files and cleaning up malicious registry entries.

-	**Signature updates**: Automatically installs the latest protection signatures (virus definitions) to ensure that protection is up to date on a pre-determined frequency.

-	**Antimalware Engine updates**: Automatically updates the Microsoft Antimalware Engine.

-	**Antimalware platform updates**: Automatically updates the Microsoft antimalware platform.

-	**Active protection**: Reports telemetry metadata about detected threats and suspicious resources to Microsoft Azure to ensure rapid response to the evolving threat landscape, enabling real-time synchronous signature delivery through the Microsoft active protection system.

-	**Samples reporting**: Provides and reports samples to the Microsoft antimalware service to help refine the service and enable troubleshooting.

-	**Exclusions**: Allows application and service administrators to configure certain files, processes, and drives for exclusion from protection and scanning for performance and other reasons.

-	**Antimalware event collection**: Records the antimalware service health, suspicious activities, and remediation actions taken in the operating system event log and collects them into the customer’s Azure storage account.

### Azure SQL Database Threat Detection

[Azure SQL Database Threat Detection](https://azure.microsoft.com/blog/azure-sql-database-threat-detection-your-built-in-security-expert/) is a new security intelligence feature built into the Azure SQL Database service. Working around the clock to learn, profile, and detect anomalous database activities, Azure SQL Database Threat Detection identifies potential threats to the database.

Security officers or other designated administrators can get an immediate notification about suspicious database activities as they occur. Each notification provides details of the suspicious activity and recommends how to further investigate and mitigate the threat.

Currently, Azure SQL Database Threat Detection detects potential vulnerabilities and SQL injection attacks, and anomalous database access patterns.

Upon receiving a threat-detection email notification, users are able to navigate and view the relevant audit records through a deep link in the mail. The link opens an audit viewer or a preconfigured auditing Excel template that shows the relevant audit records around the time of the suspicious event, according to the following:

-	Audit storage for the database/server with the anomalous database activities.

-	Relevant audit storage table that was used at the time of the event to write the audit log.

-	Audit records of the hour immediately following the event occurrence.

-	Audit records with a similar event ID at the time of the event (optional for some detectors).

SQL Database threat detectors use one of the following detection methodologies:

-	**Deterministic detection**: Detects suspicious patterns (rules based) in the SQL client queries that match known attacks. This methodology has high detection and low false positive, but limited coverage because it falls within the category of “atomic detections.”

-	**Behavioral detection**: Detects anomalous activity, which is abnormal behavior in the database that was not seen during the most recent 30 days. Examples of SQL client anomalous activity can be a spike of failed logins or queries, a high volume of data being extracted, unusual canonical queries, or unfamiliar IP addresses used to access the database.

### Application Gateway Web Application Firewall

[Web Application Firewall (WAF)](../../app-service/environment/app-service-app-service-environment-web-application-firewall.md) is a feature of [Azure Application Gateway](../../application-gateway/application-gateway-web-application-firewall-overview.md) that provides protection to web applications that use an application gateway for standard [application delivery control](https://kemptechnologies.com/in/application-delivery-controllers) functions. Web Application Firewall does this by protecting them against most of the [Open Web Application Security Project (OWASP) top 10 common web vulnerabilities](https://owasp.org/www-project-top-ten/).

![Application Gateway Web Application Firewall diagram](./media/threat-detection/azure-threat-detection-fig13.png)

Protections include:

-	SQL injection protection.

-	Cross site scripting protection.

-	Common Web Attacks Protection, such as command injection, HTTP request smuggling, HTTP response splitting, and remote file inclusion attack.

-	Protection against HTTP protocol violations.

-	Protection against HTTP protocol anomalies, such as missing host user-agent and accept headers.

-	Prevention against bots, crawlers, and scanners.

-	Detection of common application misconfigurations (that is, Apache, IIS, and so on).

Configuring WAF at your application gateway provides the following benefits:

-	Protects your web application from web vulnerabilities and attacks without modification of the back-end code.

-	Protects multiple web applications at the same time behind an application gateway. An application gateway supports hosting up to 20 websites.

-	Monitors web applications against attacks by using real-time reports that are generated by application gateway WAF logs.

-	Helps meet compliance requirements. Certain compliance controls require all internet-facing endpoints to be protected by a WAF solution.

### Anomaly Detection API: Built with Azure Machine Learning

The Anomaly Detection API is an API that's useful for detecting a variety of anomalous patterns in your time series data. The API assigns an anomaly score to each data point in the time series, which can be used for generating alerts, monitoring through dashboards, or connecting with your ticketing systems.

The [Anomaly Detection API](../../machine-learning/team-data-science-process/apps-anomaly-detection-api.md) can detect the following types of anomalies on time series data:

-	**Spikes and dips**: When you're monitoring the number of login failures to a service or number of checkouts in an e-commerce site, unusual spikes or dips could indicate security attacks or service disruptions.

-	**Positive and negative trends**: When you're monitoring memory usage in computing, shrinking free memory size indicates a potential memory leak. For service queue length monitoring, a persistent upward trend might indicate an underlying software issue.

-	**Level changes and changes in dynamic range of values**: Level changes in latencies of a service after a service upgrade or lower levels of exceptions after upgrade can be interesting to monitor.

The machine learning-based API enables:

-	**Flexible and robust detection**: The anomaly detection models allow users to configure sensitivity settings and detect anomalies among seasonal and non-seasonal data sets. Users can adjust the anomaly detection model to make the detection API less or more sensitive according to their needs. This would mean detecting the less or more visible anomalies in data with and without seasonal patterns.

-	**Scalable and timely detection**: The traditional way of monitoring with present thresholds set by experts' domain knowledge are costly and not scalable to millions of dynamically changing data sets. The anomaly detection models in this API are learned, and models are tuned automatically from both historical and real-time data.

-	**Proactive and actionable detection**: Slow trend and level change detection can be applied for early anomaly detection. The early abnormal signals that are detected can be used to direct humans to investigate and act on the problem areas. In addition, root cause analysis models and alerting tools can be developed on top of this anomaly-detection API service.

The anomaly-detection API is an effective and efficient solution for a wide range of scenarios, such as service health and KPI monitoring, IoT, performance monitoring, and network traffic monitoring. Here are some popular scenarios where this API can be useful:

- IT departments need tools to track events, error code, usage log, and performance (CPU, memory, and so on) in a timely manner.

-	Online commerce sites want to track customer activities, page views, clicks, and so on.

-	Utility companies want to track consumption of water, gas, electricity, and other resources.

-	Facility or building management services want to monitor temperature, moisture, traffic, and so on.

-	IoT/manufacturers want to use sensor data in time series to monitor work flow, quality, and so on.

-	Service providers, such as call centers, need to monitor service demand trend, incident volume, wait queue length, and so on.

-	Business analytics groups want to monitor business KPIs' (such as sales volume, customer sentiments, or pricing) abnormal movement in real time.

### Cloud App Security

[Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) is a critical component of the Microsoft Cloud Security stack. It's a comprehensive solution that can help your organization as you move to take full advantage of the promise of cloud applications. It keeps you in control, through improved visibility into activity. It also helps increase the protection of critical data across cloud applications.

With tools that help uncover shadow IT, assess risk, enforce policies, investigate activities, and stop threats, your organization can more safely move to the cloud while maintaining control of critical data.

| | |
|---|---|
| Discover | Uncover shadow IT with Cloud App Security. Gain visibility by discovering apps, activities, users, data, and files in your cloud environment. Discover third-party apps that are connected to your cloud.|
|Investigate | Investigate your cloud apps by using cloud forensics tools to deep-dive into risky apps, specific users, and files in your network. Find patterns in the data collected from your cloud. Generate reports to monitor your cloud. |
| Control | Mitigate risk by setting policies and alerts to achieve maximum control over network cloud traffic. Use Cloud App Security to migrate your users to safe, sanctioned cloud app alternatives. |
| Protect | Use Cloud App Security to sanction or prohibit applications, enforce data loss prevention, control permissions and sharing, and generate custom reports and alerts. |
| Control | Mitigate risk by setting policies and alerts to achieve maximum control over network cloud traffic. Use Cloud App Security to migrate your users to safe, sanctioned cloud app alternatives. |
| | |


![Cloud App Security diagram](./media/threat-detection/azure-threat-detection-fig14.png)

Cloud App Security integrates visibility with your cloud by:

-	Using Cloud Discovery to map and identify your cloud environment and the cloud apps your organization is using.

-	Sanctioning and prohibiting apps in your cloud.

-	Using easy-to-deploy app connectors that take advantage of provider APIs, for visibility and governance of apps that you connect to.

-	Helping you have continuous control by setting, and then continually fine-tuning, policies.

On collecting data from these sources, Cloud App Security runs sophisticated analysis on it. It immediately alerts you to anomalous activities, and gives you deep visibility into your cloud environment. You can configure a policy in Cloud App Security and use it to protect everything in your cloud environment.

## Third-party Advanced Threat Detection capabilities through the Azure Marketplace

### Web Application Firewall

Web Application Firewall inspects inbound web traffic and blocks SQL injections, cross-site scripting, malware uploads, application DDoS attacks, and other attacks targeted at your web applications. It also inspects the responses from the back-end web servers for data loss prevention (DLP). The integrated access control engine enables administrators to create granular access control policies for authentication, authorization, and accounting (AAA), which gives organizations strong authentication and user control.

Web Application Firewall provides the following benefits:

-	Detects and blocks SQL injections, Cross-Site Scripting, malware uploads, application DDoS, or any other attacks against your application.

-	Authentication and access control.

-	Scans outbound traffic to detect sensitive data and can mask or block the information from being leaked out.

-	Accelerates the delivery of web application contents, using capabilities such as caching, compression, and other traffic optimizations.

For examples of web application firewalls that are available in the Azure Marketplace, see [Barracuda WAF, Brocade virtual web application firewall (vWAF), Imperva SecureSphere, and the ThreatSTOP IP firewall](https://azuremarketplace.microsoft.com/marketplace/apps/barracudanetworks.waf).

## Next steps

- [Responding to today’s threats](../../security-center/security-center-alerts-overview.md#respond-threats): Helps identify active threats that target your Azure resources and provides the insights you need to respond quickly.

- [Azure SQL Database Threat Detection](https://azure.microsoft.com/blog/azure-sql-database-threat-detection-your-built-in-security-expert/): Helps address your concerns about potential threats to your databases.
