---
title: Azure security baseline for Azure Managed Applications
description: The Azure Managed Applications security baseline provides procedural guidance and resources for implementing the security recommendations specified in the Azure Security Benchmark.
author: msmbaldwin
ms.service: managed-applications
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark

# Important: This content is machine generated; do not modify this topic directly. Contact mbaldwin for more information.

---

# Azure security baseline for Azure Managed Applications

This security baseline applies guidance from the [Azure Security Benchmark version 2.0](../../security/benchmarks/overview.md) to Azure Managed Applications. The Azure Security Benchmark provides recommendations on how you can secure your cloud solutions on Azure. The content is grouped by the **security controls** defined by the Azure Security Benchmark and the related guidance applicable to Azure Managed Applications. **Controls** not applicable to Azure Managed Applications have been excluded.

To see how Azure Managed Applications completely maps to the Azure Security Benchmark, see the [full Azure Managed Applications security baseline mapping file](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## Network Security

*For more information, see the [Azure Security Benchmark: Network Security](/azure/security/benchmarks/security-controls-v2-network-security).*

### NS-6: Simplify network security rules

**Guidance**: Use Azure Virtual Network Service Tags to define network access controls on network security groups or Azure Firewall configured for your Azure Managed Application resources. You can use service tags in place of specific IP addresses when creating security rules. By specifying the service tag name (For example: AzureResourceManager) in the appropriate source or destination field of a rule, you can allow or deny the traffic for the corresponding service. Microsoft manages the address prefixes encompassed by the service tag and automatically updates the service tag as addresses change.

- [Use Azure Resource Manager Service Tags](../../virtual-network/service-tags-overview.md#available-service-tags)

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Customer

## Identity Management

*For more information, see the [Azure Security Benchmark: Identity Management](/azure/security/benchmarks/security-controls-v2-identity-management).*

### IM-1: Standardize Azure Active Directory as the central identity and authentication system

**Guidance**: Azure Managed Applications uses Azure Active Directory (Azure AD) as the default identity and access management service. Standardize on Azure AD to govern your organization’s identity and access management.

Azure provides the following Azure built-in roles for authorizing access to Managed Applications using Azure AD and OAuth:

- Managed Application Contributor Role: Allows for creating managed application resources.

- Managed Application Operator Role: Lets you read and perform actions on Managed Application resources

- Managed Applications Reader: Lets you read resources in a managed app and request JIT access.

For more information, see the following references:

- [Managed application contributor role](../../role-based-access-control/built-in-roles.md#managed-application-contributor-role)

- [Managed application operator role](../../role-based-access-control/built-in-roles.md#managed-application-contributor-role)

- [Managed applications reader](../../role-based-access-control/built-in-roles.md#managed-applications-reader)

- [Tenancy in Azure Active Directory](../../active-directory/develop/single-and-multi-tenant-apps.md)

**Azure Security Center monitoring**: Yes

**Responsibility**: Customer

### IM-2: Manage application identities securely and automatically

**Guidance**: Use Azure-managed identities for granting permissions to your Managed Applications. It is recommended to use Azure managed identity feature instead of creating a more powerful human account to access or execute your resources to limit the need to manage additional credentials. The Azure Managed Applications service can also be assigned a managed identity itself to natively authenticate to other Azure services/resources that supports Azure AD authentication. This can be useful to enable easy access from your Azure Managed Applications to Azure Key Vault when retrieving secrets. When using managed identities, the identity is managed by the Azure platform and does not require you to provision or rotate any secrets.

Azure Managed Applications supports your application being granted two types of identities:

- A system-assigned identity is tied to your configuration resource. It's deleted if your configuration resource is deleted. A configuration resource can only have one system-assigned identity.

- A user-assigned identity is a standalone Azure resource that can be assigned to your configuration resource. A configuration resource can have multiple user-assigned identities.

When managed identities cannot be leveraged, create a service principal with restricted permissions at the Azure Managed Application resource level. Configure these service principals with certificate credentials and only fall back to client secrets. In both cases, Azure Key Vault can be used to in conjunction with Azure managed identities, so that the runtime environment (e.g., an Azure function) can retrieve the credential from the key vault.

- [How to use managed identities for Azure Managed Applications](publish-managed-identity.md)

- [Azure managed identities](../../active-directory/managed-identities-azure-resources/overview.md)

- [Azure service principal](/powershell/azure/create-azure-service-principal-azureps) 

- [Use Azure Key Vault for security principal registration](../../key-vault/general/authentication.md#app-identity-and-security-principals)

**Azure Security Center monitoring**: Yes

**Responsibility**: Customer

### IM-3: Use Azure AD single sign-on (SSO) for application access

**Guidance**: Azure Managed Applications use Azure Active Directory to provide identity and access management to Azure resources, cloud applications, and on-premises applications. This includes enterprise identities such as employees, as well as external identities such as partners, vendors, and suppliers. This enables single sign-on (SSO) to manage and secure access to your organization’s data and resources on-premises and in the cloud. Connect all your users, applications, and devices to the Azure AD for seamless, secure access and greater visibility and control.

- [Understand Application SSO with Azure AD](../../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Customer

### IM-4: Use strong authentication controls for all Azure Active Directory based access

**Guidance**: Azure Managed Applications uses Azure Active Directory which supports strong authentication controls through multifactor authentication, and strong passwordless methods.
- Multifactor authentication - Enable Azure AD multifactor authentication and follow Azure Security Center Identity and Access Management recommendations for some best practices in your multifactor authentication setup. multifactor authentication can be enforced on all, select users or at the per-user level based on sign-in conditions and risk factors.
- Passwordless authentication – Three passwordless authentication options are available: Windows Hello for Business, Microsoft Authenticator app, and on-premises authentication methods such as smart cards.

For administrator and privileged users, ensure the highest level of strong authentication is used.

- [How to enable multifactor authentication in Azure](../../active-directory/authentication/howto-mfa-getstarted.md) 

- [Introduction to passwordless authentication options for Azure Active Directory](../../active-directory/authentication/concept-authentication-passwordless.md)

**Azure Security Center monitoring**: Currently not available

**Responsibility**: Customer

### IM-5: Monitor and alert on account anomalies

**Guidance**: Azure Managed Applications is integrated with Azure Active Directory in which provides the following data sources:
- Sign-ins - The sign-ins report provides information about the usage of managed applications and user sign-in activities.
- Audit logs - Provides traceability through logs for all changes done by various features within Azure AD. Examples of audit logs include changes made to any resources within Azure AD like adding or removing users, apps, groups, roles and policies.
- Risky sign-ins - A risky sign-in is an indicator for a sign-in attempt that might have been performed by someone who is not the legitimate owner of a user account.
- Users flagged for risk - A risky user is an indicator for a user account that might have been compromised.

These data sources can be integrated with Azure Monitor, Azure Sentinel or third-party SIEM systems.

Azure Security Center can also alert on certain suspicious activities such as excessive number of failed authentication attempts, deprecated accounts in the subscription.

Azure Advanced Threat Protection (ATP) is a security solution that can use Active Directory signals to identify, detect, and investigate advanced threats, compromised identities, and malicious insider actions.

- [Audit activity reports in the Azure Active Directory](../../active-directory/reports-monitoring/concept-audit-logs.md)

- [How to view Azure AD risky sign-ins](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [How to monitor users' identity and access activity in Azure Security Center](../../security-center/security-center-identity-access.md)

- [How to integrate Azure Activity Logs into Azure Monitor](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Azure Security Center monitoring**: Yes

**Responsibility**: Customer

### IM-6: Restrict Azure resource access based on conditions

**Guidance**: Azure Managed Applications supports Azure AD conditional access for a more granular access control based on user-defined conditions, such as user logins from certain IP ranges will need to use multifactor authentication for login. Granular authentication session management policy can also be used for different use cases.

- [Azure conditional access overview](../../active-directory/conditional-access/overview.md) 

- [Common conditional access policies](../../active-directory/conditional-access/concept-conditional-access-policy-common.md) 

- [Configure authentication session management with conditional access](../../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

**Azure Security Center monitoring**: Yes

**Responsibility**: Customer

## Privileged Access

*For more information, see the [Azure Security Benchmark: Privileged Access](/azure/security/benchmarks/security-controls-v2-privileged-access).*

### PA-1: Protect and limit highly privileged users

**Guidance**: Azure Managed Applications uses Azure Active Directory (Azure AD) for identity and access. The most critical built-in roles are Azure AD are Global Administrator and the Privileged Role Administrator as users assigned to these two roles can delegate administrator roles:
- Global Administrator / Company Administrator: Users with this role have access to all administrative features in Azure AD, as well as services that use Azure AD identities.
- Privileged Role Administrator: Users with this role can manage role assignments in Azure AD, as well as within Azure AD Privileged Identity Management (PIM). In addition, this role allows management of all aspects of PIM and administrative units.

Note: You may have other critical roles that need to be governed if you use custom roles with certain privileged permissions assigned. And you may also want to apply similar controls to the administrator account of critical business assets.

You can enable just-in-time (JIT) privileged access to Azure resources and Azure AD using Azure AD Privileged Identity Management (PIM). JIT grants temporary permissions to perform privileged tasks only when users need it. PIM can also generate security alerts when there is suspicious or unsafe activity in your Azure AD organization.

- [Administrator role permissions in Azure AD](/azure/active-directory/users-groups-roles/directory-assign-admin-roles)

- [Use Azure Privileged Identity Management security alerts](../../active-directory/privileged-identity-management/pim-how-to-configure-security-alerts.md)

- [Securing privileged access for hybrid and cloud deployments in Azure AD](/azure/active-directory/users-groups-roles/directory-admin-roles-secure)

**Azure Security Center monitoring**: Yes

**Responsibility**: Customer

### PA-2: Restrict administrative access to business-critical systems

**Guidance**: Azure Managed Applications uses Azure RBAC to isolate access to business-critical systems by restricting which accounts are granted privileged access to the subscriptions and management groups they are in.

Ensure that you also restrict access to the management, identity, and security systems that have administrative access to your business critical access such as Active Directory Domain Controllers (DCs), security tools, and system management tools with agents installed on business critical systems. Attackers who compromise these management and security systems can immediately weaponize them to compromise business critical assets.

All types of access controls should be aligned to your enterprise segmentation strategy to ensure consistent access control.

- [Azure Components and Reference model](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Management Group Access](../../governance/management-groups/overview.md#management-group-access)

- [Azure subscription administrators](../../cost-management-billing/manage/add-change-subscription-administrator.md)

**Azure Security Center monitoring**: Yes

**Responsibility**: Customer

### PA-3: Review and reconcile user access regularly

**Guidance**: Review user accounts and access assignment regularly to ensure the accounts and their level of access are valid.

Azure Managed Applications uses Azure Active Directory (AAD) accounts to manage its resources, review user accounts and access assignment regularly to ensure the accounts and their access are valid. You can use Azure AD access reviews to review group memberships, access to enterprise applications, and role assignments. Azure AD reporting can provide logs to help discover stale accounts. You can also use Azure AD Privileged Identity Management to create access review report workflow to facilitate the review process.

In addition, Azure Privileged Identity Management can also be configured to alert when an excessive number of administrator accounts are created, and to identify administrator accounts that are stale or improperly configured.

Note: Some Azure services support local users and roles which not managed through Azure AD. You will need to manage these users separately.

- [Create an access review of Azure resource roles in Privileged Identity Management(PIM)](../../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md) 

- [How to use Azure AD identity and access reviews](../../active-directory/governance/access-reviews-overview.md)

**Azure Security Center monitoring**: Yes

**Responsibility**: Customer

### PA-4: Set up emergency access in Azure AD

**Guidance**: Azure Managed Applications uses Azure Active Directory to manage its resources. To prevent being accidentally locked out of your Azure AD organization, set up an emergency access account for access when normal administrative accounts cannot be used. Emergency access accounts are usually highly privileged, and they should not be assigned to specific individuals. Emergency access accounts are limited to emergency or "break glass"' scenarios where normal administrative accounts can't be used.

You should ensure that the credentials (such as password, certificate, or smart card) for emergency access accounts are kept secure and known only to individuals who are authorized to use them only in an emergency.

- [Manage emergency access accounts in Azure AD](../../active-directory/roles/security-emergency-access.md)

**Azure Security Center monitoring**: Yes

**Responsibility**: Customer

### PA-5: Automate entitlement management 

**Guidance**: Azure Managed Applications is integrated with Azure Active Directory to manage its resources. Use Azure AD entitlement management features to automate access request workflows, including access assignments, reviews, and expiration. Dual or multi-stage approval is also supported.

- [What are Azure AD access reviews](../../active-directory/governance/access-reviews-overview.md)

- [What is Azure AD entitlement management](../../active-directory/governance/entitlement-management-overview.md)

**Azure Security Center monitoring**: Yes

**Responsibility**: Customer

### PA-6: Use privileged access workstations

**Guidance**: Secured, isolated workstations are critically important for the security of sensitive roles like administrators, developers, and critical service operators. Use highly secured user workstations and/or Azure Bastion for administrative tasks related to your Managed Applications. Use Azure Active Directory, Microsoft Defender Advanced Threat Protection (ATP), and/or Microsoft Intune to deploy a secure and managed user workstation for administrative tasks. The secured workstations can be centrally managed to enforce secured configuration including strong authentication, software and hardware baselines, restricted logical and network access.

- [Understand privileged access workstations](../../active-directory/devices/concept-azure-managed-workstation.md)

- [Deploy a privileged access workstation](../../active-directory/devices/howto-azure-managed-workstation.md)

**Azure Security Center monitoring**: Yes

**Responsibility**: Customer

### PA-7: Follow just enough administration (least privilege principle) 

**Guidance**: Azure Managed Applications is integrated with Azure role-based access control (RBAC) to manage its resources. Azure RBAC allows you to manage Azure resource access through role assignments. You can assign these roles to users, groups service principals and managed identities. There are pre-defined built-in roles for certain resources, and these roles can be inventoried or queried through tools such as Azure CLI, Azure PowerShell or the Azure portal. The privileges you assign to resources through the Azure RBAC should be always limited to what is required by the roles. This complements the just in time (JIT) approach of Azure AD Privileged Identity Management (PIM) and should be reviewed periodically.

Where possible, use built-in roles to allocate permission and only create custom role when required.

Azure provides the following Azure built-in roles for authorizing access to Managed Applications using Azure AD and OAuth:

- Managed Application Contributor Role: Allows for creating managed application resources.

- Managed Application Operator Role: Lets you read and perform actions on Managed Application resources

- Managed Applications Reader: Lets you read resources in a managed app and request JIT access.

For more information, see the following references:

- [Managed application contributor role](../../role-based-access-control/built-in-roles.md#managed-application-contributor-role)

- [Managed application operator role](../../role-based-access-control/built-in-roles.md#managed-application-contributor-role)

- [Managed applications reader](../../role-based-access-control/built-in-roles.md#managed-applications-reader)

- [What is Azure role-based access control (Azure RBAC)](../../role-based-access-control/overview.md)

**Azure Security Center monitoring**: Yes

**Responsibility**: Customer

### PA-8: Choose approval process for Microsoft support  

**Guidance**: Implement an organizational approval process for support scenarios where Microsoft may need access to your Azure Managed Application data. Customer Lockbox is not currently available for Managed Application scenarios.

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Customer

## Data Protection

*For more information, see the [Azure Security Benchmark: Data Protection](/azure/security/benchmarks/security-controls-v2-data-protection).*

### DP-2: Protect sensitive data

**Guidance**: For bringing encryption with your own keys, you can utilize your own storage account for the storage of the Managed Application configuration files.

- [Data protection of Managed configuration files using your own storage](https://docs.microsoft.com/azure/azure-resource-manager/managed-applications/publish-service-catalog-app?tabs=azure-powershell#bring-your-own-storage-for-the-managed-application-definition)

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Customer

### DP-5: Encrypt sensitive data at rest

**Guidance**: Managed application definitions which define your application and its resources can be stored in your own storage accounts which can be configured with customer-managed encryption keys.

For scenarios where you do not want to bring your own storage for Managed Application definitions, Azure provides data at rest encryption by default.

- [Bring your own storage for Managed Application definitions](https://docs.microsoft.com/azure/azure-resource-manager/managed-applications/publish-service-catalog-app?tabs=azure-powershell#bring-your-own-storage-for-the-managed-application-definition)

- [Understand encryption at rest in Azure](../../security/fundamentals/encryption-atrest.md#encryption-at-rest-in-microsoft-cloud-services) 

- [How to configure customer-managed encryption keys](/azure/storage/common/storage-encryption-keys-portal)

**Azure Security Center monitoring**: Currently not available

**Responsibility**: Shared

## Asset Management

*For more information, see the [Azure Security Benchmark: Asset Management](/azure/security/benchmarks/security-controls-v2-asset-management).*

### AM-1: Ensure security team has visibility into risks for assets

**Guidance**: Ensure security teams are granted Security Reader permissions in your Azure tenant and subscriptions so they can monitor for security risks using Azure Security Center. 

Depending on how security team responsibilities are structured, monitoring for security risks could  be the responsibility of a central security team or a local team. That said, security insights and risks must always be aggregated centrally within an organization. 

Security Reader permissions can be applied broadly to an entire tenant (Root Management Group) or scoped to management groups or specific subscriptions. 

Note: Additional permissions might be required to get visibility into workloads and services. 

- [Overview of Security Reader Role](../../role-based-access-control/built-in-roles.md#security-reader)

- [Overview of Azure Management Groups](../../governance/management-groups/overview.md)

**Azure Security Center monitoring**: Currently not available

**Responsibility**: Customer

### AM-2: Ensure security team has access to asset inventory and metadata

**Guidance**: Apply tags to your Azure resources, resource groups, and subscriptions to logically organize them into a taxonomy. Each tag consists of a name and a value pair. For example, you can apply the name "Environment" and the value "Production" to all the resources in production.

Tags that are provided at creation time of the managed app are applied to the Managed Resource Group as well. The publisher of your application can provide their own additional tagging of the managed resources after deployment.

- [Tags by resource Managed Applicaiton UI element](microsoft-common-tagsbyresource.md)

- [How to create queries with Azure Resource Graph Explorer](../../governance/resource-graph/first-query-portal.md) 

- [For more information about tagging assets, see the resource naming and tagging decision guide](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)

**Azure Security Center monitoring**: Yes

**Responsibility**: Customer

### AM-3: Use only approved Azure services

**Guidance**: Azure Managed Applications supports Azure Resource Manager based deployments and configuration enforcement using Azure Policy. Use Azure Policy to audit and restrict which services users can provision in your environment. Use Azure Resource Graph to query for and discover resources within their subscriptions. You can also use Azure Monitor to create rules to trigger alerts when a non-approved service is detected.

- [How to configure and manage Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

- [How to deny a specific resource type with Azure Policy](../../governance/policy/samples/built-in-policies.md#general)

- [How to create queries with Azure Resource Graph Explorer](../../governance/resource-graph/first-query-portal.md)

**Azure Security Center monitoring**: Yes

**Responsibility**: Customer

### AM-4: Ensure security of asset lifecycle management

**Guidance**: Managed Application resources and their connected managed resource group can be deleted by deleting the managed application resource. When a managed application resource is deleted, it will also delete the managed resource group and its contents. Additional lifecycle capabilities are specified by the publisher of the application, where they can give the consumer additional rights over the lifecycle of the underlying managed resources through allowed actions. Please consult with the publisher of your managed application for what resources are managed by the consumer.

- [Clean up Managed Application resources](https://docs.microsoft.com/azure/azure-resource-manager/managed-applications/tutorial-create-managed-app-with-custom-provider?tabs=azurecli-interactive#clean-up-resources)

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Customer

### AM-5: Limit users' ability to interact with Azure Resource Manager

**Guidance**: Use Azure Conditional Access to limit users' ability to interact with Azure Resources Manager by configuring "Block access" for the "Microsoft Azure Management" App.

- [How to configure Conditional Access to block access to Azure Resources Manager](../../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center monitoring**: Yes

**Responsibility**: Customer

## Logging and Threat Detection

*For more information, see the [Azure Security Benchmark: Logging and Threat Detection](/azure/security/benchmarks/security-controls-v2-logging-threat-protection).*

### LT-2: Enable threat detection for Azure identity and access management

**Guidance**: Azure AD provides the following user logs that can be viewed in Azure AD reporting or integrated with Azure Monitor, Azure Sentinel or other SIEM/monitoring tools for more sophisticated monitoring and analytics use cases:
Sign-ins – The sign-ins report provides information about the usage of managed applications and user sign-in activities.
Audit logs - Provides traceability through logs for all changes done by various features within Azure AD. Examples of audit logs include changes made to any resources within Azure AD like adding or removing users, apps, groups, roles and policies.
Risky sign-ins - A risky sign-in is an indicator for a sign-in attempt that might have been performed by someone who is not the legitimate owner of a user account.
Users flagged for risk - A risky user is an indicator for a user account that might have been compromised.

Azure Security Center can also alert on certain suspicious activities such as excessive number of failed authentication attempts, deprecated accounts in the subscription. In addition to the basic security hygiene monitoring, Azure Security Center’s Threat Protection module can also collect more in-depth security alerts from individual Azure compute resources (virtual machines, containers, app service), data resources (SQL DB and storage), and Azure service layers. This capability gives you visibility on account anomalies inside the individual resources.

- [Audit activity reports in the Azure Active Directory](../../active-directory/reports-monitoring/concept-audit-logs.md)

- [Enable Azure Identity Protection](../../active-directory/identity-protection/overview-identity-protection.md)

- [Threat protection in Azure Security Center](/azure/security-center/threat-protection)

**Azure Security Center monitoring**: Yes

**Responsibility**: Customer

### LT-4: Enable logging for Azure resources

**Guidance**: Activity logs, which are automatically available, contain all write operations (PUT, POST, DELETE) for your managed application resources except read operations (GET). Activity logs can be used to find an error when troubleshooting or to monitor how a user in your organization modified a resource.

- [How to collect platform logs and metrics with Azure Monitor](../../azure-monitor/platform/diagnostic-settings.md) 

- [Understand logging and different log types in Azure](../../azure-monitor/platform/platform-logs-overview.md)

**Azure Security Center monitoring**: Yes

**Responsibility**: Customer

### LT-5: Centralize security log management and analysis

**Guidance**: Centralize logging, storage, and analysis to enable correlation. For each log source, ensure you have assigned a data owner, access guidance, storage location, what tools are used to process and access the data, and data retention requirements.
Ensure you are integrating Azure activity logs into your central logging. Ingest logs via Azure Monitor to aggregate security data generated by endpoint devices, network resources, and other security systems. In Azure Monitor, use Log Analytics workspaces to query and perform analytics, and use Azure Storage accounts for long term and archival storage.

In addition, enable and onboard data to Azure Sentinel or a third-party SIEM.

Many organizations choose to use Azure Sentinel for “hot” data that is used frequently and Azure Storage for “cold” data that is used less frequently.

- [How to collect platform logs and metrics with Azure Monitor](../../azure-monitor/platform/diagnostic-settings.md)

- [How to onboard Azure Sentinel](../../sentinel/quickstart-onboard.md)

**Azure Security Center monitoring**: Yes

**Responsibility**: Customer

### LT-6: Configure log storage retention

**Guidance**: Ensure that any storage accounts or Log Analytics workspaces used for storing logs created by your managed application resources has the log retention period set according to your organization's compliance regulations.
In Azure Monitor, you can set your Log Analytics workspace retention period according to your organization's compliance regulations. Use Azure Storage, Data Lake or Log Analytics workspace accounts for long-term and archival storage.

- [How to configure Log Analytics Workspace Retention Period](../../azure-monitor/platform/manage-cost-storage.md)

- [Storing resource logs in an Azure Storage Account](/azure/azure-monitor/platform/resource-logs-collect-storage)

**Azure Security Center monitoring**: Yes

**Responsibility**: Customer

## Incident Response

*For more information, see the [Azure Security Benchmark: Incident Response](/azure/security/benchmarks/security-controls-v2-incident-response).*

### IR-1: Preparation – update incident response process for Azure

**Guidance**: Ensure your organization has processes to respond to security incidents, has updated these processes for Azure, and is regularly exercising them to ensure readiness.

- [Implement security across the enterprise environment](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Incident response reference guide](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Customer

### IR-2: Preparation – setup incident notification

**Guidance**: Set up security incident contact information in Azure Security Center. This contact information is used by Microsoft to contact you if the Microsoft Security Response Center (MSRC) discovers that your data has been accessed by an unlawful or unauthorized party. You also have options to customize incident alert and notification in different Azure services based on your incident response needs. 

- [How to set the Azure Security Center security contact](../../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center monitoring**: Yes

**Responsibility**: Customer

### IR-3: Detection and analysis – create incidents based on high quality alerts

**Guidance**: Ensure you have a process to create high-quality alerts and measure the quality of alerts. This allows you to learn lessons from past incidents and prioritize alerts for analysts, so they don’t waste time on false positives. 

High-quality alerts can be built based on experience from past incidents, validated community sources, and tools designed to generate and clean up alerts by fusing and correlating diverse signal sources. 

Azure Security Center provides high-quality alerts across many Azure assets. You can use the ASC data connector to stream the alerts to Azure Sentinel. Azure Sentinel lets you create advanced alert rules to generate incidents automatically for an investigation. 

Export your Azure Security Center alerts and recommendations using the export feature to help identify risks to Azure resources. Export alerts and recommendations either manually or in an ongoing, continuous fashion.

- [How to configure export](../../security-center/continuous-export.md)

- [How to stream alerts into Azure Sentinel](../../sentinel/connect-azure-security-center.md)

**Azure Security Center monitoring**: Currently not available

**Responsibility**: Customer

### IR-4: Detection and analysis – investigate an incident

**Guidance**: Ensure analysts can query and use diverse data sources as they investigate potential incidents, to build a full view of what happened. Diverse logs should be collected to track the activities of a potential attacker across the kill chain to avoid blind spots.  You should also ensure insights and learnings are captured for other analysts and for future historical reference.  

The data sources for investigation include the centralized logging sources that are already being collected from the in-scope services and running systems, but can also include:

- Network data – use network security groups' flow logs, Azure Network Watcher, and Azure Monitor to capture network flow logs and other analytics information. 

- Snapshots of running systems: 

    - Use Azure virtual machine's snapshot capability to create a snapshot of the running system's disk. 

    - Use the operating system's native memory dump capability to create a snapshot of the running system's memory.

    - Use the snapshot feature of the Azure services or your software's own capability to create snapshots of the running systems.

Azure Sentinel provides extensive data analytics across virtually any log source and a case management portal to manage the full lifecycle of incidents. Intelligence information during an investigation can be associated with an incident for tracking and reporting purposes. 

- [Snapshot a Windows machine's disk](../../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Snapshot a Linux machine's disk](../../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Microsoft Azure Support diagnostic information and memory dump collection](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Investigate incidents with Azure Sentinel](../../sentinel/tutorial-investigate-cases.md)

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Customer

### IR-5: Detection and analysis – prioritize incidents

**Guidance**: Provide context to analysts on which incidents to focus on first based on alert severity and asset sensitivity. 

Azure Security Center assigns a severity to each alert to help you prioritize which alerts should be investigated first. The severity is based on how confident Security Center is in the finding or the analytics used to issue the alert, as well as the confidence level that there was malicious intent behind the activity that led to the alert.

Additionally, mark resources using tags and create a naming system to identify and categorize Azure resources, especially those processing sensitive data. It is your responsibility to prioritize the remediation of alerts based on the criticality of the Azure resources and environment where the incident occurred.

- [Security alerts in Azure Security Center](../../security-center/security-center-alerts-overview.md)

- [Use tags to organize your Azure resources](/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center monitoring**: Currently not available

**Responsibility**: Customer

### IR-6: Containment, eradication and recovery – automate the incident handling

**Guidance**: Automate manual repetitive tasks to speed up response time and reduce the burden on analysts. Manual tasks take longer to execute, slowing each incident and reducing how many incidents an analyst can handle. Manual tasks also increase analyst fatigue, which increases the risk of human error that causes delays, and degrades the ability of analysts to focus effectively on complex tasks. 

Use workflow automation features in Azure Security Center and Azure Sentinel to automatically trigger actions or run a playbook to respond to incoming security alerts. The playbook takes actions, such as sending notifications, disabling accounts, and isolating problematic networks. 

- [Configure workflow automation in Security Center](../../security-center/workflow-automation.md)

- [Set up automated threat responses in Azure Security Center](../../security-center/tutorial-security-incident.md#triage-security-alerts)

- [Set up automated threat responses in Azure Sentinel](../../sentinel/tutorial-respond-threats-playbook.md)

**Azure Security Center monitoring**: Currently not available

**Responsibility**: Customer

## Posture and Vulnerability Management

*For more information, see the [Azure Security Benchmark: Posture and Vulnerability Management](/azure/security/benchmarks/security-controls-v2-vulnerability-management).*

### PV-1: Establish secure configurations for Azure services 

**Guidance**: Define security guardrails for infrastructure and DevOps teams by making it easy to securely configure the Azure services they use.

Configure Azure Policy to audit and enforce configurations of your resources related to your Managed Applications deployments.

You can use Azure Blueprints to automate deployment and configuration of services and application environments, including Azure Resource Manager templates, Azure RBAC assignments, and Azure Policy assignments, in a single blueprint definition.

- [Cloud Adoption Framework enterprise-scale landing zone](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture#landing-zone-expanded-definition)

- [Create and manage policies to enforce compliance](../../governance/policy/tutorials/create-and-manage.md)

- [Azure Blueprints](../../governance/blueprints/overview.md)

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Customer

### PV-2: Sustain secure configurations for Azure services

**Guidance**: Use Azure Security Center to monitor your resources related to Azure Managed Applications and use Azure Policy [deny] and [deploy if not exist] effects to sustain secure configurations.
- [Understand Azure Policy effects](../../governance/policy/concepts/effects.md)

- [Create and manage policies to enforce compliance](../../governance/policy/tutorials/create-and-manage.md)

- [Azure Blueprints](../../governance/blueprints/overview.md)

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Customer

### PV-8: Conduct regular attack simulation

**Guidance**: As required, conduct penetration testing or red team activities on your Azure resources and ensure remediation of all critical security findings.
Follow the Microsoft Cloud Penetration Testing Rules of Engagement to ensure your penetration tests are not in violation of Microsoft policies. Use Microsoft's strategy and execution of Red Teaming and live site penetration testing against Microsoft-managed cloud infrastructure, services, and applications.

- [Penetration testing in Azure](../../security/fundamentals/pen-testing.md)

- [Penetration Testing Rules of Engagement](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Shared

## Backup and Recovery

*For more information, see the [Azure Security Benchmark: Backup and Recovery](/azure/security/benchmarks/security-controls-v2-backup-recovery).*

### BR-3: Validate all backups including customer-managed keys

**Guidance**: When storing your Managed Application definitions in your own storage account, ensure that you can restore any associated customer-managed keys that are used for that account's encryption which are stored in Azure Key Vault.

- [Bring your own storage for Managed Application definitions](https://docs.microsoft.com/azure/azure-resource-manager/managed-applications/publish-service-catalog-app?tabs=azure-powershell#bring-your-own-storage-for-the-managed-application-definition)

- [How to restore Key Vault keys in Azure](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultkey?view=azps-5.1.0&amp;preserve-view=true)

**Azure Security Center monitoring**: Currently not available

**Responsibility**: Customer

### BR-4: Mitigate risk of lost keys

**Guidance**: If you are bringing your own storage for your Managed Application definitions, ensure you have measures in place to prevent and recover from loss of keys used to encrypt your definitions. Enable soft delete and purge protection on the Azure Key Vault which stores your customer-managed keys to protect keys against accidental or malicious deletion.  

- [Bring your own storage for Managed Application definitions](https://docs.microsoft.com/azure/azure-resource-manager/managed-applications/publish-service-catalog-app?tabs=azure-powershell#bring-your-own-storage-for-the-managed-application-definition)

- [How to enable soft delete and purge protection in Key Vault](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**Azure Security Center monitoring**: Yes

**Responsibility**: Customer

## Governance and Strategy

*For more information, see the [Azure Security Benchmark: Governance and Strategy](/azure/security/benchmarks/security-controls-v2-governance-strategy).*

### GS-1: Define asset management and data protection strategy 

**Guidance**: Ensure you document and communicate a clear strategy for continuous monitoring and protection of systems and data. Prioritize discovery, assessment, protection, and monitoring of business-critical data and systems. 

This strategy should include documented guidance, policy, and standards for the following elements: 

-	Data classification standard in accordance with the business risks

-	Security organization visibility into risks and asset inventory 

-	Security organization approval of Azure services for use 

-	Security of assets through their lifecycle

-	Required access control strategy in accordance with organizational data classification

-	Use of Azure native and third-party data protection capabilities

-	Data encryption requirements for in-transit and at-rest use cases

-	Appropriate cryptographic standards

For more information, see the following references:
- [Azure Security Architecture Recommendation - Storage, data, and encryption](https://docs.microsoft.com/azure/architecture/framework/security/storage-data-encryption?toc=/security/compass/toc.json&amp;bc=/security/compass/breadcrumb/toc.json)

- [Azure Security Fundamentals - Azure Data security, encryption, and storage](../../security/fundamentals/encryption-overview.md)

- [Cloud Adoption Framework - Azure data security and encryption best practices](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices?toc=/azure/cloud-adoption-framework/toc.json&amp;bc=/azure/cloud-adoption-framework/_bread/toc.json)

- [Azure Security Benchmark - Asset management](/azure/security/benchmarks/security-benchmark-v2-asset-management)

- [Azure Security Benchmark - Data Protection](/azure/security/benchmarks/security-benchmark-v2-data-protection)

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Customer

### GS-2: Define enterprise segmentation strategy 

**Guidance**: Establish an enterprise-wide strategy to segmenting access to assets using a combination of identity, network, application, subscription, management group, and other controls.

Carefully balance the need for security separation with the need to enable daily operation of the systems that need to communicate with each other and access data.

Ensure that the segmentation strategy is implemented consistently across control types including network security, identity and access models, and application permission/access models, and human process controls.

- [Guidance on segmentation strategy in Azure (video)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Guidance on segmentation strategy in Azure (document)](/security/compass/governance#enterprise-segmentation-strategy)

- [Align network segmentation with enterprise segmentation strategy](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Customer

### GS-3: Define security posture management strategy

**Guidance**: Continuously measure and mitigate risks to your individual assets and the environment they are hosted in. Prioritize high value assets and highly-exposed attack surfaces, such as published applications, network ingress and egress points, user and administrator endpoints, etc.

- [Azure Security Benchmark - Posture and vulnerability management](/azure/security/benchmarks/security-benchmark-v2-posture-vulnerability-management)

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Customer

### GS-4: Align organization roles, responsibilities, and accountabilities

**Guidance**: Ensure you document and communicate a clear strategy for roles and responsibilities in your security organization. Prioritize providing clear accountability for security decisions, educating everyone on the shared responsibility model, and educate technical teams on technology to secure the cloud.

- [Azure Security Best Practice 1 – People: Educate Teams on Cloud Security Journey](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Azure Security Best Practice 2 - People: Educate Teams on Cloud Security Technology](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Azure Security Best Practice 3 - Process: Assign Accountability for Cloud Security Decisions](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Customer

### GS-5: Define network security strategy

**Guidance**: Establish an Azure network security approach as part of your organization’s overall security access control strategy.  

This strategy should include documented guidance, policy, and standards for the following elements: 

-	Centralized network management and security responsibility

-	Virtual network segmentation model aligned with the enterprise segmentation strategy

-	Remediation strategy in different threat and attack scenarios

-	Internet edge and ingress and egress strategy

-	Hybrid cloud and on-premises interconnectivity strategy

-	Up-to-date network security artifacts (e.g. network diagrams, reference network architecture)

For more information, see the following references:
- [Azure Security Best Practice 11 - Architecture. Single unified security strategy](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure Security Benchmark - Network Security](/azure/security/benchmarks/security-benchmark-v2-network-security)

- [Azure network security overview](../../security/fundamentals/network-overview.md)

- [Enterprise network architecture strategy](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Customer

### GS-6: Define identity and privileged access strategy

**Guidance**: Establish an Azure identity and privileged access approaches as part of your organization’s overall security access control strategy.  

This strategy should include documented guidance, policy, and standards for the following elements: 

-	A centralized identity and authentication system and its interconnectivity with other internal and external identity systems

-	Strong authentication methods in different use cases and conditions

-	Protection of highly privileged users

-	Anomaly user activities monitoring and handling  

-	User identity and access review and reconciliation process

For more information, see the following references:

- [Azure Security Benchmark - Identity management](/azure/security/benchmarks/security-benchmark-v2-identity-management)

- [Azure Security Benchmark - Privileged access](/azure/security/benchmarks/security-benchmark-v2-privileged-access)

- [Azure Security Best Practice 11 - Architecture. Single unified security strategy](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure identity management security overview](../../security/fundamentals/identity-management-overview.md)

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Customer

### GS-7: Define logging and threat response strategy

**Guidance**: Establish a logging and threat response strategy to rapidly detect and remediate threats while meeting compliance requirements. Prioritize providing analysts with high-quality alerts and seamless experiences so that they can focus on threats rather than integration and manual steps. 

This strategy should include documented guidance, policy, and standards for the following elements: 

-	The security operations (SecOps) organization’s role and responsibilities 

-	A well-defined incident response process aligning with NIST or another industry framework 

-	Log capture and retention to support threat detection, incident response, and compliance needs

-	Centralized visibility of and correlation information about threats, using SIEM, native Azure capabilities, and other sources 

-	Communication and notification plan with your customers, suppliers, and public parties of interest

-	Use of Azure native and third-party platforms for incident handling, such as logging and threat detection, forensics, and attack remediation and eradication

-	Processes for handling incidents and post-incident activities, such as lessons learned and evidence retention

For more information, see the following references:

- [Azure Security Benchmark - Logging and threat detection](/azure/security/benchmarks/security-benchmark-v2-logging-threat-detection)

- [Azure Security Benchmark - Incident response](/azure/security/benchmarks/security-benchmark-v2-incident-response)

- [Azure Security Best Practice 4 - Process. Update Incident Response Processes for Cloud](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Azure Adoption Framework, logging, and reporting decision guide](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Azure enterprise scale, management, and monitoring](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Customer

## Next steps

- See the [Azure Security Benchmark V2 overview](/azure/security/benchmarks/overview)
- Learn more about [Azure security baselines](/azure/security/benchmarks/security-baselines-overview)
