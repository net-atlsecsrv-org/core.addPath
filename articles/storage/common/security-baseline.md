---
title: Azure Security Baseline for Azure Storage
description: Azure Security Baseline for Azure Storage
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/23/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark

---

# Azure Security Baseline for Azure Storage

The Azure Security Baseline for Azure Storage contains recommendations that will help you improve the security posture of your deployment.

The baseline for this service is drawn from the [Azure Security Benchmark version 1.0](../../security/benchmarks/overview.md), which provides recommendations on how you can secure your cloud solutions on Azure with our best practices guidance.

For more information, see [Azure Security Baselines overview](../../security/benchmarks/security-baselines-overview.md).

## Network Security

*For more information, see [Security Control: Network Security](../../security/benchmarks/security-control-network-security.md).*

### 1.1: Protect resources using Network Security Groups or Azure Firewall on your Virtual Network

**Guidance**: Configure your Storage Account's Firewall by restricting access to clients from specific public IP address ranges, select virtual networks (VNets) on Azure, or to specific Azure resources. You can also configure Private Endpoints so traffic to the storage service from your enterprise travels exclusively over private networks.

Note: Classic storage accounts do not support firewalls and virtual networks.

- [How to configure the Azure Storage Firewall](./storage-network-security.md#change-the-default-network-access-rule)

- [How to configure Private Endpoints for Azure Storage](./storage-private-endpoints.md)

**Azure Security Center monitoring**: Yes

**Responsibility**: Customer

### 1.2: Monitor and log Vnet, Subnet, and NIC configuration and traffic

**Guidance**: Azure Storage provides a layered security model. You can limit access to your storage account to requests originating from specified IP addresses, IP ranges or from a list of subnets in an Azure Virtual Network (VNet). You can use Azure Security Center and follow network protection recommendations to help secure your network resources in Azure. Also, enable NSG flow logs for virtual networks / subnet configured for the Storage accounts via Storage account firewall and send logs into a Storage Account for traffic audit. 

Note that if you have Private Endpoints attached to your storage account, you can't configure Network Security Group (NSG) rules for subnets. 

- [Configure Azure Storage firewalls and virtual networks](./storage-network-security.md)

- [How to Enable NSG Flow Logs](../../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Understanding Network Security provided by Azure Security Center](../../security-center/security-center-network-recommendations.md)

- [Understanding Private Endpoints for Azure Storage](./storage-private-endpoints.md#known-issues)

**Azure Security Center monitoring**: Yes

**Responsibility**: Customer

### 1.3: Protect Critical Web Applications

**Guidance**: Not applicable; recommendation is intended for web applications running on Azure App Service or compute resources.

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Customer

### 1.4: Deny Communications with Known Malicious IP Addresses

**Guidance**: Enable Advanced Threat Protection for your Azure Storage account. Advanced threat protection for Azure Storage provides an additional layer of security intelligence that detects unusual and potentially harmful attempts to access or exploit storage accounts. Azure Security Center integrated alerts are based on activities for which network communication was associated with an IP address that was successfully resolved, whether or not the IP address is a known risky IP address (for example, a known cryptominer) or an IP address that is not recognized previously as risky. Security alerts are triggered when anomalies in activity occur. 

- [How to enable Advanced Threat Protection](./azure-defender-storage-configure.md?tabs=azure-portal)

- [Understand Azure Security Center Integrated Threat Intelligence](../../security-center/azure-defender.md)

**Azure Security Center monitoring**: Yes

**Responsibility**: Customer

### 1.5: Record Network Packets and Flow Logs

**Guidance**: Network Watcher packet capture allows you to create capture sessions to track traffic between Storage account and a virtual machine. Filters are provided for the capture session to ensure you capture only the traffic you want. Packet capture helps to diagnose network anomalies, both reactively, and proactively. Other uses include gathering network statistics, gaining information on network intrusions, to debug client-server communication, and much more. Being able to remotely trigger packet captures, eases the burden of running a packet capture manually on a desired virtual machine, which saves valuable time. 

- [Manage packet captures with Azure Network Watcher using the portal](../../network-watcher/network-watcher-packet-capture-manage-portal.md)

**Azure Security Center monitoring**: Yes

**Responsibility**: Customer

### 1.6: Deploy Network Based Intrusion Detection/Intrusion Prevention Systems

**Guidance**: Advanced threat protection for Azure Storage provides an additional layer of security intelligence that detects unusual and potentially harmful attempts to access or exploit storage accounts. Security alerts are triggered when anomalies in activity occur. These security alerts are integrated with Azure Security Center, and are also sent via email to subscription administrators, with details of suspicious activity and recommendations on how to investigate and remediate threats. 

- [Configure advanced threat protection for Azure Storage](./azure-defender-storage-configure.md)

**Azure Security Center monitoring**: Yes

**Responsibility**: Customer

### 1.7: Manage traffic to your web applications

**Guidance**: Not applicable; recommendation is intended for web applications running on Azure App Service or compute resources.

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Customer

### 1.8: Minimize complexity and administrative overhead of network security rules

**Guidance**: For resource in Virtual Networks that need access to your Storage account, use Virtual Network Service Tags for the configured Virtual network to define network access controls on Network Security Groups or Azure Firewall. You can use service tags in place of specific IP addresses when creating security rules. By specifying the service tag name (e.g., Storage) in the appropriate source or destination field of a rule, you can allow or deny the traffic for the corresponding service. Microsoft manages the address prefixes encompassed by the service tag and automatically updates the service tag as addresses change. 

When network access needs to be scoped to specific Storage Accounts, use Virtual Network service endpoint policies.

- [For more information about using Service Tags](../../virtual-network/service-tags-overview.md)

- [For more information about Virtual network service endpoint policies for Azure Storage](../../virtual-network/virtual-network-service-endpoint-policies-overview.md)

**Azure Security Center monitoring**: Currently not available

**Responsibility**: Service

### 1.9: Maintain Standard Security Configurations for Network Devices

**Guidance**: Define and implement standard security configurations for network resources associated with your Azure Storage Account with Azure Policy. Use Azure Policy aliases in the "Microsoft.Storage" and "Microsoft.Network" namespaces to create custom policies to audit or enforce the network configuration of your Storage account resources. 

You may also make use of built-in policy definitions related to Storage account, such as: 
Storage Accounts should use a virtual network service endpoint 

- [How to configure and manage Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy samples for Storage](../../governance/policy/samples/built-in-policies.md#storage)

- [Azure Policy samples for Network](../../governance/policy/samples/built-in-policies.md#network)

- [How to create an Azure Blueprint](../../governance/blueprints/create-blueprint-portal.md)

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Customer

### 1.10: Document Traffic Configuration Rules

**Guidance**: Use tags for network security groups (NSG) and other resources related to network security and traffic flow. For individual NSG rules, use the "Description" field to specify business need and/or duration (etc.) for any rules that allow traffic to/from a network. Use any of the built-in Azure Policy definitions related to tagging, such as "Require tag and its value" to ensure that all resources are created with Tags and to notify you of existing untagged resources. You may use Azure PowerShell or Azure CLI to look-up or perform actions on resources based on their tags. 

- [How to create and use Tags](../../azure-resource-manager/management/tag-resources.md)

- [How to create a Virtual Network](../../virtual-network/quick-create-portal.md)

- [How to create an NSG with a Security Config](../../virtual-network/tutorial-filter-network-traffic.md)

**Azure Security Center monitoring**: Currently not available

**Responsibility**: Customer

### 1.11: Use Automated Tools to Monitor Network Resource Configurations and Detect Changes

**Guidance**: Use Azure Policy to log configuration changes for network resources. Create alerts within Azure Monitor that will trigger when changes to critical network resources take place. 

- [How to configure and manage Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

- [How to create alerts in Azure Monitor](../../azure-monitor/platform/alerts-activity-log.md)

**Azure Security Center monitoring**: Yes

**Responsibility**: Customer

## Logging and Monitoring

*For more information, see [Security Control: Logging and Monitoring](../../security/benchmarks/security-control-logging-monitoring.md).*

### 2.1: Use Approved Time Synchronization resource

**Guidance**: Not applicable; Microsoft maintains time sources for Azure Storage accounts.

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Microsoft

### 2.2: Configure Central Security Log Management

**Guidance**: Ingest logs via Azure Monitor to aggregate security data generated by endpoints devices, network resources, and other security systems. Within Azure Monitor, use Log Analytics Workspace(s) to query and perform analytics, and use Azure Storage Accounts for long-term/archival storage, optionally with security features such as immutable storage and enforced retention holds.

- [How to collect platform logs and metrics with Azure Monitor](../../azure-monitor/platform/diagnostic-settings.md)

**Azure Security Center monitoring**: Currently not available

**Responsibility**: Customer

### 2.3: Enable audit logging for Azure Resources

**Guidance**: Azure Storage Analytics provides logs for blobs, queues, and tables. You can use the Azure portal to configure which logs are recorded for your account. 

- [How to configure monitoring for your Azure Storage account](./storage-monitor-storage-account.md#configure-monitoring-for-a-storage-account)

**Azure Security Center monitoring**: Currently not available

**Responsibility**: Customer

### 2.4: Collect Security Logs from Operating Systems

**Guidance**: Not applicable; this recommendation is intended for compute resources.

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Not applicable

### 2.5: Configure Security Log Storage Retention

**Guidance**: When storing Security event logs in the Azure Storage account or Log Analytics workspace, you may set the retention policy according to your organization's requirements. 

- [How to configure retention policy for Azure Storage account logs](./storage-monitor-storage-account.md#configure-logging)

- [Change the data retention period in Log Analytics](../../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

**Azure Security Center monitoring**: Currently not available

**Responsibility**: Customer

### 2.6: Monitor and Review Logs

**Guidance**: To review the Azure Storage logs, there are the usual options such as queries through the Log Analytics offering as well as a unique option of viewing the log files directly. In Azure Storage, the logs are stored in blobs that must be accessed directly at `http://accountname.blob.core.windows.net/$logs` (The logging folder is hidden by default, so you will need to navigate directly. It will not display in List commands) 

Also, Enable Advanced Threat Protection for your Azure Storage account. Advanced threat protection for Azure Storage provides an additional layer of security intelligence that detects unusual and potentially harmful attempts to access or exploit storage accounts. Security alerts are triggered when anomalies in activity occur. These security alerts are integrated with Azure Security Center, and are also sent via email to subscription administrators, with details of suspicious activity and recommendations on how to investigate and remediate threats. 

- [Log and review data](./storage-analytics-logging.md#how-logs-are-stored)

- [How to enable Advanced Threat Protection](./azure-defender-storage-configure.md?tabs=azure-portal)

**Azure Security Center monitoring**: Currently not available

**Responsibility**: Customer

### 2.7: Enable Alerts for Anomalous Activity

**Guidance**: In Azure Security Center, enable Advanced Threat Protection for Storage account. Enable Diagnostic Settings for the Storage account and send logs to a Log Analytics Workspace. Onboard your Log Analytics Workspace to Azure Sentinel as it provides a security orchestration automated response (SOAR) solution. This allows for playbooks (automated solutions) to be created and used to remediate security issues. 

- [How to onboard Azure Sentinel](../../sentinel/quickstart-onboard.md)

- [How to manage alerts in Azure Security Center](../../security-center/security-center-managing-and-responding-alerts.md)

- [How to alert on log analytics log data](../../azure-monitor/learn/tutorial-response.md)

- [Azure Storage analytics logging](./storage-analytics-logging.md)

**Azure Security Center monitoring**: Yes

**Responsibility**: Customer

### 2.8: Centralize Anti-malware Logging

**Guidance**: Use Azure Security center and enable threat protection for Azure Storage for detecting malware uploads to Azure Storage using hash reputation analysis and suspicious access from an active Tor exit node (an anonymizing proxy). 

- [Configure advanced threat protection for Azure Storage](./azure-defender-storage-configure.md?tabs=azure-portal)

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Customer

### 2.9: Enable DNS Query Logging

**Guidance**: Azure DNS Analytics (Preview) solution in Azure Monitor gathers insights into DNS infrastructure on security, performance, and operations. Currently this does not support Azure Storage accounts however you can use third party dns logging solution. 

- [Gather insights about your DNS infrastructure with the DNS Analytics Preview solution](../../azure-monitor/insights/dns-analytics.md)

**Azure Security Center monitoring**: Currently not available

**Responsibility**: Customer

### 2.10: Enable Command-line Audit Logging

**Guidance**: Not applicable; benchmark is intended for compute resources.

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Not applicable

## Identity and Access Control

*For more information, see [Security Control: Identity and Access Control](../../security/benchmarks/security-control-identity-access-control.md).*

### 3.1: Maintain Inventory of Administrative Accounts

**Guidance**: Azure AD has built-in roles that must be explicitly assigned and are queryable. Use the Azure AD PowerShell module to perform ad hoc queries to discover accounts that are members of administrative groups. 

- [How to get a directory role in Azure AD with PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

- [How to get members of a directory role in Azure AD with PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

**Azure Security Center monitoring**: Currently not available

**Responsibility**: Customer

### 3.2: Change Default Passwords where Applicable

**Guidance**: Azure Storage accounts nor Azure Active Directory have the concept of default or blank passwords. Azure Storage implements an access control model that supports Azure role-based access control (Azure RBAC) as well as Shared Key and Shared Access Signatures (SAS). A characteristic of Shared Key and SAS authentication is that no identity is associated with the caller and therefore security principal permission-based authorization cannot be performed. 

- [Authorizing access to data in Azure Storage](./storage-auth.md)

- [Understanding security principals and access control for Azure Storage account](./storage-introduction.md)

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Customer

### 3.3: Use Dedicated Administrative Accounts

**Guidance**: Create standard operating procedures around the use of dedicated administrative accounts that have access to your Storage account. Use Azure Security Center Identity and access management to monitor the number of administrative accounts. 

You can also enable a Just-In-Time / Just-Enough-Access by using Azure AD Privileged Identity Management Privileged Roles for Microsoft Services, and Azure ARM. 

- [Understand Azure Security Center Identity and Access](../../security-center/security-center-identity-access.md)

- [Privileged Identity Management Overview](../../active-directory/privileged-identity-management/index.yml)

**Azure Security Center monitoring**: Currently not available

**Responsibility**: Customer

### 3.4: Use Azure Active Directory Single Sign-On (SSO)

**Guidance**: Wherever possible, use Azure Active Directory SSO instead than configuring individual stand-alone credentials per-service. Use Azure Security Center Identity and Access Management recommendations. 

- [Understanding SSO with Azure AD](../../active-directory/manage-apps/what-is-single-sign-on.md)

- [Authorizing access to data in Azure Storage](./storage-auth.md)

- [Authorize access to blobs and queues using Azure Active Directory](./storage-auth-aad.md)

**Azure Security Center monitoring**: Currently not available

**Responsibility**: Customer

### 3.5: Use Multifactor Authentication for all Azure Active Directory based access.

**Guidance**: Enable Azure Active Directory Multifactor Authentication and follow Azure Security Center Identity and access management recommendations to help protect your Storage account resources. 

- [How to enable MFA in Azure](../../active-directory/authentication/howto-mfa-getstarted.md)

- [How to monitor identity and access within Azure Security Center](../../security-center/security-center-identity-access.md)

**Azure Security Center monitoring**: Yes

**Responsibility**: Customer

### 3.6: Use Dedicated Machines (Privileged Access Workstations) for all Administrative Tasks

**Guidance**: Use PAWs (privileged access workstations) with MFA configured to log into and configure Storage account resources. 

- [Learn about Privileged Access Workstations](/windows-server/identity/securing-privileged-access/privileged-access-workstations)

- [How to enable MFA in Azure](../../active-directory/authentication/howto-mfa-getstarted.md)

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Customer

### 3.7: Log and Alert on Suspicious Activity from Administrative Accounts

**Guidance**: Send Azure Security Center Risk Detection alerts into Azure Monitor and configure custom alerting/notifications using Action Groups. Enable Advanced Threat Protection for Azure Storage account to generate alerts for suspicious activity. Additionally, use Azure AD Risk Detections to view alerts and reports on risky user behavior. 

- [How to setup Advanced Threat Protection for Azure Storage account](./azure-defender-storage-configure.md)

- [Understand Azure AD risk detections](../../active-directory/identity-protection/overview-identity-protection.md)

- [How to configure action groups for custom alerting and notification](../../azure-monitor/platform/action-groups.md)

**Azure Security Center monitoring**: Yes

**Responsibility**: Customer

### 3.8: Manage Azure Resource from only Approved Locations

**Guidance**: Use Conditional Access named locations to allow access from only specific logical groupings of IP address ranges or countries/regions. 

- [How to configure named locations in Azure](../../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure Security Center monitoring**: Currently not available

**Responsibility**: Customer

### 3.9: Use Azure Active Directory

**Guidance**: Use Azure Active Directory (Azure AD) as the central authentication and authorization system. Azure provides Azure role-based access control (Azure RBAC) for fine-grained control over a client's access to resources in a storage account.  Use Azure AD credentials when possible as a security best practice, rather than using the account key, which can be more easily compromised. When your application design requires shared access signatures for access to Blob storage, use Azure AD credentials to create a user delegation shared access signatures (SAS) when possible for superior security.

- [How to create and configure an Azure AD instance](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Use the Azure Storage resource provider to access management resources](./authorization-resource-provider.md)

- [How to configure access to Azure Blob and Queue data with Azure RBAC in Azure portal](./storage-auth-aad-rbac-portal.md)

- [Authorizing access to data in Azure Storage](./storage-auth.md)

- [Grant limited access to Azure Storage resources using shared access signatures (SAS)](./storage-sas-overview.md)

**Azure Security Center monitoring**: Currently not available

**Responsibility**: Customer

### 3.10: Regularly Review and Reconcile User Access

**Guidance**: Review the Azure Active Directory logs to help discover stale accounts which can include those with Storage account administrative roles. In addition, use Azure Identity Access Reviews to efficiently manage group memberships, access to enterprise applications that may be used to access Storage account resources, and role assignments. User access should be reviewed on a regular basis to make sure only the right Users have continued access.  

You can also use shared access signature (SAS) to provide secure delegated access to resources in your storage account without compromising the security of your data. You can control what resources the client may access, what permissions they have on those resources, and how long the SAS is valid, among other parameters.

Also, review anonymous read access to containers and blobs. By default, a container and any blobs within it may be accessed only by a user that has been given appropriate permissions. You can use Azure Monitor to alert on anonymous access for Storage accounts using anonymous authentication condition.

One effective way to reduce the risk of unsuspected user account access is to limit the duration of access that you grant to users. Time-limited SAS URIs are one effective way to automatically expire user access to a Storage account. Additionally, rotating Storage Account Keys on a frequent basis is a way to ensure that unexpected access via Storage Account keys is of limited duration.

- [Understand Azure AD Reporting](../../active-directory/reports-monitoring/index.yml)

- [How to view and change access at the Azure Storage account level](./storage-auth-aad-rbac-portal.md)

- [Grant limited access to Azure Storage resources using shared access signatures (SAS)](./storage-sas-overview.md)

- [Manage anonymous read access to containers and blobs](../blobs/anonymous-read-access-configure.md)

- [Monitor a storage account in the Azure portal](./storage-monitor-storage-account.md)

- [Manage storage account access keys](./storage-account-keys-manage.md)

**Azure Security Center monitoring**: Currently not available

**Responsibility**: Customer

### 3.11: Monitor Attempts to Access Deactivated Accounts

**Guidance**: Use Storage Analytics to logs detailed information about successful and failed requests to a storage service. All logs are stored in block blobs in a container named $logs, which is automatically created when Storage Analytics is enabled for a storage account.

Create Diagnostic Settings for Azure Active Directory user accounts, sending the audit logs and sign-in logs to a Log Analytics Workspace. You can configure desired Alerts within Log Analytics Workspace. To monitor authentication failures against Azure Storage Accounts, you can create alerts to notify you when certain thresholds have been reached for storage resource metrics. Additionally, use Azure Monitor to alert on anonymous access for Storage accounts using anonymous authentication condition.

- [Azure Storage analytics logging](./storage-analytics-logging.md)

- [How to integrate Azure Activity Logs into Azure Monitor](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [How to configure metrics alerts for Azure Storage Accounts](./storage-monitor-storage-account.md)

**Azure Security Center monitoring**: Currently not available

**Responsibility**: Customer

### 3.12: Alert on Account Login Behavior Deviation

**Guidance**: Use Azure Active Directory's Risk and Identity Protection features to configure automated responses to detected suspicious actions related to your Storage account resources. You should enable automated responses through Azure Sentinel to implement your organization's security responses. 

- [How to view Azure AD risky sign-ins](../../active-directory/identity-protection/overview-identity-protection.md)

- [How to configure and enable Identity Protection risk policies](../../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [How to onboard Azure Sentinel](../../sentinel/quickstart-onboard.md)

**Azure Security Center monitoring**: Currently not available

**Responsibility**: Customer

### 3.13: Provide Microsoft with access to relevant customer data during support scenarios

**Guidance**: In support scenarios where Microsoft needs to access customer data, Customer Lockbox (Preview for Storage account) provides an interface for customers to review and approve or reject customer data access requests. Microsoft will not require, nor request access to your organization's secrets stored within Storage account.

- [Understand Customer Lockbox](../../security/fundamentals/customer-lockbox-overview.md)

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Customer

## Data Protection

*For more information, see [Security Control: Data Protection](../../security/benchmarks/security-control-data-protection.md).*

### 4.1: Maintain an inventory of sensitive Information

**Guidance**: Use tags to assist in tracking Storage account resources that store or process sensitive information. 

- [How to create and use Tags](../../azure-resource-manager/management/tag-resources.md)

**Azure Security Center monitoring**: Currently not available

**Responsibility**: Customer

### 4.2: Isolate systems storing or processing sensitive information

**Guidance**: Implement isolation using separate subscriptions, management groups, and storage accounts for individual security domains such as environment, data sensitivity.  You can restrict your Storage Account to control the level of access to your storage accounts that your applications and enterprise environments demand, based on the type and subset of networks used. When network rules are configured, only applications requesting data over the specified set of networks can access a storage account. You can control access to Azure Storage via Azure RBAC. You can also configure Private Endpoints to improve security as traffic between your virtual network and the service traverses over the Microsoft backbone network, eliminating exposure from the public Internet. 

- [How to create additional Azure subscriptions](../../cost-management-billing/manage/create-subscription.md)

- [How to create Management Groups](../../governance/management-groups/create-management-group-portal.md)

- [How to create and use Tags](../../azure-resource-manager/management/tag-resources.md)

- [Configure Azure Storage firewalls and virtual networks](./storage-network-security.md)

- [Virtual Network service endpoints](../../virtual-network/virtual-network-service-endpoints-overview.md)

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Customer

### 4.3: Monitor and block unauthorized transfer of sensitive information.

**Guidance**: For Storage account resources storing or processing sensitive information, mark the resources as sensitive using Tags. To reduce the risk of data loss via exfiltration, restrict outbound network traffic for Azure Storage accounts using Azure Firewall. 

Additionally, use Virtual network service endpoint policies to filter egress virtual network traffic to Azure Storage accounts over service endpoint, and allow data exfiltration to only specific Azure Storage accounts.

- [Configure Azure Storage firewalls and virtual networks](../../virtual-network/virtual-network-service-endpoint-policies-overview.md)

- [Virtual network service endpoint policies for Azure Storage](../../private-link/tutorial-private-endpoint-storage-portal.md)

- [Understand customer data protection in Azure](../../security/fundamentals/protection-customer-data.md)

**Azure Security Center monitoring**: Currently not available

**Responsibility**: Customer

### 4.4: Encrypt all sensitive information in transit

**Guidance**: You can enforce the use of HTTPS by enabling Secure transfer required for the storage account. Connections using HTTP will be refused once this is enabled. Additionally, use Azure Security Center and Azure Policy to enforce Secure transfer for your storage account.

- [How to require secure transfer in Azure Storage](./storage-require-secure-transfer.md)

- [Azure security policies monitored by Security Center](../../security-center/policy-reference.md)

**Azure Security Center monitoring**: Yes

**Responsibility**: Shared

### 4.5: Use an active discovery tool to identify sensitive data

**Guidance**: Data identification features are not yet available for Azure Storage account and related resources. Implement third-party solution if required for compliance purposes. 

- [Understand customer data protection in Azure](../../security/fundamentals/protection-customer-data.md)

**Azure Security Center monitoring**: Currently not available

**Responsibility**: Customer

### 4.6: Use Azure RBAC to control access to resources

**Guidance**: Azure Active Directory (Azure AD) authorizes access rights to secured resources through Azure role-based access control (Azure RBAC). Azure Storage defines a set of Azure built-in roles that encompass common sets of permissions used to access blob or queue data. 

- [How to assign Azure roles for Azure Storage account](./storage-auth-aad-rbac-portal.md#assign-azure-roles-using-the-azure-portal)

- [Use the Azure Storage resource provider to access management resources](./authorization-resource-provider.md)

- [How to configure access to Azure Blob and Queue data with Azure RBAC in Azure portal](./storage-auth-aad-rbac-portal.md)

- [How to create and configure an AAD instance](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Authorizing access to data in Azure Storage](./storage-auth.md)

**Azure Security Center monitoring**: Currently not available

**Responsibility**: Customer

### 4.7: Use host-based Data Loss Prevention to enforce access control

**Guidance**: Not applicable; this recommendation is intended for compute resources.

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Not applicable

### 4.8: Encrypt Sensitive Information at Rest

**Guidance**: Azure Storage encryption is enabled for all storage accounts and cannot be disabled. Azure Storage automatically encrypts your data when it is persisted to the cloud. When you read data from Azure Storage, it is decrypted by Azure Storage before being returned. Azure Storage encryption enables you to secure your data at rest without having to modify code or add code to any applications. 

- [Understanding Azure Storage encryption at rest](./storage-service-encryption.md)

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Customer

### 4.9: Log and alert on changes to critical Azure resources

**Guidance**: Use Azure Monitor with the Azure Activity Log to create alerts for when changes take place to Storage account resources. You can also enable Azure Storage logging to track how each request made against Azure Storage was authorized. The logs indicate whether a request was made anonymously, by using an OAuth 2.0 token, by using Shared Key, or by using a shared access signature (SAS). Additionally, use Azure Monitor to alert on anonymous access for Storage accounts using anonymous authentication condition.

- [How to create alerts for Azure Activity Log events](../../azure-monitor/platform/alerts-activity-log.md)

- [Azure Storage analytics logging](./storage-analytics-logging.md)

- [How to configure metrics alerts for Azure Storage Accounts](./storage-monitor-storage-account.md)

**Azure Security Center monitoring**: Currently not available

**Responsibility**: Customer

## Vulnerability Management

*For more information, see [Security Control: Vulnerability Management](../../security/benchmarks/security-control-vulnerability-management.md).*

### 5.1: Run Automated Vulnerability Scanning Tools

**Guidance**: Follow recommendations from Azure Security Center to continuously audit and monitor the configuration of your storage accounts. 

- [Security recommendations - a reference guide](../../security-center/recommendations-reference.md)

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Customer

### 5.2: Deploy Automated Operating System Patch Management Solution

**Guidance**: Not applicable; this recommendation is intended for compute resources.

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Not applicable

### 5.3: Deploy Automated Third Party Software Patch Management Solution

**Guidance**: Not applicable; this recommendation is intended for compute resources.

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Not applicable

### 5.4: Compare Back-to-back Vulnerability Scans

**Guidance**: Not applicable; Microsoft performs vulnerability management on the underlying systems that support Storage accounts.

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Customer

### 5.5: Use a risk-rating process to prioritize the remediation of discovered vulnerabilities.

**Guidance**: Use the default risk ratings (Secure Score) provided by Azure Security Center. 

- [Understand Azure Security Center Secure Score](../../security-center/secure-score-security-controls.md)

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Customer

## Inventory and Asset Management

*For more information, see [Security Control: Inventory and Asset Management](../../security/benchmarks/security-control-inventory-asset-management.md).*

### 6.1: Use Azure Asset Discovery

**Guidance**: Use Azure Resource Graph to query and discover all resources (including Storage accounts) within your subscription(s). Ensure you have appropriate (read) permissions in your tenant and are able to enumerate all Azure subscriptions as well as resources within your subscriptions. 

- [How to create queries with Azure Graph](../../governance/resource-graph/first-query-portal.md)

- [How to view your Azure Subscriptions](/powershell/module/az.accounts/get-azsubscription)

- [Understand Azure RBAC](../../role-based-access-control/overview.md)

**Azure Security Center monitoring**: Currently not available

**Responsibility**: Customer

### 6.2: Maintain Asset Metadata

**Guidance**: Apply tags to Storage account resources giving metadata to logically organize them into a taxonomy. 

- [How to create and use Tags](../../azure-resource-manager/management/tag-resources.md)

**Azure Security Center monitoring**: Currently not available

**Responsibility**: Customer

### 6.3: Delete Unauthorized Azure Resources

**Guidance**: Use tagging, management groups, and separate subscriptions, where appropriate, to organize and track Storage accounts and related resources. Reconcile inventory on a regular basis and ensure unauthorized resources are deleted from the subscription in a timely manner. 

Also, use Advanced Threat Protection for Azure Storage to detect unauthorized Azure Resources. 

- [How to create additional Azure subscriptions](../../cost-management-billing/manage/create-subscription.md)

- [How to create Management Groups](../../governance/management-groups/create-management-group-portal.md)

- [How to create and use Tags](../../azure-resource-manager/management/tag-resources.md)

- [Configure advanced threat protection for Azure Storage](./azure-defender-storage-configure.md?tabs=azure-portal)

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Customer

### 6.4: Maintain inventory of approved Azure resources and software titles.

**Guidance**: You will need to create an inventory of approved Azure resources as per your organizational needs. 


**Azure Security Center monitoring**: Not applicable

**Responsibility**: Customer

### 6.5: Monitor for Unapproved Azure Resources

**Guidance**: Use Azure Policy to put restrictions on the type of resources that can be created in customer subscription(s) using the following built-in policy definitions: 

 - Not allowed resource types 
 - Allowed resource types 

In addition, use the Azure Resource Graph to query/discover resources within the subscription(s). This can help in high security based environments, such as those with Storage accounts. 

- [How to configure and manage Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

- [How to create queries with Azure Graph](../../governance/resource-graph/first-query-portal.md)

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Customer

### 6.6: Monitor for Unapproved Software Applications within Compute Resources

**Guidance**: Not applicable; this recommendation is intended for compute resources.

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Not applicable

### 6.7: Remove Unapproved Azure Resources and Software Applications

**Guidance**: Customer may prevent resource creation or usage with Azure Policy as required by the customer's company policies. 

- [How to configure and manage Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Customer

### 6.8: Use only approved applications

**Guidance**: Not applicable; this recommendation is intended for compute resources.

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Not applicable

### 6.9: Use only approved Azure Services

**Guidance**: Use Azure Policy to put restrictions on the type of resources that can be created in customer subscription(s) using the following built-in policy definitions: 

- Not allowed resource types 
- Allowed resource types 

- [How to configure and manage Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

- [How to deny a specific resource type with Azure Policy](../../governance/policy/samples/index.md)

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Customer

### 6.10: Implement approved application list

**Guidance**: Not applicable; this recommendation is intended for compute resources.

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Not applicable

### 6.11: Limit Users' Ability to interact with Azure Resource Manager via Scripts

**Guidance**: Use the Azure Conditional Access to limit users' ability to interact with Azure Resource Manager by configuring "Block access" for the "Microsoft Azure Management" App. This can prevent the creation and changes to resources within a high security environment, such as those with Storage accounts. 

- [How to configure Conditional Access to block access to ARM](../../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Customer

### 6.12: Limit Users' Ability to Execute Scripts within Compute Resources

**Guidance**: Not applicable; this recommendation is intended for compute resources.

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Not applicable

### 6.13: Physically or Logically Segregate High Risk Applications

**Guidance**: Not applicable; this recommendation is intended for web applications running on Azure App Service or compute resources.

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Not applicable

## Secure Configuration

*For more information, see [Security Control: Secure Configuration](../../security/benchmarks/security-control-secure-configuration.md).*

### 7.1: Establish Secure Configurations for all Azure Resources

**Guidance**: Use Azure Policy aliases in the "Microsoft.Storage" namespace to create custom policies to audit or enforce the configuration of your Storage account instances. You may also use built-in Azure Policy definitions for Azure Storage account such as: 

Audit unrestricted network access to storage accounts  
Deploy Advanced Threat Protection on Storage Accounts  
Storage accounts should be migrated to new Azure Resource Manager resources  
Secure transfer to storage accounts should be enabled  

Use recommendations from Azure Security Center as a secure configuration baseline for your Storage accounts. 

- [How to view available Azure Policy Aliases](/powershell/module/az.resources/get-azpolicyalias)

- [How to configure and manage Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center monitoring**: Yes

**Responsibility**: Customer

### 7.2: Establish Secure Configurations for your Operating System

**Guidance**: Not applicable; this recommendation is intended for compute resources.

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Not applicable

### 7.3: Maintain Secure Configurations for all Azure Resources

**Guidance**: Use Azure Policy [deny] and [deploy if not exist] to enforce secure settings across your Storage account resources. 

- [How to configure and manage Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

- [Understanding Azure Policy Effects](../../governance/policy/concepts/effects.md)

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Customer

### 7.4: Maintain Secure Configurations for Operating Systems

**Guidance**: Not applicable; this recommendation is intended for compute resources.

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Not applicable

### 7.5: Securely Store Configuration of Azure Resources

**Guidance**: Use Azure Repos to securely store and manage your code like custom Azure policies, Azure Resource Manager templates, Desired State Configuration scripts etc. To access the resources you manage in Azure DevOps, you can grant or deny permissions to specific users, built-in security groups, or groups defined in Azure Active Directory (Azure AD) if integrated with Azure DevOps, or Active Directory if integrated with TFS.

- [How to store code in Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [About permissions and groups in Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Customer

### 7.6: Securely Store Custom Operating System Images

**Guidance**: Not applicable; this recommendation is intended for compute resources.

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Not applicable

### 7.7: Deploy System Configuration Management Tools

**Guidance**: Leverage Azure Policy to alert, audit, and enforce system configurations for Storage account. Additionally, develop a process and pipeline for managing policy exceptions. 

- [How to configure and manage Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Customer

### 7.8: Deploy System Configuration Management Tools for Operating Systems

**Guidance**: Not applicable; this recommendation is intended for compute resources.

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Not applicable

### 7.9: Implement Automated Configuration Monitoring for Azure Services

**Guidance**: Leverage Azure Security Center to perform baseline scans for your Azure Storage account resources. 

- [How to remediate recommendations in Azure Security Center](../../security-center/security-center-remediate-recommendations.md)

**Azure Security Center monitoring**: Currently not available

**Responsibility**: Customer

### 7.10: Implement Automated Configuration Monitoring for Operating Systems

**Guidance**: Not applicable; this recommendation is intended for compute resources.

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Not applicable

### 7.11: Securely manage Azure secrets

**Guidance**: Azure Storage automatically encrypts your data when it is persisted it to the cloud. You can use Microsoft-managed keys for the encryption of the storage account, or can manage encryption with their own keys. If you are using customer-provided keys, you can leverage Azure Key Vault to securely store the keys. 

Additionally, rotate Storage Account Keys on a frequent basis to limit the impact of loss or disclosure of Storage Account keys.

- [Azure Storage encryption for data at rest](./storage-service-encryption.md)

- [Manage storage account access keys](./storage-account-keys-manage.md)

**Azure Security Center monitoring**: Yes

**Responsibility**: Customer

### 7.12: Securely and automatically manage identities

**Guidance**: Authorize access to blobs and queues within Azure Storage Accounts with Azure Active Directory and Managed Identities. Azure Blob and Queue storage support Azure Active Directory (Azure AD) authentication with managed identities for Azure resources. Managed identities for Azure resources can authorize access to blob and queue data using Azure AD credentials from applications running in Azure virtual machines (VMs), function apps, virtual machine scale sets, and other services. By using managed identities for Azure resources together with Azure AD authentication, you can avoid storing credentials with your applications that run in the cloud. 

- [How to grant access to Azure blob and queue data using a managed identity](./storage-auth-aad-rbac-portal.md)

**Azure Security Center monitoring**: Currently not available

**Responsibility**: Customer

### 7.13: Eliminate unintended credential exposure

**Guidance**: Implement Credential Scanner to identify credentials within code. Credential Scanner will also encourage moving discovered credentials to more secure locations such as Azure Key Vault. 

- [How to setup Credential Scanner](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Customer

## Malware Defense

*For more information, see [Security Control: Malware Defense](../../security/benchmarks/security-control-malware-defense.md).*

### 8.1: Use Centrally Managed Anti-malware Software

**Guidance**: Not applicable; this recommendation is intended for compute resources. Microsoft handles anti-malware for underlying platform.

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Not applicable

### 8.2: Pre-scan files to be uploaded to non-compute Azure resources

**Guidance**: Use threat protection for Azure Storage for detecting malware uploads to Azure Storage using hash reputation analysis and suspicious access from an active Tor exit node (an anonymizing proxy). 

You can also pre-scan any content for malware before uploading to non-compute Azure resources, such as App Service, Data Lake Storage, Blob Storage, etc to meet your organizations requirements.

- [Configure advanced threat protection for Azure Storage](./azure-defender-storage-configure.md?tabs=azure-portal)

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Customer

### 8.3: Ensure Anti-Malware Software and Signatures are Updated

**Guidance**: Not applicable; this recommendation is intended for compute resources. Microsoft handles anti-malware for underlying platform.

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Not applicable

## Data Recovery

*For more information, see [Security Control: Data Recovery](../../security/benchmarks/security-control-data-recovery.md).*

### 9.1: Ensure Regular Automated Back Ups

**Guidance**: The data in your Microsoft Azure storage account is always automatically replicated to ensure durability and high availability. Azure Storage copies your data so that it is protected from planned and unplanned events, including transient hardware failures, network or power outages, and massive natural disasters. You can choose to replicate your data within the same data center, across zonal data centers within the same region, or across geographically separated regions. 

You can also enable Azure automation to take regular snapshots of the blobs.

- [Understanding Azure Storage redundancy and Service-Level Agreements](./storage-redundancy.md)

- [Create a snapshot of a blob](/rest/api/storageservices/creating-a-snapshot-of-a-blob)

- [Azure Automation overview](../../automation/automation-intro.md)

**Azure Security Center monitoring**: Yes

**Responsibility**: Customer

### 9.2: Perform Complete System Backups and Backup any Customer Managed Keys

**Guidance**: In order to backup data from Storage account supported services, there are multiple methods available including using azcopy or third party tools. Immutable storage for Azure Blob storage enables users to store business-critical data objects in a WORM (Write Once, Read Many) state. This state makes the data non-erasable and non-modifiable for a user-specified interval.

- [Get started with AzCopy](./storage-use-azcopy-v10.md)

- [Set and manage immutability policies for Blob storage](../blobs/storage-blob-immutability-policies-manage.md?tabs=azure-portal)

Customer managed / provided keys can be backed within Azure Key Vault using Azure CLI or PowerShell. 

- [How to backup key vault keys in Azure](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

**Azure Security Center monitoring**: Yes

**Responsibility**: Customer

### 9.3: Validate all Backups including Customer Managed Keys

**Guidance**: Periodically perform data restoration of your Key Vault Certificates, Keys, Managed Storage Accounts, and Secrets, with the following PowerShell commands: 

Restore-AzKeyVaultCertificate 
Restore-AzKeyVaultKey 
Restore-AzKeyVaultManagedStorageAccount
Restore-AzKeyVaultSecret 

- [How to restore Key Vault Certificates](/powershell/module/azurerm.keyvault/restore-azurekeyvaultcertificate)

- [How to restore Key Vault Keys](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey)

- [How to restore Key Vault Managed Storage Accounts](/powershell/module/az.keyvault/backup-azkeyvaultmanagedstorageaccount)

- [How to restore Key Vault Secrets](/powershell/module/azurerm.keyvault/restore-azurekeyvaultsecret)

- [AzCopy is a command-line utility that you can use to copy blobs, files and table data to or from a storage account](./storage-use-azcopy-v10.md)

Note: If you want to copy data to and from your Azure Table storage service, then install AzCopy version 7.3.


**Azure Security Center monitoring**: Not applicable

**Responsibility**: Customer

### 9.4: Ensure Protection of Backups and Customer Managed Keys

**Guidance**: To enable customer-managed keys on a storage account, you must use an Azure Key Vault to store your keys. You must enable both the Soft Delete and Do Not Purge properties on the key vault. Key Vault's Soft Delete feature allows recovery of deleted vaults and vault objects such as keys, secrets, and certificates. If backing Storage account data to Azure Storage blobs, enable soft delete to save and recover your data when blobs or blob snapshots are deleted. You should treat your backups as sensitive data and apply the relevant access and data protection controls as part of this baseline. Additionally, for improved protection, you may store business-critical data objects in a WORM (Write Once, Read Many) state.

- [How to use Azure Key Vault's Soft Delete](../../key-vault/general/key-vault-recovery.md)

- [Soft delete for Azure Storage blobs](../blobs/soft-delete-blob-overview.md?tabs=azure-portal)

- [Store business-critical blob data with immutable storage](../blobs/storage-blob-immutable-storage.md)

**Azure Security Center monitoring**: Yes

**Responsibility**: Customer

## Incident Response

*For more information, see [Security Control: Incident Response](../../security/benchmarks/security-control-incident-response.md).*

### 10.1: Create incident response guide

**Guidance**: Build out an incident response guide for your organization. Ensure that there are written incident response plans that define all roles of personnel as well as phases of incident handling/management from detection to post-incident review.

- [Guidance on building your own security incident response process](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft Security Response Center's Anatomy of an Incident](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [Customer may also leverage NIST's Computer Security Incident Handling Guide to aid in the creation of their own incident response plan](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Customer

### 10.2: Create Incident Scoring and Prioritization Procedure

**Guidance**: Security Center assigns a severity to each alert to help you prioritize which alerts should be investigated first. The severity is based on how confident Security Center is in the finding or the analytic used to issue the alert as well as the confidence level that there was malicious intent behind the activity that led to the alert. 

Additionally, clearly mark subscriptions (for ex. production, non-prod) using tags and create a naming system to clearly identify and categorize Azure resources, especially those processing sensitive data. It is your responsibility to prioritize the remediation of alerts based on the criticality of the Azure resources and environment where the incident occurred.

- [Security alerts in Azure Security Center](../../security-center/security-center-alerts-overview.md)

- [Use tags to organize your Azure resources](../../azure-resource-manager/management/tag-resources.md)

**Azure Security Center monitoring**: Yes

**Responsibility**: Customer

### 10.3: Test Security Response Procedures

**Guidance**: Conduct exercises to test your systems’ incident response capabilities on a regular cadence to help protect your Azure resources. Identify weak points and gaps and revise plan as needed.

- [NIST's publication - Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Customer

### 10.4: Provide Security Incident Contact Details and Configure Alert Notifications for Security Incidents

**Guidance**: Security incident contact information will be used by Microsoft to contact you if the Microsoft Security Response Center (MSRC) discovers that your data has been accessed by an unlawful or unauthorized party. Review incidents after the fact to ensure that issues are resolved.

- [How to set the Azure Security Center Security Contact](../../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center monitoring**: Yes

**Responsibility**: Customer

### 10.5: Incorporate security alerts into your incident response system

**Guidance**: Export your Azure Security Center alerts and recommendations using the Continuous Export feature to help identify risks to Azure resources. Continuous Export allows you to export alerts and recommendations either manually or in an ongoing, continuous fashion. You may use the Azure Security Center data connector to stream the alerts to Azure Sentinel.

- [How to configure continuous export](../../security-center/continuous-export.md)

- [How to stream alerts into Azure Sentinel](../../sentinel/connect-azure-security-center.md)

**Azure Security Center monitoring**: Currently not available

**Responsibility**: Customer

### 10.6: Automate the response to security alerts

**Guidance**: Use the Workflow Automation feature in Azure Security Center to automatically trigger responses via "Logic Apps" on security alerts and recommendations to protect your Azure resources.

- [How to configure Workflow Automation and Logic Apps](../../security-center/workflow-automation.md)

**Azure Security Center monitoring**: Currently not available

**Responsibility**: Customer

## Penetration Tests and Red Team Exercises

*For more information, see [Security Control: Penetration Tests and Red Team Exercises](../../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### 11.1: Conduct regular Penetration Testing of your Azure resources

**Guidance**: Follow the Microsoft Rules of Engagement to ensure your Penetration Tests are not in violation of Microsoft policies. Use Microsoft’s strategy and execution of Red Teaming and live site penetration testing against Microsoft-managed cloud infrastructure, services, and applications.

- [Penetration Testing Rules of Engagement](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Shared

## Next steps

- See the [Azure Security Benchmark](../../security/benchmarks/overview.md)
- Learn more about [Azure Security Baselines](../../security/benchmarks/security-baselines-overview.md)