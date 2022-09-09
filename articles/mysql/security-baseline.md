---
title: Azure Security Baseline for Azure Database for MySQL
description: Azure Security Baseline for Azure Database for MySQL
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: mbaldwin
ms.custom: security-benchmark

---

# Azure Security Baseline for Azure Database for MySQL

The Azure Security Baseline for Azure Database for MySQL contains recommendations that will help you improve the security posture of your deployment.

The baseline for this service is drawn from the [Azure Security Benchmark version 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview), which provides recommendations on how you can secure your cloud solutions on Azure with our best practices guidance.

For more information, see [Azure Security Baselines overview](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## Network Security

*For more information, see [Security Control: Network Security](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### 1.1: Protect resources using Network Security Groups or Azure Firewall on your Virtual Network

**Guidance**: Configure Private Link for Azure Database for MySQL with Private Endpoints. Private Link allows you to connect to various PaaS services in Azure via a private endpoint. Azure Private Link essentially brings Azure services inside your private Virtual Network (VNet). Traffic between your virtual network and MySQL instance travels the Microsoft backbone network.

Alternatively, you may use Virtual Network Service Endpoints to protect and limit network access to your Azure Database for MySQL implementations. Virtual network rules are one firewall security feature that controls whether your Azure Database for MySQL server accepts communications that are sent from particular subnets in virtual networks.

You may also secure your Azure Database for MySQL server with firewall rules. The server firewall prevents all access to your database server until you specify which computers have permission. To configure your firewall, you create firewall rules that specify ranges of acceptable IP addresses. You can create firewall rules at the server level.

How to configure Private Link for Azure Database for MySQL: https://docs.microsoft.com/azure/mysql/howto-configure-privatelink-portal

How to create and manage VNet service endpoints and VNet rules in Azure Database for MySQL: https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview

How to configure Azure Database for MySQL firewall rules: https://docs.microsoft.com/azure/mysql/howto-manage-firewall-using-portal

**Azure Security Center monitoring**: Not available

**Responsibility**: Customer

### 1.2: Monitor and log the configuration and traffic of Vnets, Subnets, and NICs

**Guidance**: When your Azure Database for MySQL instance is secured to a private endpoint, you can deploy virtual machines in the same virtual network. You can use a network security group (NSG) to reduce the risk of data exfiltration. Enable NSG flow logs and send logs into a Storage Account for traffic audit. You may also send NSG flow logs to a Log Analytics workspace and use Traffic Analytics to provide insights into traffic flow in your Azure cloud. Some advantages of Traffic Analytics are the ability to visualize network activity and identify hot spots, identify security threats, understand traffic flow patterns, and pinpoint network misconfigurations.

How to configure Private Link for Azure Database for MySQL: https://docs.microsoft.com/azure/mysql/howto-configure-privatelink-portal

How to Enable NSG Flow Logs: https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

How to Enable and use Traffic Analytics: https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Azure Security Center monitoring**: Yes

**Responsibility**: Customer

### 1.3: Protect critical web applications

**Guidance**: Not applicable; this recommendation is intended for web applications running on Azure App Service or compute resources.

**Azure Security Center monitoring**: Not applicable

**Responsibility**: N/A

### 1.4: Deny communications with known malicious IP addresses

**Guidance**: Use Advanced Threat Protection for Azure Database for MySQL. Advanced Threat Protection detects anomalous activities indicating unusual and potentially harmful attempts to access or exploit databases.

Enable DDoS Protection Standard on the Virtual Networks associated with your Azure Database for MySQL instances to guard against DDoS attacks. Use Azure Security Center Integrated Threat Intelligence to deny communications with known malicious or unused Internet IP addresses.

How to configure Advanced Threat Protection for Azure Database for MySQL: https://docs.microsoft.com/azure/mysql/howto-database-threat-protection-portal

How to configure DDoS protection: https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

**Azure Security Center monitoring**: Yes

**Responsibility**: Customer

### 1.5: Record network packets and flow logs

**Guidance**: When your Azure Database for MySQL instance is secured to a private endpoint, you can deploy virtual machines in the same virtual network. You can then configure a network security group (NSG) to reduce the risk of data exfiltration. Enable NSG flow logs and send logs into a Storage Account for traffic audit. You may also send NSG flow logs to a Log Analytics workspace and use Traffic Analytics to provide insights into traffic flow in your Azure cloud. Some advantages of Traffic Analytics are the ability to visualize network activity and identify hot spots, identify security threats, understand traffic flow patterns, and pinpoint network misconfigurations.

How to Enable NSG Flow Logs: https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

How to Enable and use Traffic Analytics: https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Azure Security Center monitoring**: Yes

**Responsibility**: Customer

### 1.6: Deploy network based intrusion detection/intrusion prevention systems (IDS/IPS)

**Guidance**: Use Advanced Threat Protection for Azure Database for MySQL. Advanced Threat Protection detects anomalous activities indicating unusual and potentially harmful attempts to access or exploit databases.

How to configure Advanced Threat Protection for Azure Database for MySQL: https://docs.microsoft.com/azure/mysql/howto-database-threat-protection-portal

**Azure Security Center monitoring**: Yes

**Responsibility**: Customer

### 1.7: Manage traffic to web applications

**Guidance**: Not applicable; this recommendation is intended for web applications running on Azure App Service or compute resources.

**Azure Security Center monitoring**: Not applicable

**Responsibility**: N/A

### 1.8: Minimize complexity and administrative overhead of network security rules

**Guidance**: For resources that need access to your Azure Database for MySQL instances, use Virtual Network Service Tags to define network access controls on Network Security Groups or Azure Firewall. You can use service tags in place of specific IP addresses when creating security rules. By specifying the service tag name (e.g., SQL.WestUs) in the appropriate source or destination field of a rule, you can allow or deny the traffic for the corresponding service. Microsoft manages the address prefixes encompassed by the service tag and automatically updates the service tag as addresses change.

Note: Azure Database for MySQL uses the "Microsoft.Sql" Service Tags.

For more information about using service tags: https://docs.microsoft.com/azure/virtual-network/service-tags-overview

Understand Service Tag usage for Azure Database for MySQL: https://docs.microsoft.com/azure/mysql/concepts-data-access-and-security-vnet#terminology-and-description

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Customer

### 1.9: Maintain standard security configurations for network devices

**Guidance**: Define and implement standard security configurations for network settings and network resources associated with your Azure Database for MySQL instances with Azure Policy. Use Azure Policy aliases in the "Microsoft.DBforMySQL" and "Microsoft.Network" namespaces to create custom policies to audit or enforce the network configuration of your Azure Database for MySQL instances. You may also make use of built-in policy definitions related to networking or your Azure Database for MySQL instances, such as:

- DDoS Protection Standard should be enabled

- Enforce SSL connection should be enabled for MySQL database servers

How to configure and manage Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure Policy samples for networking: https://docs.microsoft.com/azure/governance/policy/samples/

How to create an Azure Blueprint: https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Customer

### 1.10: Document traffic configuration rules

**Guidance**: Use tags for resources related to network security and traffic flow for your Azure Database for MySQL instances to provide metadata and logical organization.

Use any of the built-in Azure policy definitions related to tagging, such as, "Require tag and its value," to ensure that all resources are created with tags and to notify you of existing untagged resources.

You may use Azure PowerShell or Azure CLI to look-up or perform actions on resources based on their tags.

How to create and use tags: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Customer

### 1.11: Use automated tools to monitor network resource configurations and detect changes

**Guidance**: Use Azure Activity Log to monitor network resource configurations and detect changes for network resources related to your Azure Database for MySQL instances. Create alerts within Azure Monitor that will trigger when changes to critical network resources take place.

How to view and retrieve Azure Activity Log events: https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

How to create alerts in Azure Monitor: https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Customer

## Logging and Monitoring

*For more information, see [Security Control: Logging and Monitoring](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### 2.1: Use approved time synchronization sources

**Guidance**: Microsoft maintains the time source used for Azure resources, such as Azure Database for MySQL for timestamps in the logs.

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Microsoft

### 2.2: Configure central security log management

**Guidance**: Enable Diagnostic Settings and Server Logs and ingest logs to aggregate security data generated by your Azure Database for MySQL instances. Within Azure Monitor, use Log Analytics Workspace(s) to query and perform analytics, and use Azure Storage Accounts for long-term/archival storage. Alternatively, you may enable and on-board data to Azure Sentinel or a third-party SIEM.

Understand Server Logs for Azure Database for MySQL:
https://docs.microsoft.com/azure/mysql/concepts-monitoring#server-logs

How to onboard Azure Sentinel:
https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Azure Security Center monitoring**: Not available

**Responsibility**: Customer

### 2.3: Enable audit logging for Azure resources

**Guidance**: Enable Diagnostic Settings on your Azure Database for MySQL instances for access to audit, slow query, and MySQL metrics logs. Ensure that you specifically enable the MySQL Audit log. Activity logs, which are automatically available, include event source, date, user, timestamp, source addresses, destination addresses, and other useful elements. You may also enable Azure Activity Log Diagnostic Settings and send the logs to the same Log Analytics workspace or Storage Account.

Understand Server Logs for Azure Database for MySQL: https://docs.microsoft.com/azure/mysql/concepts-monitoring#server-logs

How to configure and access slow query logs for Azure Database for MySQL: https://docs.microsoft.com/azure/mysql/howto-configure-server-logs-in-portal

How to configure and access audit logs for Azure Database for MySQL: https://docs.microsoft.com/azure/mysql/howto-configure-audit-logs-portal

How to configure Diagnostic Settings for the Azure Activity Log: https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

**Azure Security Center monitoring**: Not available

**Responsibility**: Customer

### 2.4: Collect security logs from operating systems

**Guidance**: Not applicable; this recommendation is intended for compute resources.

**Azure Security Center monitoring**: Not applicable

**Responsibility**: N/A

### 2.5: Configure security log storage retention

**Guidance**: Within Azure Monitor, for the Log Analytics workspace being used to hold your Azure Database for MySQL logs, set the retention period according to your organization's compliance regulations. Use Azure Storage Accounts for long-term/archival storage.

How to set log retention parameters for Log Analytics Workspaces: https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

Storing resource logs in an Azure Storage Account: https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-storage

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Customer

### 2.6: Monitor and review Logs

**Guidance**: Analyze and monitor logs from your Azure Database for MySQL instances for anomalous behavior. Use Azure Monitor's Log Analytics to review logs and perform queries on log data. Alternatively, you may enable and on-board data to Azure Sentinel or a third party SIEM.

How to onboard Azure Sentinel: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

For more information about the Log Analytics: https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

How to perform custom queries in Azure Monitor: https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Customer

### 2.7: Enable alerts for anomalous activity

**Guidance**: Enable Advanced Threat Protection for Azure Database for MySQL. Advanced Threat Protection detects anomalous activities indicating unusual and potentially harmful attempts to access or exploit databases.

In addition, you may enable Server Logs and Diagnostic Settings for MySQL and send logs to a Log Analytics workspace. Onboard your Log Analytics workspace to Azure Sentinel as it provides a security orchestration automated response (SOAR) solution. This allows for playbooks (automated solutions) to be created and used to remediate security issues.

How to enable Advanced Threat Protection for Azure Database for MySQL (Preview): https://docs.microsoft.com/azure/mysql/howto-database-threat-protection-portal

Understand Server Logs for Azure Database for MySQL: https://docs.microsoft.com/azure/mysql/concepts-monitoring#server-logs

How to configure and access slow query logs for Azure Database for MySQL: https://docs.microsoft.com/azure/mysql/howto-configure-server-logs-in-portal

How to configure and access audit logs for Azure Database for MySQL: https://docs.microsoft.com/azure/mysql/howto-configure-audit-logs-portal

How to configure Diagnostic Settings for the Azure Activity Log: https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

How to onboard Azure Sentinel: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Azure Security Center monitoring**: Yes

**Responsibility**: Customer

### 2.8: Centralize anti-malware logging

**Guidance**: Not applicable; Azure Database for MySQL does not process or produce anti-malware related logs.

**Azure Security Center monitoring**: Not applicable

**Responsibility**: N/A

### 2.9: Enable DNS query logging

**Guidance**: Not of applicable; Azure Database for MySQL does not process or produce DNS related logs.

**Azure Security Center monitoring**: Not applicable

**Responsibility**: N/A

### 2.10: Enable command-line audit logging

**Guidance**: Not applicable; this recommendation is intended for compute resources.

**Azure Security Center monitoring**: Not applicable

**Responsibility**: N/A

## Identity and Access Control

*For more information, see [Security Control: Identity and Access Control](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### 3.1: Maintain an inventory of administrative accounts

**Guidance**: Maintain an inventory of the user accounts that have administrative access to the management plane (e.g. Azure portal) of your Azure Database for MySQLinstances. In addition, maintain an inventory of the administrative accounts that have access to the data plane (within the database itself) of your Azure Database for MySQL instances. (When creating the MySQL server, you provide credentials for an administrator user. This administrator can be used to create additional MySQL users.)

Azure Database for MySQL does not support built-in role-based access control, but you can create custom roles based on specific resource provider options.

Understand custom roles for Azure subscription: https://docs.microsoft.com/azure/role-based-access-control/custom-roles 

Understand Azure Database for MySQL resource provider operations: https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations#microsoftdbformysql

Understand access management for Azure Database for MySQL: https://docs.microsoft.com/azure/mysql/concepts-security#access-management

**Azure Security Center monitoring**: Yes

**Responsibility**: Customer

### 3.2: Change default passwords where applicable

**Guidance**: Azure AD does not have the concept of default passwords.

Upon creation of the Azure Database for MySQL resource itself, Azure forces the creation of an administrative user with a strong password. However, once the MySQL instance has been created, you may use the first server admin account you created to create additional users and grant administrative access to them. When creating these accounts, ensure you configure a different, strong password for each account.

How to create additional accounts for Azure Database for MySQL: https://docs.microsoft.com/azure/mysql/howto-create-users

How to update admin password: https://docs.microsoft.com/azure/mysql/howto-create-manage-server-portal#update-admin-password

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Customer

### 3.3: Use dedicated administrative accounts

**Guidance**: Create standard operating procedures around the use of dedicated administrative accounts that have access to your Azure Database for MySQL instances. Use Azure Security Center Identity and access management to monitor the number of administrative accounts.

Understand Azure Security Center Identity and Access: https://docs.microsoft.com/azure/security-center/security-center-identity-access

Understand how to create admin users in Azure Database for MySQL: https://docs.microsoft.com/azure/mysql/howto-create-users

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Customer

### 3.4: Use single sign-on (SSO) with Azure Active Directory

**Guidance**: Signing into Azure Database for MySQL is supported both using username/password configured directly in the database, as well as using an Azure Active Directory (AD) identity and utilizing an Azure AD token to connect. When using an Azure AD token, different methods are supported, such as an Azure AD user, an Azure AD group, or an Azure AD application connecting to the database.

Separately, control plane access for MySQL is available via REST API and supports SSO. To authenticate, set the Authorization header for your requests to a JSON Web Token that you obtain from Azure Active Directory.

Use Azure Active Directory for authenticating with Azure Database for MySQL: https://docs.microsoft.com/azure/mysql/howto-configure-sign-in-azure-ad-authentication

Understand Azure Database for MySQL REST API: https://docs.microsoft.com/rest/api/mysql/

Understand SSO with Azure AD: https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Customer

### 3.5: Use multi-factor authentication for all Azure Active Directory based access

**Guidance**: Enable Azure Active Directory Multi-Factor Authentication (MFA) and follow Azure Security Center Identity and Access Management recommendations. When utilizing Azure AD tokens for signing into your database, this allows you to require multi-factor authentication for database sign-ins.

How to enable MFA in Azure: https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Use Azure Active Directory for authenticating with Azure Database for MySQL: https://docs.microsoft.com/azure/mysql/howto-configure-sign-in-azure-ad-authentication

How to monitor identity and access within Azure Security Center: https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure Security Center monitoring**: Yes

**Responsibility**: Customer

### 3.6: Use dedicated machines (Privileged Access Workstations) for all administrative tasks

**Guidance**: Use privileged access workstations (PAWs) with Multi-Factor Authentication (MFA) configured to log into and configure Azure resources.

Learn about Privileged Access Workstations: https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

How to enable MFA in Azure: https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Customer

### 3.7: Log and alert on suspicious activity from administrative accounts

**Guidance**: Enable Advanced Threat Protection for Azure Database for MySQL to generate alerts for suspicious activity.

In addition, you may use Azure AD Privileged Identity Management (PIM) for generation of logs and alerts when suspicious or unsafe activity occurs in the environment.

Use Azure AD Risk Detections to view alerts and reports on risky user behavior.

How to configure Advanced Threat Protection for Azure Database for MySQL: https://docs.microsoft.com/azure/mysql/howto-database-threat-protection-portal

How to deploy Privileged Identity Management (PIM): https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

Understand Azure AD risk detections: https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

**Azure Security Center monitoring**: Yes

**Responsibility**: Customer

### 3.8: Manage Azure resources from only approved locations

**Guidance**: Use Conditional Access Named Locations to allow portal and Azure Resource Manager access from only specific logical groupings of IP address ranges or countries/regions.

How to configure Named Locations in Azure: https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Customer

### 3.9: Use Azure Active Directory

**Guidance**: Use Azure Active Directory (AD) as the central authentication and authorization system. Azure AD protects data by using strong encryption for data at rest and in transit. Azure AD also salts, hashes, and securely stores user credentials.

For signing into Azure Database for MySQL it is recommended to use Azure AD and utilize an Azure AD token to connect. When using an Azure AD token, different methods are supported, such as an Azure AD user, an Azure AD group, or an Azure AD application connecting to the database. 

Azure AD credentials may also be used for administration at the management plane level (e.g. the Azure portal) to control MySQL admin accounts.

Use Azure Active Directory for authenticating with Azure Database for MySQL: https://docs.microsoft.com/azure/mysql/howto-configure-sign-in-azure-ad-authentication

**Azure Security Center monitoring**: Yes

**Responsibility**: Customer

### 3.10: Regularly review and reconcile user access

**Guidance**: Review the Azure Active Directory logs to help discover stale accounts which can include those with Azure Database for MySQL administrative roles. In addition, use Azure Identity Access Reviews to efficiently manage group memberships, access to enterprise applications that may be used to access Azure Database for MySQL, and role assignments. User access should be reviewed on a regular basis such as every 90 days to make sure only the right Users have continued access.

Understand Azure AD Reporting https://docs.microsoft.com/azure/active-directory/reports-monitoring/

How to use Azure Identity Access Reviews: https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Azure Security Center monitoring**: Yes

**Responsibility**: Customer

### 3.11: Monitor attempts to access deactivated accounts

**Guidance**: Enable Diagnostic Settings for Azure Database for MySQL and Azure Active Directory, sending all logs to a Log Analytics workspace. Configure desired alerts (such as failed authentication attempts) within Log Analytics.

How to configure and access slow query logs for Azure Database for MySQL: https://docs.microsoft.com/Azure/mysql/howto-configure-server-logs-in-portal

How to configure and access audit logs for Azure Database for MySQL: https://docs.microsoft.com/Azure/mysql/howto-configure-audit-logs-portal

How to integrate Azure Activity Logs into Azure Monitor: https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Azure Security Center monitoring**: Not available

**Responsibility**: Customer

### 3.12: Alert on account login behavior deviation

**Guidance**: Enable Advanced Threat Protection for Azure Database for MySQL to generate alerts for suspicious activity.

Use Azure Active Directory's Identity Protection and risk detection features to configure automated responses to detected suspicious actions. You may enable automated responses through Azure Sentinel to implement your organization's security responses.

You can also ingest logs into Azure Sentinel for further investigation.

How to configure Advanced Threat Protection for Azure Database for MySQL: https://docs.microsoft.com/azure/mysql/howto-database-threat-protection-portal

Overview of Azure AD Identity Protection: https://docs.microsoft.com/azure/active-directory/identity-protection/overview-identity-protection

How to view Azure AD risky sign-ins: https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

How to onboard Azure Sentinel: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Azure Security Center monitoring**: Not available

**Responsibility**: Customer

### 3.13: Provide Microsoft with access to relevant customer data during support scenarios

**Guidance**: Not applicable; Customer Lockbox is not yet supported for Azure Database for MySQL.

List of Customer Lockbox supported services: https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability

**Azure Security Center monitoring**: Not applicable

**Responsibility**: N/A

## Data Protection

*For more information, see [Security Control: Data Protection](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### 4.1: Maintain an inventory of sensitive Information

**Guidance**: Use tags to assist in tracking Azure Database for MySQL instances or related resources that store or process sensitive information.

How to create and use tags: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Customer

### 4.2: Isolate systems storing or processing sensitive information

**Guidance**: Implement separate subscriptions and/or management groups for development, test, and production. Use a combination of Private Link, Service Endpoints, and/or firewall rules to isolate and limit network access to your Azure Database for MySQL instances.

How to create additional Azure subscriptions: https://docs.microsoft.com/azure/billing/billing-create-subscription

How to create Management Groups: https://docs.microsoft.com/azure/governance/management-groups/create

How to configure Private Link for Azure Database for MySQL: https://docs.microsoft.com/azure/mysql/concepts-data-access-security-private-link

How to create and manage VNet service endpoints and VNet rules in Azure Database for MySQL: https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview

How to configure Azure Database for MySQL firewall rules: https://docs.microsoft.com/azure/mysql/concepts-firewall-rules


**Azure Security Center monitoring**: Not available

**Responsibility**: Customer

### 4.3: Monitor and block unauthorized transfer of sensitive information

**Guidance**: When using Azure VMs to access Azure Database for MySQL instances, make use of Private Link, MySQL network configurations, network security groups, and service tags to mitigate the possibility of data exfiltration.

Microsoft manages the underlying infrastructure for Azure Database for MySQL and has implemented strict controls to prevent the loss or exposure of customer data.

How to mitigate data exfiltration for Azure Database for MySQL: https://docs.microsoft.com/azure/mysql/concepts-data-access-security-private-link#data-exfiltration-prevention

Understand customer data protection in Azure: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Customer

### 4.4: Encrypt all sensitive information in transit

**Guidance**: Azure Database for MySQL supports connecting your MySQL server to client applications using Secure Sockets Layer (SSL). Enforcing SSL connections between your database server and your client applications helps protect against "man in the middle" attacks by encrypting the data stream between the server and your application. In the Azure portal, ensure "Enforce SSL connection" is enabled for all of your Azure Database for MySQL instances by default.

Currently the TLS version supported for Azure Database for MySQL are TLS 1.0, TLS 1.1, TLS 1.2.

How to configure encryption in transit for Azure Database for MySQL: https://docs.microsoft.com/azure/mysql/concepts-ssl-connection-security

**Azure Security Center monitoring**: Not available

**Responsibility**: Shared

### 4.5: Use an active discovery tool to identify sensitive data

**Guidance**: Data identification, classification, and loss prevention features are not yet available for Azure Database for MySQL. Implement third-party solution if required for compliance purposes.

For the underlying platform which is managed by Microsoft, Microsoft treats all customer content as sensitive and goes to great lengths to guard against customer data loss and exposure. To ensure customer data within Azure remains secure, Microsoft has implemented and maintains a suite of robust data protection controls and capabilities.

Understand customer data protection in Azure:
https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure Security Center monitoring**: Not available

**Responsibility**: Shared

### 4.6: Use Azure RBAC to control access to resources

**Guidance**: Use Azure role-based access control (RBAC) to control access to the Azure Database for MySQL control plane (e.g. Azure portal). For data plane access (within the database itself), use SQL queries to create users and configure user permissions. RBAC does not affect user permissions within the database.

How to configure RBAC in Azure: https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

How to configure user access with SQL for Azure Database for MySQL: https://docs.microsoft.com/azure/mysql/howto-create-users

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Customer

### 4.7: Use host-based data loss prevention to enforce access control

**Guidance**: Not applicable; this recommendation is intended for compute resources.

Microsoft manages the underlying infrastructure for Azure Database for MySQL and has implemented strict controls to prevent the loss or exposure of customer data.

Understand customer data protection in Azure: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Microsoft

### 4.8: Encrypt sensitive information at rest

**Guidance**: The Azure Database for MySQL service uses the FIPS 140-2 validated cryptographic module for storage encryption of data at-rest. Data, including backups, are encrypted on disk, with the exception of temporary files created while running queries. The service uses the AES 256-bit cipher included in Azure storage encryption, and the keys are system managed. Storage encryption is always on and can't be disabled.

Data encryption with customer-managed keys for Azure Database for MySQL enables you to bring your own key (BYOK) for data protection at rest. At this time, you must request access to use this capability. To do so, contact:

AskAzureDBforMySQL@service.microsoft.com

Understand encryption at-rest for Azure Database for MySQL: https://docs.microsoft.com/azure/mysql/concepts-security

How to configure Customer Managed Keys for Azure Database for MySQL: https://docs.microsoft.com/azure/mysql/concepts-data-encryption-mysql

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Microsoft

### 4.9: Log and alert on changes to critical Azure resources

**Guidance**: Use Azure Monitor with the Azure Activity Log to create alerts for when changes take place to production instances of Azure Database for MySQL and other critical or related resources.

How to create alerts for Azure Activity Log events: https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Customer

## Vulnerability Management

*For more information, see [Security Control: Vulnerability Management](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### 5.1: Run automated vulnerability scanning tools

**Guidance**: Currently not available; Azure Security Center does not yet support vulnerability assessment for Azure Database for MySQL.

Feature coverage for Azure PaaS services in Azure Security Center: https://docs.microsoft.com/azure/security-center/features-paas

**Azure Security Center monitoring**: Yes

**Responsibility**: Customer

### 5.2: Deploy automated operating system patch management solution

**Guidance**: Not applicable; this guideline is intended for compute resources.

**Azure Security Center monitoring**: Not applicable

**Responsibility**: N/A

### 5.3: Deploy automated third-party software patch management solution

**Guidance**: Not applicable; this guideline is intended for compute resources.

**Azure Security Center monitoring**: Not applicable

**Responsibility**: N/A

### 5.4: Compare back-to-back vulnerability scans

**Guidance**: Not applicable; this guideline is intended for compute resources.

**Azure Security Center monitoring**: Not applicable

**Responsibility**: N/A

### 5.5: Use a risk-rating process to prioritize the remediation of discovered vulnerabilities

**Guidance**: Microsoft performs vulnerability management on the underlying systems that support Azure Database for MySQL.


**Azure Security Center monitoring**: Not applicable

**Responsibility**: Microsoft

## Inventory and Asset Management

*For more information, see [Security Control: Inventory and Asset Management](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### 6.1: Use Azure Asset Discovery

**Guidance**: Use Azure Resource Graph to query and discover all resources (including Azure Database for MySQL instances) within your subscription(s). Ensure you have appropriate (read) permissions in your tenant and are able to enumerate all Azure subscriptions as well as resources within your subscriptions.

How to create queries with Azure Graph: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

How to view your Azure Subscriptions: https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Understand Azure RBAC: https://docs.microsoft.com/azure/role-based-access-control/overview

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Customer

### 6.2: Maintain asset metadata

**Guidance**: Apply tags to Azure Database for MySQL instances and other related resources giving metadata to logically organize them into a taxonomy.

How to create and use Tags: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Customer

### 6.3: Delete unauthorized Azure resources

**Guidance**: Use tagging, management groups, and separate subscriptions, where appropriate, to organize and track Azure Database for MySQL instances and related resources. Reconcile inventory on a regular basis and ensure unauthorized resources are deleted from the subscription in a timely manner.

How to create additional Azure subscriptions: https://docs.microsoft.com/azure/billing/billing-create-subscription

How to create Management Groups: https://docs.microsoft.com/azure/governance/management-groups/create

How to create and use tags: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Customer

### 6.4: Maintain an inventory of approved Azure resources and software titles

**Guidance**: Not applicable; this recommendation is intended for compute resources and Azure as a whole.

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Customer

### 6.5: Monitor for unapproved Azure resources

**Guidance**: Use Azure policy to put restrictions on the type of resources that can be created in customer subscription(s) using the following built-in policy definitions:

- Not allowed resource types

- Allowed resource types

In addition, use the Azure Resource Graph to query/discover resources within the subscription(s).

How to configure and manage Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

How to create queries with Azure Graph: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Customer

### 6.6: Monitor for unapproved software applications within compute resources

**Guidance**: Not applicable; this recommendation is intended for compute resources.

**Azure Security Center monitoring**: Not applicable

**Responsibility**: N/A

### 6.7: Remove unapproved Azure resources and software applications

**Guidance**: Not applicable; this recommendation is intended for compute resources and Azure as a whole.

**Azure Security Center monitoring**: Not applicable

**Responsibility**: N/A

### 6.8: Use only approved applications

**Guidance**: Not applicable; this recommendation is intended for compute resources.

**Azure Security Center monitoring**: Not applicable

**Responsibility**: N/A

### 6.9: Use only approved Azure services

**Guidance**: Use Azure policy to put restrictions on the type of resources that can be created in customer subscription(s) using the following built-in policy definitions:

- Not allowed resource types

- Allowed resource types

How to configure and manage Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

How to deny a specific resource type with Azure Policy: https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Customer

### 6.10: Implement approved application list

**Guidance**: Not applicable; this recommendation is intended for compute resources.

**Azure Security Center monitoring**: Not applicable

**Responsibility**: N/A

### 6.11: Limit users' ability to interact with Azure Resources Manager via scripts

**Guidance**: Use the Azure Conditional Access to limit users' ability to interact with Azure Resource Manager by configuring "Block access" for the "Microsoft Azure Management" App. This can prevent the creation and changes to resources within a high security environment, such as instances of Azure Database for MySQL containing sensitive information.

How to configure Conditional Access to block access to Azure Resource Manager: https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Customer

### 6.12: Limit users' ability to execute scripts within compute resources

**Guidance**: Not applicable; this recommendation is intended for compute resources.

**Azure Security Center monitoring**: Not applicable

**Responsibility**: N/A

### 6.13: Physically or logically segregate high risk applications

**Guidance**: Not applicable; this recommendation is intended for web applications running on Azure App Service or compute resources.

**Azure Security Center monitoring**: Not applicable

**Responsibility**: N/A

## Secure Configuration

*For more information, see [Security Control: Secure Configuration](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### 7.1: Establish secure configurations for all Azure resources

**Guidance**: Define and implement standard security configurations for your Azure Database for MySQL instances with Azure Policy. Use Azure Policy aliases in the "Microsoft.DBforMySQL" namespace to create custom policies to audit or enforce the network configuration of your Azure Database for MySQL instances. You may also make use of built-in policy definitions related to your Azure Database for MySQL instances, such as:

Enforce SSL connection should be enabled for MySQL database servers

How to view available Azure Policy Aliases: https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

How to configure and manage Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Customer

### 7.2: Establish secure operating system configurations

**Guidance**: Not applicable; this recommendation is intended for compute resources.

**Azure Security Center monitoring**: Not applicable

**Responsibility**: N/A

### 7.3: Maintain secure Azure resource configurations

**Guidance**: Use Azure policy [deny] and [deploy if not exist] to enforce secure settings across your Azure resources.

How to configure and manage Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Understand Azure Policy Effects: https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Customer

### 7.4: Maintain secure operating system configurations

**Guidance**: Not applicable; this recommendation is intended for compute resources.

**Azure Security Center monitoring**: Not applicable

**Responsibility**: N/A

### 7.5: Securely store configuration of Azure resources

**Guidance**: If using custom Azure policy definitions for your Azure Database for MySQL instances and related resources, use Azure Repos to securely store and manage your code.

How to store code in Azure DevOps: https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Azure Repos Documentation: https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Customer

### 7.6: Securely store custom operating system images

**Guidance**: Not applicable; this recommendation is intended for compute resources.

**Azure Security Center monitoring**: Not applicable

**Responsibility**: N/A

### 7.7: Deploy system configuration management tools

**Guidance**: Use Azure Policy aliases in the "Microsoft.DBforMySQL" namespace to create custom policies to alert, audit, and enforce system configurations. Additionally, develop a process and pipeline for managing policy exceptions.

How to configure and manage Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Customer

### 7.8: Deploy system configuration management tools for operating systems

**Guidance**: Not applicable; this recommendation is intended for compute resources.

**Azure Security Center monitoring**: Not applicable

**Responsibility**: N/A

### 7.9: Implement automated configuration monitoring for Azure services

**Guidance**: Use Azure Policy aliases in the "Microsoft.DBforMySQL" namespace to create custom policies to alert, audit, and enforce system configurations. Use Azure policy [audit], [deny], and [deploy if not exist] to automatically enforce configurations for your Azure Database for MySQL instances and related resources.

How to configure and manage Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Customer

### 7.10: Implement automated configuration monitoring for operating systems

**Guidance**: Not applicable; this recommendation is intended for compute resources.

**Azure Security Center monitoring**: Not applicable

**Responsibility**: N/A

### 7.11: Manage Azure secrets securely

**Guidance**: For Azure Virtual Machines or web applications running on Azure App Service being used to access your Azure Database for MySQL instances, use Managed Service Identity in conjunction with Azure Key Vault to simplify and secure Azure Database for MySQL secret management. Ensure Key Vault Soft Delete is enabled.

How to integrate with Azure Managed Identities: https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

How to create a Key Vault: https://docs.microsoft.com/azure/key-vault/quick-create-portal

How to provide Key Vault authentication with a managed identity: https://docs.microsoft.com/azure/key-vault/managed-identity

**Azure Security Center monitoring**: Yes

**Responsibility**: Customer

### 7.12: Manage identities securely and automatically

**Guidance**: Azure Database for MySQL instance supports Azure Active Directory authentication (in preview) to access databases.  While creating the Azure Database for MySQL instance, you provide credentials for an administrator user. This administrator can be used to create additional database users.  

For Azure Virtual Machines or web applications running on Azure App Service being used to access your Azure Database for MySQL instances, use Managed Service Identity in conjunction with Azure Key Vault to store and retrieve credentials for Azure Database for MySQL instance. Ensure Key Vault Soft Delete is enabled.

Use Managed Identities to provide Azure services with an automatically managed identity in Azure Active Directory (AD). Managed Identities allows you to authenticate to any service that supports Azure AD authentication, including Key Vault, without any credentials in your code.

How to configure Managed Identities: https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

How to integrate with Azure Managed Identities: https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Customer

### 7.13: Eliminate unintended credential exposure

**Guidance**: Implement Credential Scanner to identify credentials within code. Credential Scanner will also encourage moving discovered credentials to more secure locations such as Azure Key Vault.

How to setup Credential Scanner: https://secdevtools.azurewebsites.net/helpcredscan.html

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Customer

## Malware Defense

*For more information, see [Security Control: Malware Defense](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### 8.1: Use centrally managed anti-malware software

**Guidance**: Not applicable; this recommendation is intended for compute resources.

Microsoft anti-malware is enabled on the underlying host that supports Azure services (for example, Azure Database for SQL), however it does not run on customer content.

**Azure Security Center monitoring**: Not applicable

**Responsibility**: N/A

### 8.2: Pre-scan files to be uploaded to non-compute Azure resources

**Guidance**: Microsoft anti-malware is enabled on the underlying host that supports Azure services (for example, Azure Database for MySQL), however it does not run on customer content.

Pre-scan any content being uploaded to non-compute Azure resources, such as App Service, Data Lake Storage, Blob Storage, Azure Database for MySQL, etc. Microsoft cannot access your data in these instances.

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Shared

### 8.3: Ensure anti-malware software and signatures are updated

**Guidance**: Not applicable; this recommendation is intended for compute resources.

Microsoft anti-malware is enabled on the underlying host that supports Azure services (for example, Azure Database for MySQL), however it does not run on customer content.

**Azure Security Center monitoring**: Not applicable

**Responsibility**: N/A

## Data Recovery

*For more information, see [Security Control: Data Recovery](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### 9.1: Ensure regular automated back ups

**Guidance**: Azure Database for MySQL takes backups of the data files and the transaction log. Depending on the supported maximum storage size, we either take full and differential backups (4 TB max storage servers) or snapshot backups (up to 16-TB max storage servers). These backups allow you to restore a server to any point-in-time within your configured backup retention period. The default backup retention period is seven days. You can optionally configure it up to 35 days. All backups are encrypted using AES 256-bit encryption.

Understand backups for Azure Database for MySQL: https://docs.microsoft.com/azure/mysql/concepts-backup

Understand Azure Database for MySQL initial configuration: https://docs.microsoft.com/azure/mysql/tutorial-design-database-using-portal

**Azure Security Center monitoring**: Yes

**Responsibility**: Shared

### 9.2: Perform complete system backups and backup any customer managed keys

**Guidance**: Azure Database for MySQL automatically creates server backups and stores them in either locally-redundant or geo-redundant storage, according to the user's choice. Backups can be used to restore your server to a point-in-time. Backup and restore are an essential part of any business continuity strategy because they protect your data from accidental corruption or deletion. 

If using Azure Key Vault to store credentials for your Azure Database for MySQL instances, ensure regular automated backups of your keys. 

Understand backups for Azure Database for MySQL: https://docs.microsoft.com/azure/mysql/howto-restore-server-portal 

How to backup Key Vault Keys:  https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey


**Azure Security Center monitoring**: Yes

**Responsibility**: Shared

### 9.3: Validate all backups including customer managed keys

**Guidance**: In Azure Database for MySQL, performing a restore creates a new server from the original server's backups. There are two types of restore available: Point-in-time restore and Geo-restore. Point-in-time restore is available with either backup redundancy option and creates a new server in the same region as your original server. Geo-restore is available only if you configured your server for geo-redundant storage and it allows you to restore your server to a different region.

The estimated time of recovery depends on several factors including the database sizes, the transaction log size, the network bandwidth, and the total number of databases recovering in the same region at the same time. The recovery time is usually less than 12 hours.

Periodically test restoration of your Azure Database for MySQL instances.

Understand backup and restore in Azure Database for MySQL: https://docs.microsoft.com/azure/mysql/concepts-backup

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Customer

### 9.4: Ensure protection of backups and customer managed keys

**Guidance**: Azure Database for MySQL takes full, differential, and transaction log backups. These backups allow you to restore a server to any point-in-time within your configured backup retention period. The default backup retention period is seven days. You can optionally configure it up to 35 days. All backups are encrypted using AES 256-bit encryption. Ensure Key Vault Soft Delete is enabled.

Understand backup and restore in Azure Database for MySQL: https://docs.microsoft.com/azure/mysql/concepts-backup

**Azure Security Center monitoring**: Yes

**Responsibility**: Customer

## Incident Response

*For more information, see [Security Control: Incident Response](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### 10.1: Create an incident response guide

**Guidance**: Build out an incident response guide for your organization. Ensure that there are written incident response plans that define all roles of personnel as well as phases of incident handling/management from detection to post-incident review.

How to configure Workflow Automations within Azure Security Center: https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

Guidance on building your own security incident response process: https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Microsoft Security Response Center's Anatomy of an Incident: https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Customer may also leverage NIST's Computer Security Incident Handling Guide to aid in the creation of their own incident response plan: https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Customer

### 10.2: Create an incident scoring and prioritization procedure

**Guidance**: Security Center assigns a severity to each alert to help you prioritize which alerts should be investigated first. The severity is based on how confident Security Center is in the finding or the analytic used to issue the alert as well as the confidence level that there was malicious intent behind the activity that led to the alert.

Additionally, clearly mark subscriptions (for ex. production, non-prod) and create a naming system to clearly identify and categorize Azure resources.

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Customer

### 10.3: Test security response procedures

**Guidance**: Conduct exercises to test your systems’ incident response capabilities on a regular cadence. Identify weak points and gaps and revise plan as needed.

Refer to NIST's publication: Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities: https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Customer

### 10.4: Provide security incident contact details and configure alert notifications for security incidents

**Guidance**: Security incident contact information will be used by Microsoft to contact you if the Microsoft Security Response Center (MSRC) discovers that the customer's data has been accessed by an unlawful or unauthorized party.  Review incidents after the fact to ensure that issues are resolved.

How to set the Azure Security Center Security Contact: https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Azure Security Center monitoring**: Yes

**Responsibility**: Customer

### 10.5: Incorporate security alerts into your incident response system

**Guidance**: Export your Azure Security Center alerts and recommendations using the Continuous Export feature. Continuous Export allows you to export alerts and recommendations either manually or in an ongoing, continuous fashion. You may use the Azure Security Center data connector to stream the alerts Sentinel.

How to configure continuous export: https://docs.microsoft.com/azure/security-center/continuous-export

How to stream alerts into Azure Sentinel: https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Customer

### 10.6: Automate the response to security alerts

**Guidance**: Use the Workflow Automation feature in Azure Security Center to automatically trigger responses via "Logic Apps" on security alerts and recommendations.

How to configure Workflow Automation and Logic Apps: https://docs.microsoft.com/azure/security-center/workflow-automation

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Customer

## Penetration Tests and Red Team Exercises

*For more information, see [Security Control: Penetration Tests and Red Team Exercises](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### 11.1: Conduct regular penetration testing of your Azure resources and ensure remediation of all critical security findings within 60 days

**Guidance**: Follow the Microsoft Rules of Engagement to ensure your Penetration Tests are not in violation of Microsoft policies: https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

You can find more information on Microsoft’s strategy and execution of Red Teaming and live site penetration testing against Microsoft-managed cloud infrastructure, services, and applications, here:  https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Shared

## Next steps

- See the [Azure Security Benchmark](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Learn more about [Azure Security Baselines](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
