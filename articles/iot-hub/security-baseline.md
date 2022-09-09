---
title: Azure security baseline for Azure IoT Hub
description: The Azure IoT Hub security baseline provides procedural guidance and resources for implementing the security recommendations specified in the Azure Security Benchmark.
author: msmbaldwin
ms.service: iot-hub
ms.topic: conceptual
ms.date: 09/03/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark

# Important: This content is machine generated; do not modify this topic directly. Contact mbaldwin for more information.

---

# Azure security baseline for Azure IoT Hub

The Azure Security Baseline for Microsoft Azure IoT Hub contains recommendations that will help you improve the security posture of your deployment. The baseline for this service is drawn from the [Azure Security Benchmark version 1.0](../security/benchmarks/overview.md), which provides recommendations on how you can secure your cloud solutions on Azure with our best practices guidance. For more information, see [Azure Security Baselines overview](../security/benchmarks/security-baselines-overview.md).

## Network security

*For more information, see the [Azure Security Benchmark: Network security](../security/benchmarks/security-control-network-security.md).*

### 1.1: Protect Azure resources within virtual networks

**Guidance**: By default, IoT Hub's hostnames map to a public endpoint with a publicly routable IP address over the internet. Different customers share this IoT Hub public endpoint, and IoT devices in over wide-area networks and on-premises networks can all access it.

IoT Hub features including message routing, file upload, and bulk device import/export also require connectivity from IoT Hub to a customer-owned Azure resource over its public endpoint. These connectivity paths collectively make up the egress traffic from IoT Hub to customer resources.

Recommend restricting connectivity to your Azure resources (including Azure IoT Hub) through a virtual network that you own and operate to reduce connectivity exposure in an isolated network and enable on-premises network connectivity directly to Azure backbone network. Use Azure Private Link and Azure Private Endpoint, where feasible, to enable private access to your services from other virtual networks.

Keep open hardware ports in your devices to a bare minimum to avoid unwanted access. Additionally, build mechanisms to prevent or detect physical tampering of the device.

