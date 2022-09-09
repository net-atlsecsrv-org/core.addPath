---
title: Azure security baseline for Cognitive Services
description: The Cognitive Services security baseline provides procedural guidance and resources for implementing the security recommendations specified in the Azure Security Benchmark.
author: msmbaldwin
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark

# Important: This content is machine generated; do not modify this topic directly. Contact mbaldwin for more information.

---

# Azure security baseline for Cognitive Services

This security
baseline applies guidance from the [Azure Security Benchmark version
1.0](../security/benchmarks/overview-v1.md) to Cognitive Services. The Azure Security Benchmark
provides recommendations on how you can secure your cloud solutions on Azure.
The content is grouped by the **security controls** defined by the Azure
Security Benchmark and the related guidance applicable to Cognitive Services. **Controls** not applicable to Cognitive Services have been excluded.

 
To see how Cognitive Services completely maps to the Azure
Security Benchmark, see the [full Cognitive Services security baseline mapping
file](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## Network Security

*For more information, see the [Azure Security Benchmark: Network Security](../security/benchmarks/security-control-network-security.md).*

### 1.1: Protect Azure resources within virtual networks

**Guidance**: Microsoft Azure Cognitive Services provides a layered security model. This model enables you to secure your Cognitive Services accounts to a specific subset of networks. When network rules are configured, only applications requesting data over the specified set of networks can access the account. You can limit access to your resources with request filtering, allowing only requests that originate from specified IP addresses, IP ranges, or from a list of subnets in Azure Virtual Networks.

Virtual network and service endpoint support for Cognitive Services is limited to a specific set of regions.

- [How to configure Azure Cognitive Services virtual networks](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-virtual-networks?tabs=portal)

- [Overview of Azure Virtual Networks](../virtual-network/virtual-networks-overview.md)

**Responsibility**: Customer

**Azure Security Center monitoring**: None

### 1.2: Monitor and log the configuration and traffic of virtual networks, subnets, and network interfaces

**Guidance**: When Virtual Machines are deployed in the same virtual network as your Cognitive Services container, you can use network security groups to reduce the risk of data exfiltration. Enable network security groups flow logs and send logs into an Azure Storage Account for traffic audit. You may also send network security groups flow logs to a Log Analytics workspace and use Traffic Analytics to provide insights into traffic flow in your Azure cloud. Some advantages of Traffic Analytics are the ability to visualize network activity and identify hot spots, identify security threats, understand traffic flow patterns, and pinpoint network misconfigurations.

- [How to Enable NSG Flow Logs](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [How to Enable and use Traffic Analytics](../network-watcher/traffic-analytics.md)

**Responsibility**: Customer

**Azure Security Center monitoring**: None

### 1.3: Protect critical web applications

**Guidance**: If you are using Cognitive Services within a container, you may augment your container deployment with a front-facing web-application firewall solution that filters malicious traffic and supports end-to-end TLS encryption, keeping the container endpoint private and secure. 

Bear in mind that Cognitive Services containers are required to submit metering information for billing purposes. The only exception, is Offline containers as they follow a different billing methodology. Failure to allow list various network channels that the Cognitive Services containers rely on will prevent the container from working. The host should allow list port 443 and the following domains:

- *.cognitive.microsoft.com
- *.cognitiveservices.azure.com

Also note that you must disable deep packet inspection for your firewall solution on the secure channels that the Cognitive Services containers create to Microsoft servers. Failure to do so will prevent the container from functioning correctly.

- [Understand Azure Cognitive Services container security](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-container-support#azure-cognitive-services-container-security)

**Responsibility**: Customer

**Azure Security Center monitoring**: None

### 1.4: Deny communications with known-malicious IP addresses

**Guidance**: When virtual machines are deployed in the same virtual network as your Cognitive Services container, define and implement standard security configurations for related network resources with Azure Policy. Use Azure Policy aliases in the "Microsoft.CognitiveServices" and "Microsoft.Network" namespaces to create custom policies to audit or enforce the network configuration of your Azure Cache for Redis instances. You may also make use of built-in policy definitions such as:

- DDoS Protection Standard should be enabled

Use Azure Blueprints to simplify large-scale Azure deployments by packaging key environment artifacts, such as Azure Resource Manager templates, Azure role-based access control (Azure RBAC), and policies, in a single blueprint definition. Easily apply the blueprint to new subscriptions and environments, and fine-tune control and management through versioning.

If you are using Cognitive Services within a container, you may augment your container deployment with a front-facing web-application firewall solution that filters malicious traffic and supports end-to-end TLS encryption, keeping the container endpoint private and secure. 

- [How to configure and manage Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [How to create an Azure Blueprint](../governance/blueprints/create-blueprint-portal.md)

- [Understand Azure Cognitive Services container security](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-container-support#azure-cognitive-services-container-security)

**Responsibility**: Customer

**Azure Security Center monitoring**: None

### 1.5: Record network packets

**Guidance**: When virtual machines are deployed in the same virtual network as your Cognitive Services container, you can use network security groups to reduce the risk of data exfiltration. Enable network security groups flow logs and send logs into an Azure Storage Account for traffic audit. You may also send network security groups flow logs to a Log Analytics workspace and use Traffic Analytics to provide insights into traffic flow in your Azure cloud. Some advantages of Traffic Analytics are the ability to visualize network activity and identify hot spots, identify security threats, understand traffic flow patterns, and pinpoint network misconfigurations.

- [How to Enable NSG Flow Logs](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [How to Enable and use Traffic Analytics](../network-watcher/traffic-analytics.md)

**Responsibility**: Customer

**Azure Security Center monitoring**: None

### 1.6: Deploy network-based intrusion detection/intrusion prevention systems (IDS/IPS)

**Guidance**: If using Cognitive Services within a container, you may augment your container deployment with a front-facing web-application firewall solution that filters malicious traffic and supports end-to-end TLS encryption, keeping the container endpoint private and secure.  You can select an offer from the Azure Marketplace that supports IDS/IPS functionality with the ability to disable payload inspection.

Bear in mind that Cognitive Services containers are required to submit metering information for billing purposes. The only exception is Offline containers as they follow a different billing methodology. Failure to allow list various network channels that the Cognitive Services containers rely on will prevent the container from working. The host should allow list port 443 and the following domains:

- *.cognitive.microsoft.com
- *.cognitiveservices.azure.com

Also note that you must disable deep packet inspection for your firewall solution on the secure channels that the Cognitive Services containers create to Microsoft servers. Failure to do so will prevent the container from functioning correctly.

- [Understand Azure Cognitive Services container security](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-container-support#azure-cognitive-services-container-security)

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

**Responsibility**: Customer

**Azure Security Center monitoring**: None

### 1.7: Manage traffic to web applications

**Guidance**: If using Cognitive Services within a container, you may augment your container deployment with a front-facing web-application firewall solution that filters malicious traffic and supports end-to-end TLS encryption, keeping the container endpoint private and secure. 

Bear in mind that Cognitive Services containers are required to submit metering information for billing purposes. The only exception, is Offline containers as they follow a different billing methodology. Failure to allow list various network channels that the Cognitive Services containers rely on will prevent the container from working. The host should allow list port 443 and the following domains:

- *.cognitive.microsoft.com
- *.cognitiveservices.azure.com

Also note that you must disable deep packet inspection for your firewall solution on the secure channels that the Cognitive Services containers create to Microsoft servers. Failure to do so will prevent the container from functioning correctly.

- [Understand Azure Cognitive Services container security](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-container-support#azure-cognitive-services-container-security)

**Responsibility**: Customer

**Azure Security Center monitoring**: None

### 1.8: Minimize complexity and administrative overhead of network security rules

**Guidance**: Use virtual network service tags to define network access controls on network security groups or Azure Firewall. You can use service tags in place of specific IP addresses when creating security rules. By specifying the service tag name (for example, ApiManagement) in the appropriate source or destination field of a rule, you can allow or deny the traffic for the corresponding service. Microsoft manages the address prefixes encompassed by the service tag and automatically updates the service tag as addresses change.

You may also use application security groups to help simplify complex security configuration. Application security groups enable you to configure network security as a natural extension of an application's structure, allowing you to group virtual machines and define network security policies based on those groups.

- [Virtual network service tags](../virtual-network/service-tags-overview.md)

- [Application Security Groups](https://docs.microsoft.com/azure/virtual-network/network-security-groups-overview#application-security-groups)

**Responsibility**: Customer

**Azure Security Center monitoring**: None

### 1.9: Maintain standard security configurations for network devices

**Guidance**: Define and implement standard security configurations for network resources related to your Cognitive Services container with Azure Policy. Use Azure Policy aliases in the "Microsoft.CognitiveServices" and "Microsoft.Network" namespaces to create custom policies to audit or enforce the network configuration of your Azure Cache for Redis instances.

You can also use Azure Blueprints to simplify large-scale Azure deployments by packaging key environment artifacts, such as Azure Resource Manager templates, role-based access control (Azure RBAC), and policies, in a single blueprint definition. Easily apply the blueprint to new subscriptions and environments, and fine-tune control and management through versioning.

- [How to configure and manage Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [How to create an Azure Blueprint](../governance/blueprints/create-blueprint-portal.md)

**Responsibility**: Customer

**Azure Security Center monitoring**: None

### 1.10: Document traffic configuration rules

**Guidance**: Use tags for network resources associated with your Cognitive Services container in order to logically organize them into a taxonomy.

- [How to create and use tags](../azure-resource-manager/management/tag-resources.md)

**Responsibility**: Customer

**Azure Security Center monitoring**: None

### 1.11: Use automated tools to monitor network resource configurations and detect changes

**Guidance**: Use the Azure Activity log to monitor network resource configurations and detect changes for network resources related to your Cognitive Services container. Create alerts within Azure Monitor that will trigger when changes to critical network resources take place.

- [How to view and retrieve Azure Activity Log events](/azure/azure-monitor/platform/activity-log#view-the-activity-log)

- [How to create alerts in Azure Monitor](/azure/azure-monitor/platform/alerts-activity-log)

**Responsibility**: Customer

**Azure Security Center monitoring**: None

## Logging and Monitoring

*For more information, see the [Azure Security Benchmark: Logging and Monitoring](../security/benchmarks/security-control-logging-monitoring.md).*

### 2.2: Configure central security log management

**Guidance**: Enable Azure Activity Log diagnostic settings and send the logs to a Log Analytics workspace, Azure event hub, or Azure storage account for archive. Activity logs provide insight into the operations that were performed on your Cognitive Services container at the control plane level. Using Azure Activity Log data, you can determine the "what, who, and when" for any write operations (PUT, POST, DELETE) performed at the control plane level for your Azure Cache for Redis instances.

- [How to enable Diagnostic Settings for Azure Activity Log](/azure/azure-monitor/platform/activity-log)

**Responsibility**: Customer

**Azure Security Center monitoring**: None

### 2.3: Enable audit logging for Azure resources

**Guidance**: For control plane audit logging, enable Azure Activity Log diagnostic settings and send the logs to a Log Analytics workspace, Azure event hub, or Azure storage account for archive. Using Azure Activity Log data, you can determine the "what, who, and when" for any write operations (PUT, POST, DELETE) performed at the control plane level for your Azure resources.

Additionally, Cognitive Services sends diagnostics events that can be collected and used for the purposes of analysis, alerting and reporting. You can configure diagnostics settings for a Cognitive Services container via the Azure portal. You can send one or more diagnostics events to a Storage Account, Event Hub, or a Log Analytics workspace.

- [How to enable Diagnostic Settings for Azure Activity Log](/azure/azure-monitor/platform/diagnostic-settings-legacy)

- [Using diagnostic settings to for Azure Cognitive Services](diagnostic-logging.md)

**Responsibility**: Customer

**Azure Security Center monitoring**: None

### 2.5: Configure security log storage retention

**Guidance**: Within Azure Monitor, set your Log Analytics Workspace retention period according to your organization's compliance regulations. Use Azure Storage accounts for long-term/archival storage.

- [How to set log retention parameters for Log Analytics Workspaces](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Responsibility**: Customer

**Azure Security Center monitoring**: None

### 2.6: Monitor and review logs

**Guidance**: Enable Azure Activity Log diagnostic settings and send the logs to a Log Analytics workspace. These logs provide rich, frequent data about the operation of a resource that are used for issue identification and debugging. Perform queries in Log Analytics to search terms, identify trends, analyze patterns, and provide many other insights based on the Activity Log Data that may have been collected for Azure Cognitive Services.

- [How to enable Diagnostic Settings for Azure Activity Log](/azure/azure-monitor/platform/activity-log)

- [How to collect and analyze Azure activity logs in Log Analytics workspace in Azure Monitor](/azure/azure-monitor/platform/activity-log)

**Responsibility**: Customer

**Azure Security Center monitoring**: None

### 2.7: Enable alerts for anomalous activities

**Guidance**: You can raise alerts on supported metrics in Cognitive Services by going to the Alerts and Metrics section in Azure Monitor.

Configure diagnostic settings for your Cognitive Services container and send logs to a Log Analytics workspace. Within your Log Analytics workspace, configure alerts to take place for when a pre-defined set of conditions takes place. Alternatively, you may enable and on-board data to Azure Sentinel or a third-party SIEM.

- [How to onboard Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Create, view, and manage log alerts using Azure Monitor](/azure/azure-monitor/platform/alerts-log)

**Responsibility**: Customer

**Azure Security Center monitoring**: None

## Identity and Access Control

*For more information, see the [Azure Security Benchmark: Identity and Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### 3.1: Maintain an inventory of administrative accounts

**Guidance**: Azure Active Directory (Azure AD) has built-in roles that must be explicitly assigned and are queryable. Use the Azure AD PowerShell module to perform ad hoc queries to discover accounts that are members of administrative groups.

- [How to get a directory role in Azure AD with PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0&amp;preserve-view=true)

- [How to get members of a directory role in Azure AD with PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0&amp;preserve-view=true)

**Responsibility**: Customer

**Azure Security Center monitoring**: None

### 3.2: Change default passwords where applicable

**Guidance**: Control plane access to Cognitive Services is controlled through Azure Active Directory (Azure AD). Azure AD does not have the concept of default passwords.

Data plane access to Cognitive Services is controlled through access keys. These keys are used by the clients connecting to your cache and can be regenerated at any time.

It is not recommended that you build default passwords into your application. Instead, you can store your passwords in Azure Key Vault and then use Azure AD to retrieve them.

- [How to regenerate Azure Cache for Redis access keys](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-configure#settings)

**Responsibility**: Customer

**Azure Security Center monitoring**: None

### 3.3: Use dedicated administrative accounts

**Guidance**: Create standard operating procedures around the use of dedicated administrative accounts. Use Security Center Identity and Access Management to monitor the number of administrative accounts.

Additionally, to help you keep track of dedicated administrative accounts, you may use recommendations from Security Center or built-in Azure Policies, such as:

- There should be more than one owner assigned to your subscription
- Deprecated accounts with owner permissions should be removed from your subscription
- External accounts with owner permissions should be removed from your subscription

- [How to use Azure Security Center to monitor identity and access (Preview)](../security-center/security-center-identity-access.md)

- [How to use Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Responsibility**: Customer

**Azure Security Center monitoring**: None

### 3.4: Use Azure Active Directory single sign-on (SSO)

**Guidance**: Cognitive Services uses access keys to authenticate users and does not support single sign-on (SSO) at the data plane level. Access to the control plane for Cognitive Services is available via REST API and supports SSO. To authenticate, set the Authorization header for your requests to a JSON (JavaScript Object Notation) Web Token that you obtain from Azure Active Directory (Azure AD).

- [Understand Azure Cognitive Services REST API](/rest/api/cognitiveservices/)

- [Understand SSO with Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Responsibility**: Customer

**Azure Security Center monitoring**: None

### 3.5: Use multi-factor authentication for all Azure Active Directory-based access

**Guidance**: Enable Azure Active Directory (Azure AD) multifactor authentication and follow Security Center Identity and Access Management recommendations.

- [How to enable multifactor authentication in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [How to monitor identity and access within Azure Security Center](../security-center/security-center-identity-access.md)

**Responsibility**: Customer

**Azure Security Center monitoring**: None

### 3.6: Use secure, Azure-managed workstations for administrative tasks

**Guidance**: Use privileged access workstations (PAW) with multifactor authentication configured to log into and configure Azure resources. 

- [Learn about Privileged Access Workstations](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [How to enable multifactor authentication in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Responsibility**: Customer

**Azure Security Center monitoring**: None

### 3.7: Log and alert on suspicious activities from administrative accounts

**Guidance**: Use Azure Active Directory (Azure AD) Privileged Identity Management (PIM) for generation of logs and alerts when suspicious or unsafe activity occurs in the environment.

In addition, use Azure AD risk detections to view alerts and reports on risky user behavior.

- [How to deploy Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Understand Azure AD risk detections](../active-directory/identity-protection/overview-identity-protection.md)

**Responsibility**: Customer

**Azure Security Center monitoring**: None

### 3.8: Manage Azure resources from only approved locations

**Guidance**: Configure named locations in Azure Active Directory (Azure AD) Conditional Access to allow access from only specific logical groupings of IP address ranges or countries and regions.

- [How to configure Named Locations in Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Responsibility**: Customer

**Azure Security Center monitoring**: None

### 3.9: Use Azure Active Directory

**Guidance**: Use Azure Active Directory (Azure AD) as the central authentication and authorization system. Azure AD protects data by using strong encryption for data at rest and in transit and also salts, hashes, and securely stores user credentials. If your use case supports AD authentication, use Azure AD to authenticate requests to your Cognitive Services API. 

Currently, only the Computer Vision API, Face API, Text Analytics API, Immersive Reader, Form Recognizer, Anomaly Detector, and all Bing services except Bing Custom Search support authentication using Azure AD.

- [How to authenticate requests to Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/authentication#authenticate-with-azure-active-directory)

**Responsibility**: Customer

**Azure Security Center monitoring**: None

### 3.10: Regularly review and reconcile user access

**Guidance**: Azure Active Directory (Azure AD) provides logs to help discover stale accounts. In addition, customer to utilize Azure Identity Access Reviews to efficiently manage group memberships, access to enterprise applications, and role assignments. User's access can be reviewed on a regular basis to make sure only active users have continued access.

Customer to maintain inventory of API Management user accounts, reconcile access as needed. In API Management, developers are the users of the APIs that you expose using API Management. By default, newly created developer accounts are Active, and associated with the Developers group. Developer accounts that are in an active state can be used to access all of the APIs for which they have subscriptions.

- [How to manage user accounts in Azure API Management](../api-management/api-management-howto-create-or-invite-developers.md)

- [How to get list of API Management users](https://docs.microsoft.com/powershell/module/az.apimanagement/get-azapimanagementuser?view=azps-4.8.0&amp;preserve-view=true)

- [How to use Azure Identity Access Reviews](../active-directory/governance/access-reviews-overview.md)

**Responsibility**: Customer

**Azure Security Center monitoring**: None

### 3.11: Monitor attempts to access deactivated credentials

**Guidance**: You have access to Azure Active Directory (Azure AD) sign-in activity, audit and risk event log sources, which allow you to integrate with Azure Sentinel or a third-party SIEM.

You can streamline this process by creating diagnostic settings for Azure AD user accounts and sending the audit logs and sign-in logs to a Log Analytics workspace. You can configure desired log alerts within Log Analytics.

- [How to integrate Azure Activity Logs into Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [How to on-board Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsibility**: Customer

**Azure Security Center monitoring**: None

### 3.12: Alert on account sign-in behavior deviation

**Guidance**: For account login behavior deviation on the control plane, use Azure Active Directory (Azure AD) Identity Protection and risk detection features to configure automated responses to detected suspicious actions related to user identities. You can also ingest data into Azure Sentinel for further investigation.

- [How to view Azure AD risky sign-ins](../active-directory/identity-protection/overview-identity-protection.md)

- [How to configure and enable Identity Protection risk policies](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [How to onboard Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsibility**: Customer

**Azure Security Center monitoring**: None

### 3.13: Provide Microsoft with access to relevant customer data during support scenarios

**Guidance**: Not available for Cognitive Services. Customer Lockbox is not yet supported for Cognitive Services.

- [List of Customer Lockbox-supported services](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability)

**Responsibility**: Customer

**Azure Security Center monitoring**: None

## Data Protection

*For more information, see the [Azure Security Benchmark: Data Protection](../security/benchmarks/security-control-data-protection.md).*

### 4.1: Maintain an inventory of sensitive Information

**Guidance**: Use tags to assist in tracking Azure resources that store or process sensitive information.

- [How to create and use tags](../azure-resource-manager/management/tag-resources.md)

**Responsibility**: Customer

**Azure Security Center monitoring**: None

### 4.2: Isolate systems storing or processing sensitive information

**Guidance**: Implement separate subscriptions or management groups for development, test, and production. Resources should be separated by Virtual Networks or subnets, tagged appropriately, and secured by a network security group or Azure Firewall. Resources storing or processing sensitive data should be sufficiently isolated. For Virtual Machines storing or processing sensitive data, implement policy and procedures to turn them off when not in use.

- [How to create additional Azure subscriptions](../cost-management-billing/manage/create-subscription.md)

- [How to create management groups](../governance/management-groups/create-management-group-portal.md)

- [How to create and use tags](../azure-resource-manager/management/tag-resources.md)

- [How to create a Virtual Network](../virtual-network/quick-create-portal.md)

- [How to create an NSG with a Security Config](../virtual-network/tutorial-filter-network-traffic.md)

- [How to deploy Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

- [How to configure alert or alert and deny with Azure Firewall](../firewall/threat-intel.md)

**Responsibility**: Customer

**Azure Security Center monitoring**: None

### 4.3: Monitor and block unauthorized transfer of sensitive information

**Guidance**: Not yet available. Data identification, classification, and loss prevention features are not yet available for Cognitive Services.

Microsoft manages the underlying infrastructure for Cognitive Services and has implemented strict controls to prevent the loss or exposure of customer data.

- [Understand customer data protection in Azure](../security/fundamentals/protection-customer-data.md)

**Responsibility**: Customer

**Azure Security Center monitoring**: None

### 4.4: Encrypt all sensitive information in transit

**Guidance**: All of the Cognitive Services endpoints are exposed over HTTP enforce TLS 1.2. With an enforced security protocol, consumers attempting to call a Cognitive Services endpoint should adhere to these guidelines:

- The client Operating System (OS) needs to support TLS 1.2.
- The language (and platform) used to make the HTTP call need to specify TLS 1.2 as part of the request. Depending on the language and platform, specifying TLS is done either implicitly or explicitly.

- [Understand Transport Layer Security for Azure Cognitive Services](cognitive-services-security.md)

**Responsibility**: Shared

**Azure Security Center monitoring**: None

### 4.5: Use an active discovery tool to identify sensitive data

**Guidance**: Data identification, classification, and loss prevention features are not yet available for Cognitive Services. Tag instances containing sensitive information as such and implement third-party solution if required for compliance purposes.

Microsoft manages the underlying platform and treats all customer content as sensitive and goes to great lengths to guard against customer data loss and exposure. To ensure customer data within Azure remains secure, Microsoft has implemented and maintains a suite of robust data protection controls and capabilities.

- [Understand customer data protection in Azure](../security/fundamentals/protection-customer-data.md)

**Responsibility**: Shared

**Azure Security Center monitoring**: None

### 4.6: Use Azure RBAC to control access to resources 

**Guidance**: Use Azure role-based access control (Azure RBAC) to control access to the Cognitive Services control plane (i.e. Azure portal). 

- [How to configure Azure RBAC](../role-based-access-control/role-assignments-portal.md)

**Responsibility**: Customer

**Azure Security Center monitoring**: None

### 4.8: Encrypt sensitive information at rest

**Guidance**: Encryption at rest for Cognitive Services is dependent on the specific service being used. In most cases, data is encrypted and decrypted using FIPS 140-2 compliant 256-bit AES encryption. Encryption and decryption are transparent, meaning encryption and access are managed for the customers by Microsoft. Customer data is secure by default and they do not need to modify their code or applications to take advantage of encryption.

You may also use Azure Key Vault to store your customer-managed keys. You can either create your own keys and store them in a key vault, or you can use the Azure Key Vault APIs to generate keys.

- [List of services that encrypt information at rest](/azure/cognitive-services/encryption/cognitive-services-encryption-keys-portal)

**Responsibility**: Customer

**Azure Security Center monitoring**: None

### 4.9: Log and alert on changes to critical Azure resources

**Guidance**: Use Azure Monitor with the Azure Activity log to create alerts for when changes take place to production instances of Cognitive Services and other critical or related resources.

- [How to create alerts for Azure Activity Log events](/azure/azure-monitor/platform/alerts-activity-log)

**Responsibility**: Customer

**Azure Security Center monitoring**: None

## Inventory and Asset Management

*For more information, see the [Azure Security Benchmark: Inventory and Asset Management](../security/benchmarks/security-control-inventory-asset-management.md).*

### 6.1: Use automated asset discovery solution

**Guidance**: Use Azure Resource Graph to query or discover all resources (such as compute, storage, network, ports, protocols and so on) within your subscriptions. Ensure appropriate (read) permissions in your tenant and enumerate all Azure subscriptions as well as resources within your subscriptions.

Although classic Azure resources may be discovered via Resource Graph, it is highly recommended to create and use Azure Resource Manager resources going forward.

- [How to create queries with Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [How to view your Azure Subscriptions](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-4.8.0&amp;preserve-view=true)

- [Understand Azure RBAC](../role-based-access-control/overview.md)

**Responsibility**: Customer

**Azure Security Center monitoring**: None

### 6.2: Maintain asset metadata

**Guidance**: Apply tags to Azure resources giving metadata to logically organize them into a taxonomy.

- [How to create and use tags](../azure-resource-manager/management/tag-resources.md)

**Responsibility**: Customer

**Azure Security Center monitoring**: None

### 6.3: Delete unauthorized Azure resources

**Guidance**: Use tagging, management groups, and separate subscriptions, where appropriate, to organize and track Azure Cache for Redis instances and related resources. Reconcile inventory on a regular basis and ensure unauthorized resources are deleted from the subscription in a timely manner.

In addition, use Azure Policy to put restrictions on the type of resources that can be created in customer subscriptions using the following built-in policy definitions:

- Not allowed resource types
- Allowed resource types

- [How to create additional Azure subscriptions](../cost-management-billing/manage/create-subscription.md)

- [How to create management groups](../governance/management-groups/create-management-group-portal.md)

- [How to create and use tags](../azure-resource-manager/management/tag-resources.md)

**Responsibility**: Customer

**Azure Security Center monitoring**: None

### 6.5: Monitor for unapproved Azure resources

**Guidance**: Use Azure Policy to put restrictions on the type of resources that can be created in customer subscriptions using the following built-in policy definitions:

- Not allowed resource types
- Allowed resource types

In addition, use Azure Resource Graph to query or discover resources within the subscriptions.

- [How to configure and manage Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [How to create queries with Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

**Responsibility**: Customer

**Azure Security Center monitoring**: None

### 6.9: Use only approved Azure services

**Guidance**: Use Azure Policy to put restrictions on the type of resources that can be created in customer subscriptions using the following built-in policy definitions:
- Not allowed resource types
- Allowed resource types

- [How to configure and manage Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [How to deny a specific resource type with Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**Responsibility**: Customer

**Azure Security Center monitoring**: None

### 6.11: Limit users' ability to interact with Azure Resource Manager

**Guidance**: Configure Azure Conditional Access to limit users' ability to interact with Azure Resource Manager by configuring "Block access" for the "Microsoft Azure Management" App.

- [How to configure Conditional Access to block access to Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Responsibility**: Customer

**Azure Security Center monitoring**: None

## Secure Configuration

*For more information, see the [Azure Security Benchmark: Secure Configuration](../security/benchmarks/security-control-secure-configuration.md).*

### 7.1: Establish secure configurations for all Azure resources

**Guidance**: Define and implement standard security configurations for your Cognitive Services container with Azure Policy. Use Azure Policy aliases in the "Microsoft.CognitiveServices" namespace to create custom policies to audit or enforce the configuration of your Azure Cache for Redis instances.

- [How to view available Azure Policy Aliases](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [How to configure and manage Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Responsibility**: Customer

**Azure Security Center monitoring**: None

### 7.3: Maintain secure Azure resource configurations

**Guidance**: Use Azure Policy [deny] and [deploy if not exist] effects, to enforce secure settings across your Azure resources.

- [How to configure and manage Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Understand Azure Policy Effects](../governance/policy/concepts/effects.md)

**Responsibility**: Customer

**Azure Security Center monitoring**: None

### 7.5: Securely store configuration of Azure resources

**Guidance**: If you are using custom Azure Policy definitions or Azure Resource Manager templates for your Cognitive Services containers and related resources, use Azure Repos to securely store and manage your code.

- [How to store code in Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops&amp;preserve-view=true)

- [Azure Repos Documentation](https://docs.microsoft.com/azure/devops/repos/?view=azure-devops&amp;preserve-view=true)

**Responsibility**: Customer

**Azure Security Center monitoring**: None

### 7.7: Deploy configuration management tools for Azure resources

**Guidance**: Use Azure Policy aliases in the "Microsoft.Cache" namespace to create custom policies to alert, audit, and enforce system configurations. Additionally, develop a process and pipeline for managing policy exceptions.

- [How to configure and manage Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Responsibility**: Customer

**Azure Security Center monitoring**: None

### 7.9: Implement automated configuration monitoring for Azure resources

**Guidance**: Use Azure Policy aliases in the "Microsoft.CognitiveServices" namespace to create custom Azure Policy definitions to alert, audit, and enforce system configurations. Use Azure Policy [audit], [deny], and [deploy if not exist] effects, to automatically enforce configurations for your Azure Cache for Redis instances and related resources.

- [How to configure and manage Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Responsibility**: Customer

**Azure Security Center monitoring**: None

### 7.11: Manage Azure secrets securely

**Guidance**: For Azure virtual machines or web applications running on Azure App Service being used to access your Cognitive Services API, use Managed Service Identity in conjunction with Azure Key Vault to simplify and secure Cognitive Services key management. Ensure Key Vault soft delete is enabled.

- [How to integrate with Azure Managed Identities](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [How to create a Key Vault](/azure/key-vault/quick-create-portal)

- [How to authenticate to Key Vault](../key-vault/general/authentication.md)

- [How to assign a Key Vault access policy](../key-vault/general/assign-access-policy-portal.md)

**Responsibility**: Customer

**Azure Security Center monitoring**: None

### 7.12: Manage identities securely and automatically

**Guidance**: For Azure virtual machines or web applications running on Azure App Service being used to access your Cognitive Services API, use Managed Service Identity in conjunction with Azure Key Vault to simplify and secure Cognitive Services key management. Ensure Key Vault Soft Delete is enabled.

Use Managed Identities to provide Azure services with an automatically managed identity in Azure Active Directory (Azure AD). Managed Identities allows you to authenticate to any service that supports Azure AD authentication, including Azure Key Vault, without any credentials in your code.

- [How to configure Managed Identities](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

- [How to integrate with Azure Managed Identities](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

**Responsibility**: Customer

**Azure Security Center monitoring**: None

### 7.13: Eliminate unintended credential exposure

**Guidance**: Implement Credential Scanner to identify credentials within code. Credential Scanner will also encourage moving discovered credentials to more secure locations such as Azure Key Vault.

- [How to setup Credential Scanner](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Responsibility**: Customer

**Azure Security Center monitoring**: None

## Malware Defense

*For more information, see the [Azure Security Benchmark: Malware Defense](../security/benchmarks/security-control-malware-defense.md).*

### 8.2: Pre-scan files to be uploaded to non-compute Azure resources

**Guidance**: Microsoft anti-malware is enabled on the underlying host that supports Azure services (for example, Azure Cache for Redis), however it does not run on customer content.

Pre-scan any content being uploaded to non-compute Azure resources, such as App Service, Data Lake Storage, Blob Storage, Azure Database for PostgreSQL, and so on. Microsoft cannot access your data in these instances.

**Responsibility**: Customer

**Azure Security Center monitoring**: None

## Data Recovery

*For more information, see the [Azure Security Benchmark: Data Recovery](../security/benchmarks/security-control-data-recovery.md).*

### 9.1: Ensure regular automated back-ups

**Guidance**: The data in your Microsoft Azure storage account is always automatically replicated to ensure durability and high availability. Azure Storage copies your data so that it is protected from planned and unplanned events, including transient hardware failures, network or power outages, and massive natural disasters. You can choose to replicate your data within the same data center, across zonal data centers within the same region, or across geographically separated regions. 

You can also use lifecycle management feature to back up data to the Archive tier. Additionally, enable soft delete for your backups stored in Storage account.

- [Understanding Azure Storage redundancy and Service-Level Agreements](../storage/common/storage-redundancy.md)

- [Manage the Azure Blob storage lifecycle](../storage/blobs/storage-lifecycle-management-concepts.md)

- [Soft delete for Azure Storage blobs](../storage/blobs/soft-delete-blob-overview.md)

**Responsibility**: Customer

**Azure Security Center monitoring**: None

### 9.2: Perform complete system backups and backup any customer-managed keys

**Guidance**: Use Azure Resource Manager to deploy Cognitive Services and related resources. Azure Resource Manager provides the ability to export templates, which allows you to redeploy your solution throughout the development lifecycle and have confidence your resources are deployed in a consistent state. Use Azure Automation to call the Azure Resource Manager template export API on a regular basis. Back up pre-shared keys within Azure Key Vault.

- [Overview of Azure Resource Manager](../azure-resource-manager/management/overview.md)

- [How to create a Cognitive Services resource using an Azure Resource Manager template](https://docs.microsoft.com/azure/cognitive-services/resource-manager-template?tabs=portal)

- [Single and multi-resource export to a template in Azure portal](../azure-resource-manager/templates/export-template-portal.md)

- [Resource Groups - Export Template](/rest/api/resources/resourcegroups/exporttemplate)

- [Introduction to Azure Automation](../automation/automation-intro.md)

- [How to backup key vault keys in Azure](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

**Responsibility**: Customer

**Azure Security Center monitoring**: None

### 9.3: Validate all backups including customer-managed keys

**Guidance**: Ensure ability to periodically perform deployment of Azure Resource Manager templates on a regular basis to an isolated subscription if required. Test restoration of backed up pre-shared keys.

- [Deploy resources with ARM templates and Azure portal](../azure-resource-manager/templates/deploy-portal.md)

- [How to restore key vault keys in Azure](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

**Responsibility**: Customer

**Azure Security Center monitoring**: None

### 9.4: Ensure protection of backups and customer-managed keys

**Guidance**: Use Azure DevOps to securely store and manage your Azure Resource Manager templates. To protect resources you manage in Azure DevOps, you can grant or deny permissions to specific users, built-in security groups, or groups defined in Azure Active Directory (Azure AD) if integrated with Azure DevOps, or Active Directory if integrated with Team Foundation Server.

Use Azure role-based access control to protect customer-managed keys. Enable Soft-Delete and purge protection in Key Vault to protect keys against accidental or malicious deletion. 

- [How to store code in Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops&amp;preserve-view=true)

- [About permissions and groups in Azure DevOps](/azure/devops/organizations/security/about-permissions)

- [Soft delete for Azure Storage blobs](../storage/blobs/soft-delete-blob-overview.md)

**Responsibility**: Customer

**Azure Security Center monitoring**: None

## Incident Response

*For more information, see the [Azure Security Benchmark: Incident Response](../security/benchmarks/security-control-incident-response.md).*

### 10.1: Create an incident response guide

**Guidance**: Build out an incident response guide for your organization. Ensure that there are written incident response plans that define all roles of personnel as well as phases of incident handling/management from detection to post-incident review.

- [How to configure Workflow Automations within Azure Security Center](../security-center/security-center-planning-and-operations-guide.md)

- [Guidance on building your own security incident response process](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process)

- [You may also leverage NIST's Computer Security Incident Handling Guide to aid in the creation of your own incident response plan](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Responsibility**: Customer

**Azure Security Center monitoring**: None

### 10.2: Create an incident scoring and prioritization procedure

**Guidance**: Security Center assigns a severity to each alert to help you prioritize which alerts should be investigated first. The severity is based on how confident Security Center is in the finding or the analytics used to issue the alert as well as the confidence level that there was malicious intent behind the activity that led to the alert. 

Additionally, clearly mark subscriptions (for ex. production, non-prod) and create a naming system to clearly identify and categorize Azure resources.

**Responsibility**: Customer

**Azure Security Center monitoring**: None

### 10.3: Test security response procedures

**Guidance**: Conduct exercises to test your systems’ incident response capabilities on a regular cadence. Identify weak points and gaps and revise plan as needed.

- [Refer to NIST's publication: Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Responsibility**: Customer

**Azure Security Center monitoring**: None

### 10.4: Provide security incident contact details and configure alert notifications for security incidents

**Guidance**: Security incident contact information will be used by Microsoft to contact you if the Microsoft Security Response Center (MSRC) discovers that the customer's data has been accessed by an unlawful or unauthorized party.  Review incidents after the fact to ensure that issues are resolved.

- [How to set the Azure Security Center Security Contact](../security-center/security-center-provide-security-contact-details.md)

**Responsibility**: Customer

**Azure Security Center monitoring**: None

### 10.5: Incorporate security alerts into your incident response system

**Guidance**: Export your Security Center alerts and recommendations using the Continuous Export feature. Continuous Export allows you to export alerts and recommendations either manually or in an ongoing, continuous fashion. You may use the Security Center data connector to stream the alerts to Azure Sentinel.

- [How to configure continuous export](../security-center/continuous-export.md)

- [How to stream alerts into Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Responsibility**: Customer

**Azure Security Center monitoring**: None

### 10.6: Automate the response to security alerts

**Guidance**: Use the Workflow Automation feature in Security Center to automatically trigger responses via "Logic Apps" on security alerts and recommendations.

- [How to configure Workflow Automation and Logic Apps](../security-center/workflow-automation.md)

**Responsibility**: Customer

**Azure Security Center monitoring**: None

## Penetration Tests and Red Team Exercises

*For more information, see the [Azure Security Benchmark: Penetration Tests and Red Team Exercises](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### 11.1: Conduct regular penetration testing of your Azure resources and ensure remediation of all critical security findings

**Guidance**: Follow the Microsoft Cloud Penetration Testing Rules of Engagement to ensure your penetration tests are not in violation of Microsoft policies. Use Microsoft's strategy and execution of Red Teaming and live site penetration testing against Microsoft-managed cloud infrastructure, services, and applications. 

- [Penetration Testing Rules of Engagement](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Responsibility**: Shared

**Azure Security Center monitoring**: None

## Next steps

- See the [Azure Security Benchmark V2 overview](/azure/security/benchmarks/overview)
- Learn more about [Azure security baselines](/azure/security/benchmarks/security-baselines-overview)