- [IoT virtual networks support](virtual-network-support.md)
- [loT networking best practice](../iot-fundamentals/security-recommendations.md?context=azure%2fiot-hub%2frc%2frc#networking)
- [Azure Private Link overview](../private-link/private-link-overview.md)
- [Azure network security group](../virtual-network/network-security-groups-overview.md)

**Azure Security Center monitoring**: Yes

**Responsibility**: Customer

### 1.2: Monitor and log the configuration and traffic of virtual networks, subnets, and NICs

**Guidance**: Use Azure Security Center and follow the network protection recommendations to help secure your Azure network resources. Enable network security group flow logs and send the logs to an Azure Storage account for auditing. You can also send the flow logs to a Log Analytics workspace and then use Traffic Analytics to provide insights into traffic patterns in your Azure cloud. Some advantages of Traffic Analytics are the ability to visualize network activity, identify hot spots and security threats, understand traffic flow patterns, and pinpoint network misconfigurations.
 
- [How to enable network security group flow logs](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [How to enable and use Traffic Analytics](../network-watcher/traffic-analytics.md)
 
- [Understand network security provided by Azure Security Center](../security-center/security-center-network-recommendations.md)

**Azure Security Center monitoring**: Yes

**Responsibility**: Customer

### 1.3: Protect critical web applications

**Guidance**: Not applicable; this recommendation is intended for web applications running on Azure App Service or compute resources.

**Azure Security Center monitoring**: Not Applicable

**Responsibility**: Not Applicable

### 1.4: Deny communications with known malicious IP addresses

**Guidance**: Block known malicious IPs with IoT Hub IP filter rules . Malicious attempts are also recorded and alerted via Azure Security Center for IoT.

Azure DDoS Protection Basic is already enabled and available for no additional cost as part of IoT Hub. Always-on traffic monitoring, and real-time mitigation of common network-level attacks, provide the same defenses utilized by Microsoft's online services. The entire scale of Azure's global network can be used to distribute and mitigate attack traffic across regions.

- [IoT Hub IP filter](iot-hub-ip-filtering.md)

- [Azure Security Center for IoT suspicious IP address communication](../defender-for-iot/concept-security-alerts.md)

- [Manage Azure DDoS Protection Basic](../ddos-protection/ddos-protection-overview.md)

- [Threat protection in Azure Security Center](../security-center/azure-defender.md)

**Azure Security Center monitoring**: Yes

**Responsibility**: Customer

### 1.5: Record network packets

**Guidance**: Not applicable; this recommendation is intended for offerings that produce network packets that can be recorded and viewed by customers. IoT Hub does not produce network packets that are customer facing, and is not designed to deploy directly into Azure virtual networks.

**Azure Security Center monitoring**: No

**Responsibility**: Not Applicable

### 1.6: Deploy network-based intrusion detection/intrusion prevention systems (IDS/IPS)

**Guidance**: Select an offer from Azure Marketplace that supports IDS/IPS functionality with payload inspection capabilities.  When payload inspection is not a requirement, Azure Firewall threat intelligence can be used. Azure Firewall threat intelligence-based filtering is used to alert on and/or block traffic to and from known malicious IP addresses and domains. The IP addresses and domains are sourced from the Microsoft Threat Intelligence feed.

Deploy the firewall solution of your choice at each of your organization's network boundaries to detect and/or block malicious traffic. 

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [How to deploy Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

- [How to configure alerts with Azure Firewall](../firewall/threat-intel.md)

**Azure Security Center monitoring**: Not Applicable

**Responsibility**: Customer

### 1.7: Manage traffic to web applications

**Guidance**: Not applicable; this recommendation is intended for web applications running on Azure App Service or compute resources.

**Azure Security Center monitoring**: Not Applicable

**Responsibility**: Not Applicable

### 1.8: Minimize complexity and administrative overhead of network security rules

**Guidance**: For resources that need access to your Azure IoT Hub, use Virtual Network service tags to define network access controls on network security Groups or Azure Firewall. You can use service tags in place of specific IP addresses when creating security rules. By specifying the service tag name (for example, AzureIoTHub) in the appropriate source or destination field of a rule, you can allow or deny the traffic for the corresponding service. Microsoft manages the address prefixes encompassed by the service tag and automatically updates the service tag as addresses change.

- [How to use service tags for Azure IoT](iot-hub-understand-ip-address.md)
- [For more information about using service tags](../virtual-network/service-tags-overview.md)

**Azure Security Center monitoring**: Not Applicable

**Responsibility**: Customer

### 1.9: Maintain standard security configurations for network devices

**Guidance**: Define and implement standard security configurations for network resources associated with your Azure IoT Hub namespaces with Azure Policy. Use Azure Policy aliases in the "Microsoft.Devices" and "Microsoft.Network" namespaces to create custom policies to audit or enforce the network configuration of your Machine Learning namespaces. 

- [How to configure and manage Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center monitoring**: Not Applicable

**Responsibility**: Customer

### 1.10: Document traffic configuration rules

**Guidance**: Use tags for network resources associated with your Azure IoT Hub deployment in order to logically organize them into a taxonomy.

- [How to create and use tags](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center monitoring**: Not Applicable

**Responsibility**: Customer

### 1.11: Use automated tools to monitor network resource configurations and detect changes

**Guidance**: Use Azure Activity Log to monitor network resource configurations and detect changes for network resources related to Azure IoT Hub. Create alerts within Azure Monitor that will trigger when changes to critical network resources take place.

- [How to view and retrieve Azure Activity Log events](../azure-monitor/platform/activity-log.md#view-the-activity-log)

- [How to create alerts in Azure Monitor](../azure-monitor/platform/alerts-activity-log.md)

**Azure Security Center monitoring**: Not Applicable

**Responsibility**: Customer

## Logging and monitoring

*For more information, see the [Azure Security Benchmark: Logging and monitoring](../security/benchmarks/security-control-logging-monitoring.md).*

### 2.1: Use approved time synchronization sources

**Guidance**: Microsoft maintains the time source used for Azure resources such as Azure IoT Hub for timestamps in the logs.

**Azure Security Center monitoring**: Not Applicable

**Responsibility**: Microsoft

### 2.2: Configure central security log management

**Guidance**: Ingest logs via Azure Monitor to aggregate security data generated by Azure IoT Hub. In Azure Monitor, use Log Analytics workspaces to query and perform analytics, and use storage accounts for long-term/archival storage. Alternatively, you can enable and on-board data to Azure Sentinel or a third-party Security Incident and Event Management (SIEM).

- [Set up Azure IoT logs](monitor-iot-hub-reference.md#resource-logs)
- [How to onboard Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure Security Center monitoring**: Yes

**Responsibility**: Customer

### 2.3: Enable audit logging for Azure resources

**Guidance**: Enable Azure IoT diagnostic settings on Azure resources for access to audit, security, and resource logs. Activity logs, which are automatically available, include event source, date, user, timestamp, source addresses, destination addresses, and other useful elements.

- [Set up Azure IoT Hub logs](monitor-iot-hub-reference.md#resource-logs)

- [How to collect platform logs and metrics with Azure Monitor](../azure-monitor/platform/diagnostic-settings.md)

- [Understand logging and different log types in Azure](../azure-monitor/platform/platform-logs-overview.md)

**Azure Security Center monitoring**: Yes

**Responsibility**: Customer

### 2.4: Collect security logs from operating systems

**Guidance**: Not applicable; this recommendation is intended for compute resources.

**Azure Security Center monitoring**: Not Applicable

**Responsibility**: Not Applicable

### 2.5: Configure security log storage retention

**Guidance**: In Azure Monitor, set the log retention period for Log Analytics workspaces associated with your Azure IoT Hub instances according to your organization's compliance regulations.

- [How to set log retention parameters](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

**Azure Security Center monitoring**: Not Applicable

**Responsibility**: Customer

### 2.6: Monitor and review Logs

**Guidance**: Analyze and monitor logs for anomalous behavior and regularly review the results from your Azure IoT Hub. Use Azure Monitor and a Log Analytics workspace to review logs and perform queries on log data.

Alternatively, you can enable and on-board data to Azure Sentinel or a third-party SIEM. 

- [Monitor Azure IoT health](monitor-iot-hub.md)
- [How to onboard Azure Sentinel](../sentinel/quickstart-onboard.md)
  
- [Getting started with Log Analytics queries](../azure-monitor/log-query/log-analytics-tutorial.md)
   
- [ How to perform custom queries in Azure Monitor](../azure-monitor/log-query/get-started-queries.md)

**Azure Security Center monitoring**: Not Applicable

**Responsibility**: Customer

### 2.7: Enable alerts for anomalous activities

**Guidance**: Use Azure Security Center for IoT with a Log Analytics workspace for monitoring and alerting on anomalous activity found in security logs and events. Alternatively, you can enable and on-board data to Azure Sentinel. You can also define operational alerts with Azure Monitor that may have security implications, such as when traffic drops unexpectedly.

- [Monitor Azure IoT Hub health](monitor-iot-hub.md)
- [How to onboard Azure Sentinel](../sentinel/quickstart-onboard.md)
- [Azure Security Center for IoT alerts](../defender-for-iot/concept-security-alerts.md)

- [How to alert on log analytics log data](../azure-monitor/learn/tutorial-response.md)

**Azure Security Center monitoring**: Yes

**Responsibility**: Customer

### 2.8: Centralize anti-malware logging

**Guidance**: Not applicable; Azure IoT Hub does not process or produce anti-malware related logs.

**Azure Security Center monitoring**: Not Applicable

**Responsibility**: Not Applicable

### 2.9: Enable DNS query logging

**Guidance**: Not applicable; Azure IoT Hub does not process or produce DNS-related logs.

**Azure Security Center monitoring**: Not Applicable

**Responsibility**: Not Applicable

### 2.10: Enable command-line audit logging

**Guidance**: Not applicable; this recommendation is intended for compute resources.

**Azure Security Center monitoring**: Not Applicable

**Responsibility**: Not Applicable

## Identity and access control

*For more information, see the [Azure Security Benchmark: Identity and access control](../security/benchmarks/security-control-identity-access-control.md).*

### 3.1: Maintain an inventory of administrative accounts

**Guidance**: Azure role-based access control (Azure RBAC) allows you to manage access to Azure IoT hub through role assignments. You can assign these roles to users, groups service principals, and managed identities. There are pre-defined built-in roles for certain resources, and these roles can be inventoried or queried through tools such as Azure CLI, or Azure PowerShell, or the Azure portal. 

- [How to get a directory role in Azure AD with PowerShell](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [How to get members of a directory role in Azure AD with PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Azure Security Center monitoring**: Yes

**Responsibility**: Customer

### 3.2: Change default passwords where applicable

**Guidance**: Access management to Azure IoT Hub resources is controlled through Azure Active Directory (Azure AD). Azure AD does not have the concept of default passwords.

**Azure Security Center monitoring**: Not Applicable

**Responsibility**: Customer

### 3.3: Use dedicated administrative accounts

**Guidance**: Create standard operating procedures around the use of dedicated administrative accounts.

You can also enable just-in-time access to administrative accounts by using Azure AD Privileged Identity Management and Azure Resource Manager.

- [Learn more about Privileged Identity Management](../active-directory/privileged-identity-management/index.yml)

**Azure Security Center monitoring**: Yes

**Responsibility**: Customer

### 3.4: Use single sign-on (SSO) with Azure Active Directory

**Guidance**: For users accessing IoT Hub, use Azure Active Directory SSO. Use Azure Security Center identity and access recommendations. 

- [Understand SSO with Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure Security Center monitoring**: Not Applicable

**Responsibility**: Customer

### 3.5: Use multi-factor authentication for all Azure Active Directory based access

**Guidance**: 
Enable Azure AD MFA to protect your overall Azure tenant, benefiting all services. IoT Hub service doesn't have MFA support.  

- [How to enable MFA in Azure](../active-directory/authentication/howto-mfa-getstarted.md) 

- [How to monitor identity and access within Azure Security Center](../security-center/security-center-identity-access.md)

**Azure Security Center monitoring**: Yes

**Responsibility**: Customer

### 3.6: Use dedicated machines (Privileged Access Workstations) for all administrative tasks

**Guidance**: Use a secure, Azure-managed workstation (also known as a Privileged Access Workstation, or PAW) for administrative tasks that require elevated privileges.

- [Understand secure, Azure-managed workstations](../active-directory/devices/concept-azure-managed-workstation.md)

- [How to enable Azure AD MFA](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure Security Center monitoring**: Not Applicable

**Responsibility**: Customer

### 3.7: Log and alert on suspicious activities from administrative accounts

**Guidance**: Use Azure Active Directory security reports and monitoring to detect when suspicious or unsafe activity occurs in the environment. Use Azure Security Center to monitor identity and access activity.

- [How to identify Azure AD users flagged for risky activity](../active-directory/identity-protection/overview-identity-protection.md)
- [How to monitor users' identity and access activity in Azure Security Center](../security-center/security-center-identity-access.md)

**Azure Security Center monitoring**: Yes

**Responsibility**: Customer

### 3.8: Manage Azure resources only from approved locations

**Guidance**: 
For users accessing IoT Hub, conditional access isn't supported. To mitigate this, use Azure AD named locations to allow access only from specific logical groupings of IP address ranges or countries/regions for your overall Azure tenant, benefitting all services including IoT Hub. 

- [How to configure Azure AD named locations](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure Security Center monitoring**: Not Applicable

**Responsibility**: Customer

### 3.9: Use Azure Active Directory

**Guidance**: For user access to IoT Hub, Use Azure Active Directory (Azure AD) as the central authentication and authorization system. Azure AD protects data by using strong encryption for data at rest and in transit. Azure AD also salts, hashes, and securely stores user credentials.

For device and service access, IoT Hub uses security tokens and Shared Access Signature (SAS) tokens to authenticate devices and services to avoid sending keys on network. 

- [How to create and configure an Azure AD instance](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)
- [IoT Hub security tokens](../iot-fundamentals/iot-security-deployment.md#iot-hub-security-tokens)

**Azure Security Center monitoring**: Not Applicable

**Responsibility**: Customer

### 3.10: Regularly review and reconcile user access

**Guidance**: Azure AD provides logs to help discover stale accounts. In addition, use Azure AD identity and access reviews to efficiently manage group memberships, access to enterprise applications, and role assignments. User access can be reviewed on a regular basis to make sure only the right users have continued access. 

Use Azure AD Privileged Identity Management (PIM) for generation of logs and alerts when suspicious or unsafe activity occurs in the environment.

- [Understand Azure AD reporting](../active-directory/reports-monitoring/index.yml)
- [How to use Azure AD identity and access reviews](../active-directory/governance/access-reviews-overview.md)
- [Deploy Azure AD Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

**Azure Security Center monitoring**: Yes

**Responsibility**: Customer

### 3.11: Monitor attempts to access deactivated credentials

**Guidance**: 
You have access to Azure AD sign-in activity, audit, and risk event log sources, which allow you to integrate with any SIEM/monitoring tool. 

You can streamline this process by creating diagnostic settings for Azure AD user accounts and sending the audit logs and sign-in logs to a Log Analytics workspace. You can configure desired alerts within Log Analytics workspace. 

User Azure Monitor resource logs to monitor unauthorized connection attempts in the Connections category.

- [How to integrate Azure activity logs with Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Configure resource logs for IoT hub](monitor-iot-hub.md#collection-and-routing)

**Azure Security Center monitoring**: Not Applicable

**Responsibility**: Customer

### 3.12: Alert on account login behavior deviation

**Guidance**: Use Azure AD Identity Protection features to configure automated responses to detected suspicious actions related to user identities. You can also ingest data into Azure Sentinel for further investigation.
  
- [ How to view Azure AD risky sign-ins](../active-directory/identity-protection/overview-identity-protection.md)
  
- [ How to configure and enable Identity Protection risk policies](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)
  
- [ How to onboard Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure Security Center monitoring**: Not Applicable

**Responsibility**: Customer

### 3.13: Provide Microsoft with access to relevant customer data during support scenarios

**Guidance**: In support scenarios where Microsoft needs to access customer data, it will be requested directly from the customer. 

**Azure Security Center monitoring**: Not Applicable

**Responsibility**: Customer

## Data protection

*For more information, see the [Azure Security Benchmark: Data protection](../security/benchmarks/security-control-data-protection.md).*

### 4.1: Maintain an inventory of sensitive Information

**Guidance**: Use tags to assist in tracking Azure resources that store or process sensitive information.
 
- [ How to create and use tags](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center monitoring**: Not Applicable

**Responsibility**: Customer

### 4.2: Isolate systems storing or processing sensitive information

**Guidance**: Implement isolation using separate subscriptions and management groups for individual security domains such as environment type and data sensitivity level. You can restrict the level of access to your Azure resources that your applications and enterprise environments demand. You can control access to Azure resources via Azure RBAC.
  
- [ How to create additional Azure subscriptions](../cost-management-billing/manage/create-subscription.md)
- [ How to create management groups](../governance/management-groups/create-management-group-portal.md)
- [ How to create and use tags](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center monitoring**: Not Applicable

**Responsibility**: Customer

### 4.3: Monitor and block unauthorized transfer of sensitive information

**Guidance**: Use a third-party solution from Azure Marketplace in network perimeters to monitor for unauthorized transfer of sensitive information and block such transfers while alerting information security professionals.

For the underlying platform managed by Microsoft, Microsoft treats all customer content as sensitive and guards against customer data loss and exposure. To ensure customer data within Azure remains secure, Microsoft has implemented and maintains a suite of robust data protection controls and capabilities.

- [Understand customer data protection in Azure](../security/fundamentals/protection-customer-data.md)

**Azure Security Center monitoring**: Not Applicable

**Responsibility**: Customer

### 4.4: Encrypt all sensitive information in transit

**Guidance**: IoT Hub uses Transport Layer Security (TLS) to secure connections from IoT devices and services. Three versions of the TLS protocol are currently supported, namely versions 1.0, 1.1, and 1.2. It is strongly recommended that you use TLS 1.2 as the preferred TLS version when connecting to IoT Hub.

Follow Azure Security Center recommendations for encryption at rest and encryption in transit, where applicable.

- [TLS support in IoT Hub](iot-hub-tls-support.md)
- [Understand encryption in transit with Azure](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

**Azure Security Center monitoring**: Not Applicable

**Responsibility**: Customer

### 4.5: Use an active discovery tool to identify sensitive data

**Guidance**: Data identification, classification, and loss prevention features are not yet available for Azure IoT Hub. Implement a third-party solution if required for compliance purposes.

For the underlying Azure platform managed by Microsoft, Microsoft treats all customer content as sensitive and goes to great lengths to guard against customer data loss and exposure. To ensure customer data within Azure remains secure, Microsoft has implemented and maintains a suite of robust data protection controls and capabilities.

- [Understand customer data protection in Azure](../security/fundamentals/protection-customer-data.md)

**Azure Security Center monitoring**: Not Applicable

**Responsibility**: Customer

### 4.6: Use Azure RBAC to manage access to resources

**Guidance**: For control plane user access to IoT Hub, use Azure RBAC to control access. For data plane access to IoT Hub, use shared access policies for IoT Hub.

- [How to configure Azure RBAC](../role-based-access-control/role-assignments-portal.md)

- [Control access to IoT Hub](iot-hub-devguide-security.md)

**Azure Security Center monitoring**: Not Applicable

**Responsibility**: Customer

### 4.7: Use host-based data loss prevention to enforce access control

**Guidance**: Not applicable; this guideline is intended for compute resources.

Microsoft manages the underlying infrastructure for Azure IoT Hub and has implemented strict controls to prevent the loss or exposure of customer data.

- [Understand customer data protection in Azure](../security/fundamentals/protection-customer-data.md)

**Azure Security Center monitoring**: Not Applicable

**Responsibility**: Microsoft

### 4.8: Encrypt sensitive information at rest

**Guidance**: IoT Hub supports encryption of data at rest with customer-managed keys (CMK), also known as "bring your own key" (BYOK).

Azure IoT Hub provides encryption of data at rest and in-transit as it is written in our datacenters and decrypts it for you as you access it. By default, IoT Hub uses Microsoft-managed keys to encrypt the data at rest.

- [Encryption of data at rest with customer-managed keys for IoT Hub](iot-hub-customer-managed-keys.md)

- [Understand encryption at rest in Azure](../security/fundamentals/encryption-atrest.md)

**Azure Security Center monitoring**: Not Applicable

**Responsibility**: Microsoft

### 4.9: Log and alert on changes to critical Azure resources

**Guidance**: Use Azure Monitor with the Azure Activity log to create alerts for when changes take place to production instances of Azure IoT Hub and other critical or related resources.

- [How to create alerts for Azure Activity Log events](../azure-monitor/platform/alerts-activity-log.md)

**Azure Security Center monitoring**: Not Applicable

**Responsibility**: Customer

## Vulnerability management

*For more information, see the [Azure Security Benchmark: Vulnerability management](../security/benchmarks/security-control-vulnerability-management.md).*

### 5.1: Run automated vulnerability scanning tools

**Guidance**: Not applicable; Microsoft performs vulnerability management on the underlying systems that support Azure IoT Hub.

**Azure Security Center monitoring**: Not Applicable

**Responsibility**: Microsoft

### 5.2: Deploy automated operating system patch management solution

**Guidance**: Not applicable; Microsoft performs patch management on the underlying systems that support Azure IoT Hub. 

**Azure Security Center monitoring**: Not Applicable

**Responsibility**: Microsoft

### 5.3: Deploy an automated patch management solution for third-party software titles

**Guidance**: Not applicable; this guideline is intended for compute resources.

**Azure Security Center monitoring**: Not Applicable

**Responsibility**: Not Applicable

### 5.4: Compare back-to-back vulnerability scans

**Guidance**: Not applicable; this guideline is intended for compute resources.

**Azure Security Center monitoring**: Not Applicable

**Responsibility**: Not Applicable

### 5.5: Use a risk-rating process to prioritize the remediation of discovered vulnerabilities

**Guidance**: Not applicable; this guideline is intended for compute resources.

**Azure Security Center monitoring**: Not Applicable

**Responsibility**: Not Applicable

## Inventory and asset management

*For more information, see the [Azure Security Benchmark: Inventory and asset management](../security/benchmarks/security-control-inventory-asset-management.md).*

### 6.1: Use automated asset discovery solution

**Guidance**: Not applicable; this guideline is intended for compute resources.

**Azure Security Center monitoring**: Not Applicable

**Responsibility**: Not Applicable

### 6.2: Maintain asset metadata

**Guidance**: Apply tags to Azure resources (not all resources support tags, but most do) to logically organize them into a taxonomy.

- [How to create and use tags](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center monitoring**: Not Applicable

**Responsibility**: Customer

### 6.3: Delete unauthorized Azure resources

**Guidance**: Use tagging, management groups, and separate subscriptions where appropriate, to organize and track assets. Reconcile inventory on a regular basis and ensure unauthorized resources are deleted from the subscription in a timely manner.
  
- [ How to create additional Azure subscriptions](../cost-management-billing/manage/create-subscription.md)
  
- [ How to create management groups](../governance/management-groups/create-management-group-portal.md)
  
- [ How to create and use tags](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center monitoring**: Not Applicable

**Responsibility**: Customer

### 6.4: Define and maintain an inventory of approved Azure resources

**Guidance**: Create an inventory of approved Azure resources and approved software for compute resources as per your organizational needs.

Each IoT Hub has an identity registry that can be used to create per-device resources in the service. Individual or groups of device identities can be added to an allow list, or a block list, enabling complete control over device access.

- [IoT Hub identity registry](iot-hub-devguide-identity-registry.md)

**Azure Security Center monitoring**: Not Applicable

**Responsibility**: Customer

### 6.5: Monitor for unapproved Azure resources

**Guidance**: 
Use Azure Policy to put restrictions on the type of resources that can be created in your subscriptions. 

Use Azure Resource Graph to query for and discover resources within their subscriptions.  Ensure that all Azure resources present in the environment are approved. 

- [How to configure and manage Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [How to create queries with Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

**Azure Security Center monitoring**: Not Applicable

**Responsibility**: Customer

### 6.6: Monitor for unapproved software applications within compute resources

**Guidance**: Not applicable; this recommendation is intended for compute resources.

**Azure Security Center monitoring**: Not Applicable

**Responsibility**: Not Applicable

### 6.7: Remove unapproved Azure resources and software applications

**Guidance**: Not applicable; this recommendation is intended for compute resources.

**Azure Security Center monitoring**: Not Applicable

**Responsibility**: Not Applicable

### 6.8: Use only approved applications

**Guidance**: Not applicable; this recommendation is intended for compute resources.

**Azure Security Center monitoring**: Not Applicable

**Responsibility**: Not Applicable

### 6.9: Use only approved Azure services

**Guidance**: Use Azure Policy to put restrictions on the type of resources that can be created in customer subscriptions using the following built-in policy definitions:

* Not allowed resource types
* Allowed resource types

In addition, use the Azure Resource Graph to query/discover resources within the subscriptions.

- [How to configure and manage Azure Policy](../governance/policy/tutorials/create-and-manage.md)
- [How to create queries with Azure Graph](../governance/resource-graph/first-query-portal.md)

**Azure Security Center monitoring**: Not Applicable

**Responsibility**: Customer

### 6.10: Maintain an inventory of approved software titles

**Guidance**: Not applicable; this recommendation is intended for compute resources.

**Azure Security Center monitoring**: Not Applicable

**Responsibility**: Not Applicable

### 6.11: Limit users' ability to interact with Azure Resource Manager

**Guidance**: Use Azure AD Conditional Access to limit users' ability to interact with Azure Resource Manager by configuring "Block access" for the "Microsoft Azure Management" App.
  
- [ How to configure Conditional Access to block access to Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center monitoring**: Not Applicable

**Responsibility**: Customer

### 6.12: Limit users' ability to execute scripts in compute resources

**Guidance**: Not applicable; this recommendation is intended for compute resources.

**Azure Security Center monitoring**: Not Applicable

**Responsibility**: Not Applicable

### 6.13: Physically or logically segregate high risk applications

**Guidance**: Not applicable; this recommendation is intended for web applications running on Azure App Service or compute resources.

**Azure Security Center monitoring**: Not Applicable

**Responsibility**: Not Applicable

## Secure configuration

*For more information, see the [Azure Security Benchmark: Secure configuration](../security/benchmarks/security-control-secure-configuration.md).*

### 7.1: Establish secure configurations for all Azure resources

**Guidance**: Define and implement standard security configurations for your Azure Iot Hub service with Azure Policy. Use Azure Policy aliases in the "Microsoft.Devices" namespace to create custom policies to audit or enforce the configuration of your Azure IoT Hub services.

Azure Resource Manager has the ability to export the template in JavaScript Object Notation (JSON), which should be reviewed to ensure that the configurations meet the security requirements for your organization.

You can also use the recommendations from Azure Security Center as a secure configuration baseline for your Azure resources.

- [How to view available Azure Policy aliases](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Tutorial: Create and manage policies to enforce compliance](../governance/policy/tutorials/create-and-manage.md)

- [Single and multi-resource export to a template in Azure portal](../azure-resource-manager/templates/export-template-portal.md)

- [Security recommendations - a reference guide](../security-center/recommendations-reference.md)

**Azure Security Center monitoring**: Not Applicable

**Responsibility**: Customer

### 7.2: Establish secure operating system configurations

**Guidance**: Not applicable; this guideline is intended for compute resources.

**Azure Security Center monitoring**: Not Applicable

**Responsibility**: Not Applicable

### 7.3: Maintain secure Azure resource configurations

**Guidance**: Use Azure Policy [deny] and [deploy if not exist] to enforce secure settings across your Azure resources. In addition, you can use Azure Resource Manager templates to maintain the security configuration of your Azure resources required by your organization.  
 
- [Understand Azure Policy effects](../governance/policy/concepts/effects.md)
- [Create and manage policies to enforce compliance](../governance/policy/tutorials/create-and-manage.md)
- [Azure Resource Manager templates overview](../azure-resource-manager/templates/overview.md)

**Azure Security Center monitoring**: Not Applicable

**Responsibility**: Customer

### 7.4: Maintain secure operating system configurations

**Guidance**: Not applicable; this guideline is intended for compute resources.

**Azure Security Center monitoring**: Not Applicable

**Responsibility**: Not Applicable

### 7.5: Securely store configuration of Azure resources

**Guidance**: If using custom Azure Policy definitions for your Azure IoT Hub or related resources, use Azure Repos to securely store and manage your code.

- [How to store code in Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)
- [Azure Repos Documentation](/azure/devops/repos/index?view=azure-devops)

**Azure Security Center monitoring**: Not Applicable

**Responsibility**: Customer

### 7.6: Securely store custom operating system images

**Guidance**: Not applicable; this guideline is intended for compute resources.

**Azure Security Center monitoring**: Not Applicable

**Responsibility**: Not Applicable

### 7.7: Deploy configuration management tools for Azure resources

**Guidance**: Use Azure Policy aliases in the "Microsoft.Devices" namespace to create custom policies to alert, audit, and enforce system configurations. Additionally, develop a process and pipeline for managing policy exceptions.

- [How to configure and manage Azure Policy](../governance/policy/tutorials/create-and-manage.md)
- [How to use aliases](../governance/policy/concepts/definition-structure.md#aliases)

**Azure Security Center monitoring**: Not Applicable

**Responsibility**: Customer

### 7.8: Deploy configuration management tools for operating systems

**Guidance**: Not applicable; this guideline is intended for compute resources.

**Azure Security Center monitoring**: Not Applicable

**Responsibility**: Not Applicable

### 7.9: Implement automated configuration monitoring for Azure resources

**Guidance**: Use Azure Security Center to perform baseline scans for your Azure Resources. Additionally, use Azure Policy to alert and audit Azure resource configurations. 
 
- [ How to remediate recommendations in Azure Security Center](../security-center/security-center-remediate-recommendations.md)

**Azure Security Center monitoring**: Yes

**Responsibility**: Customer

### 7.10: Implement automated configuration monitoring for operating systems

**Guidance**: Not applicable; this guideline is intended for compute resources.

**Azure Security Center monitoring**: Not Applicable

**Responsibility**: Not Applicable

### 7.11: Manage Azure secrets securely

**Guidance**: IoT Hub uses security tokens and Shared Access Signature (SAS) tokens to authenticate devices and services to avoid sending keys on network. 

Use managed identities in conjunction with Azure Key Vault to simplify secret management for your cloud applications.

- [IoT Hub security tokens](../iot-fundamentals/iot-security-deployment.md#iot-hub-security-tokens)
- [How to use managed identities for IoT Hub](virtual-network-support.md#turn-on-managed-identity-for-iot-hub)

- [How to create a key vault](../key-vault/general/quick-create-portal.md)
- [How to provide Key Vault authentication with a managed identity](../key-vault/general/assign-access-policy-portal.md)

**Azure Security Center monitoring**: Yes

**Responsibility**: Customer

### 7.12: Manage identities securely and automatically

**Guidance**: IoT Hub uses security tokens and Shared Access Signature (SAS) tokens to authenticate devices and services to avoid sending keys on the network. 

Use managed identities to provide Azure services with an automatically managed identity in Azure AD. Managed identities allow you to authenticate to any service that supports Azure AD authentication, including Key Vault, without any credentials in your code.

- [IoT Hub security tokens](../iot-fundamentals/iot-security-deployment.md#iot-hub-security-tokens)
- [How to configure managed identities for IoT Hub](virtual-network-support.md#turn-on-managed-identity-for-iot-hub)

**Azure Security Center monitoring**: Not Applicable

**Responsibility**: Customer

### 7.13: Eliminate unintended credential exposure

**Guidance**: Implement Credential Scanner to identify credentials within code. Credential Scanner will also encourage moving discovered credentials to more secure locations such as Azure Key Vault. 
 
- [  How to set up Credential Scanner](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center monitoring**: Not Applicable

**Responsibility**: Customer

## Malware defense

*For more information, see the [Azure Security Benchmark: Malware defense](../security/benchmarks/security-control-malware-defense.md).*

### 8.1: Use centrally managed antimalware software

**Guidance**: Not applicable; this recommendation is intended for compute resources.

Microsoft anti-malware is enabled on the underlying host that supports Azure services (for example, Azure App Service), however it does not run on customer content.

**Azure Security Center monitoring**: Not Applicable

**Responsibility**: Microsoft

### 8.2: Pre-scan files to be uploaded to non-compute Azure resources

**Guidance**: Microsoft Anti-malware is enabled on the underlying host that supports Azure services (for example, Azure IoT Hub), however it does not run on customer content.

It is your responsibility to pre-scan any content being uploaded to non-compute Azure resources. Microsoft cannot access customer data, and therefore cannot conduct anti-malware scans of customer content on your behalf.

**Azure Security Center monitoring**: Not Applicable

**Responsibility**: Customer

### 8.3: Ensure antimalware software and signatures are updated

**Guidance**: Not applicable; this benchmark is intended for compute resources. Microsoft Antimalware is enabled on the underlying host that supports Azure services, however it does not run on customer content.

**Azure Security Center monitoring**: Not Applicable

**Responsibility**: Microsoft

## Data recovery

*For more information, see the [Azure Security Benchmark: Data recovery](../security/benchmarks/security-control-data-recovery.md).*

### 9.1: Ensure regular automated back ups

**Guidance**: Azure IoT Hub service provides methods and framework to make your IoT Hub services highly available and recoverable from disasters based on specific business objectives. 

- [IoT Hub high availability and disaster recovery](iot-hub-ha-dr.md)

- [How to clone IoT Hub](iot-hub-how-to-clone.md)

**Azure Security Center monitoring**: Not Applicable

**Responsibility**: Customer

### 9.2: Perform complete system backups and backup any customer-managed keys

**Guidance**: Azure IoT Hub recommends the secondary IoT hub must contain all device identities that can connect to the solution. The solution should keep geo-replicated backups of device identities, and upload them to the secondary IoT hub before switching the active endpoint for the devices. The device identity export functionality of IoT Hub is useful in this context.

- [IoT Hub high availability and disaster recovery](iot-hub-ha-dr.md#achieve-cross-region-ha)

- [IoT Hub device identity export](iot-hub-bulk-identity-mgmt.md)

**Azure Security Center monitoring**: Not Applicable

**Responsibility**: Customer

### 9.3: Validate all backups including customer-managed keys

**Guidance**: Azure IoT Hub recommends the secondary IoT hub must contain all device identities that can connect to the solution. The solution should keep geo-replicated backups of device identities, and upload them to the secondary IoT hub before switching the active endpoint for the devices. The device identity export functionality of IoT Hub is useful in this context.

Periodically perform data restoration of content in backup. Ensure that you can restore backed-up customer-managed keys.

- [IoT Hub high availability and disaster recovery](iot-hub-ha-dr.md#achieve-cross-region-ha)

- [IoT Hub device identity export](iot-hub-bulk-identity-mgmt.md)

**Azure Security Center monitoring**: Not Applicable

**Responsibility**: Customer

### 9.4: Ensure protection of backups and customer-managed keys

**Guidance**: Enable soft delete and purge protection in Key Vault to protect keys against accidental or malicious deletion. If Azure Storage is used to store backups, enable soft delete to save and recover your data when blobs or blob snapshots are deleted.
 
 
- [Understand Azure RBAC](../role-based-access-control/overview.md)
- [Soft delete for Azure Blob storage](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Azure Security Center monitoring**: Not Applicable

**Responsibility**: Customer

## Incident response

*For more information, see the [Azure Security Benchmark: Incident response](../security/benchmarks/security-control-incident-response.md).*

### 10.1: Create an incident response guide

**Guidance**: Develop an incident response guide for your organization. Ensure there are written incident response plans that define all the roles of personnel as well as the phases of incident handling and management from detection to post-incident review. 
  
- [ Guidance on building your own security incident response process](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)
  
- [ Microsoft Security Response Center's Anatomy of an Incident](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)
 
- [  Use NIST's Computer Security Incident Handling Guide to aid in the creation of your own incident response plan](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure Security Center monitoring**: Not Applicable

**Responsibility**: Customer

### 10.2: Create an incident scoring and prioritization procedure

**Guidance**: Azure Security Center assigns a severity to each alert to help you prioritize which alerts should be investigated first. The severity is based on how confident Security Center is in the finding or the analytically used to issue the alert as well as the confidence level that there was malicious intent behind the activity that led to the alert.

  
 Additionally, mark subscriptions using tags and create a naming system to identify and categorize Azure resources, especially those processing sensitive data. It's your responsibility to prioritize the remediation of alerts based on the criticality of the Azure resources and environment where the incident occurred.
  
- [ Security alerts in Azure Security Center](../security-center/security-center-alerts-overview.md)
  
- [ Use tags to organize your Azure resources](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center monitoring**: Yes

**Responsibility**: Customer

### 10.3: Test security response procedures

**Guidance**: Conduct exercises to test your systems' incident response capabilities on a regular cadence to help protect your Azure resources. Identify weak points and gaps and then revise your response plan as needed.
  
- [ NIST's publication--Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Azure Security Center monitoring**: Not Applicable

**Responsibility**: Customer

### 10.4: Provide security incident contact details and configure alert notifications for security incidents

**Guidance**: Security incident contact information will be used by Microsoft to contact you if the Microsoft Security Response Center (MSRC) discovers that your data has been accessed by an unlawful or unauthorized party. Review incidents after the fact to ensure that issues are resolved.
  
- [ How to set the Azure Security Center security contact](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center monitoring**: Not Applicable

**Responsibility**: Customer

### 10.5: Incorporate security alerts into your incident response system

**Guidance**: Export your Azure Security Center alerts and recommendations using the continuous export feature to help identify risks to Azure resources. Continuous export allows you to export alerts and recommendations either manually or in an ongoing, continuous fashion. You can use the Azure Security Center data connector to stream the alerts to Azure Sentinel.
  
- [ How to configure continuous export](../security-center/continuous-export.md)
 
- [ How to stream alerts into Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure Security Center monitoring**: Not Applicable

**Responsibility**: Customer

### 10.6: Automate the response to security alerts

**Guidance**: Use workflow automation feature Azure Security Center to automatically trigger responses to security alerts and recommendations to protect your Azure resources.
  
- [ How to configure workflow automation in Security Center](../security-center/workflow-automation.md)

**Azure Security Center monitoring**: Not Applicable

**Responsibility**: Customer

## Penetration tests and red team exercises

*For more information, see the [Azure Security Benchmark: Penetration tests and red team exercises](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### 11.1: Conduct regular penetration testing of your Azure resources and ensure remediation of all critical security findings

**Guidance**: Follow the Microsoft Cloud Penetration Testing Rules of Engagement to ensure your penetration tests are not in violation of Microsoft policies. Use Microsoft's strategy and execution of Red Teaming and live site penetration testing against Microsoft-managed cloud infrastructure, services, and applications.

- [Penetration Testing Rules of Engagement](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center monitoring**: Not Applicable

**Responsibility**: Shared

## Next steps

- See the [Azure security benchmark](../security/benchmarks/overview.md)
- Learn more about [Azure security baselines](../security/benchmarks/security-baselines-overview.md)