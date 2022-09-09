---
title: Azure Security and Compliance Blueprint - IaaS Web Application for PCI DSS
description: Azure Security and Compliance Blueprint - IaaS Web Application for PCI DSS
services: security
author: meladie

ms.assetid: 9c825380-2abe-4fdd-800c-59d1fed1780b
ms.service: security
ms.topic: article
ms.date: 07/03/2018
ms.author: meladie
---
# Azure Security and Compliance Blueprint: IaaS Web Application for PCI DSS

## Overview

This Azure Security and Compliance Blueprint provides guidance for the deployment of a Payment Card Industry Data Security Standards (PCI DSS 3.2) compliant infrastructure as a service (IaaS) environment suitable for the collection, storage, and retrieval of cardholder data. It showcases a common reference architecture and demonstrates the proper handling of credit card data (including card number, expiration, and verification data) in a secure, compliant, multi-tier environment. This blueprint illustrates an end-to-end solution to meet the needs of organizations seeking a cloud-based approach to reducing the burden and cost of deployment.

This reference architecture, implementation guide, and threat model provide a foundation for customers to comply with PCI DSS 3.2 requirements. This solution provides a baseline to help customers deploy workloads to Azure in a PCI DSS 3.2 compliant manner; however, this solution should not be used as-is in a production environment because additional configuration is required.

Achieving PCI DSS-compliance requires that an accredited Qualified Security Assessor (QSA) certify a production customer solution. Customers are responsible for conducting appropriate security and compliance assessments of any solution built using this architecture, as requirements may vary based on the specifics of each customer's implementation.

## Architecture diagram and components

This solution deploys a reference architecture for an IaaS web application with a SQL Server backend. The architecture includes a web tier, data tier, Active Directory infrastructure, Application Gateway, and Load Balancer. Virtual machines deployed to the web and data tiers are configured in an availability set, and SQL Server instances are configured in an Always On availability group for high availability. Virtual machines are domain-joined, and Active Directory group policies are used to enforce security and compliance configurations at the operating system level. A management bastion host provides a secure connection for administrators to access deployed resources. **Azure recommends configuring a VPN or ExpressRoute connection for management and data import into the reference architecture subnet.**

![IaaS Web Application for PCI DSS reference architecture diagram](images/pcidss-iaaswa-architecture.png "IaaS Web Application for PCI DSS reference architecture diagram")

This solution uses the following Azure services. Details of the deployment architecture are located in the [deployment architecture](#deployment-architecture) section.

- Availability Sets
	- (1) Active Directory domain controllers
	- (1) SQL cluster nodes
	- (1) Web/IIS
- Azure Active Directory
- Azure Application Gateway
	- (1) Web Application Firewall
		- Firewall mode: prevention
		- Rule set: OWASP 3.0
		- Listener port: 443
- Azure Key Vault
- Azure Load Balancer
- Azure Monitor
- Azure Resource Manager
- Azure Security Center
- Azure Storage
	- (7) Geo-redundant storage accounts
- Azure Virtual Machines
	- (1) management/bastion (Windows Server 2016 Datacenter)
	- (2) Active Directory domain controller (Windows Server 2016 Datacenter)
	- (2) SQL Server cluster node (SQL Server 2017 on Windows Server 2016)
	- (2) Web/IIS (Windows Server 2016 Datacenter)
- Azure Virtual Network
	- (1) /16 Network
	- (5) /24 Networks
	- (5) Network Security Groups
- Cloud Witness
- Recovery Services Vault

## Deployment architecture

The following section details the deployment and implementation elements.

**Bastion host**: The bastion host is the single point of entry that allows users to access the deployed resources in this environment. The bastion host provides a secure connection to deployed resources by only allowing remote traffic from public IP addresses on a safe list. To permit remote desktop (RDP) traffic, the source of the traffic needs to be defined in the Network Security Group.

This solution creates a virtual machine as a domain-joined bastion host with the following configurations:
-	[Antimalware extension](https://docs.microsoft.com/azure/security/fundamentals/antimalware)
-	[Azure Diagnostics extension](../../virtual-machines/windows/extensions-diagnostics-template.md)
-	[Azure Disk Encryption](../azure-security-disk-encryption-overview.md) using Azure Key Vault
-	An [auto-shutdown policy](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) to reduce consumption of virtual machine resources when not in use
-	[Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) enabled so that credentials and other secrets run in a protected environment that is isolated from the running operating system

### Virtual network

The architecture defines a private virtual network with an address space of 10.200.0.0/16.

**Network security groups**: This solution deploys resources in an architecture with a separate web subnet, database subnet, Active Directory subnet, and management subnet inside of a virtual network. Subnets are logically separated by network security group rules applied to the individual subnets to restrict traffic between subnets to only that necessary for system and management functionality.

See the configuration for [Network Security Groups](https://github.com/Azure/fedramp-iaas-webapp/blob/master/nestedtemplates/virtualNetworkNSG.json) deployed with this solution. Organizations can configure Network Security Groups by editing the file above using [this documentation](../../virtual-network/virtual-network-vnet-plan-design-arm.md) as a guide.

Each of the subnets has a dedicated Network Security Group:
- 1 Network Security Group for Application Gateway (LBNSG)
- 1 Network Security Group for bastion host (MGTNSG)
- 1 Network Security Group for primary and backup domain controllers (ADNSG)
- 1 Network Security Group for SQL Servers and Cloud Witness (SQLNSG)
- 1 Network Security Group for web tier (WEBNSG)

### Data in transit

Azure encrypts all communications to and from Azure datacenters by default. Additionally, all transactions to Azure Storage through the Azure portal occur via HTTPS.

### Data at rest

The architecture protects data at rest through multiple measures, including encryption and database auditing.

**Azure Storage**: To meet encrypted data at rest requirements, all [Azure Storage](https://azure.microsoft.com/services/storage/) uses [Storage Service Encryption](../../storage/common/storage-service-encryption.md). This helps protect and safeguard cardholder data in support of organizational security commitments and compliance requirements defined by PCI DSS 3.2.

**Azure Disk Encryption**: Azure Disk Encryption is used to encrypted Windows IaaS virtual machine disks. [Azure Disk Encryption](../azure-security-disk-encryption-overview.md) leverages the BitLocker feature of Windows to provide volume encryption for OS and data disks. The solution is integrated with Azure Key Vault to help control and manage the disk-encryption keys.

**SQL Server**: The SQL Server instance uses the following database security measures:
- [Active Directory authentication and authorization](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) enables identity management of database users and other Microsoft services in one central location.
-	[SQL database auditing](../../sql-database/sql-database-auditing.md) tracks database events and writes them to an audit log in an Azure storage account.
-	SQL Database is configured to use [Transparent Data Encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), which performs real-time encryption and decryption of the database, associated backups, and transaction log files to protect information at rest. Transparent Data Encryption provides assurance that stored cardholder data has not been subject to unauthorized access.
-	[Firewall rules](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) prevent all access to database servers until proper permissions are granted. The firewall grants access to databases based on the originating IP address of each request.
-	[SQL Threat Detection](../../sql-database/sql-database-threat-detection.md) enables the detection and response to potential threats as they occur by providing security alerts for suspicious database activities, potential vulnerabilities, SQL injection attacks, and anomalous database access patterns.
-	[Always Encrypted Columns](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) ensure that sensitive cardholder data never appears as plaintext inside the database system. After enabling data encryption, only client applications or application servers with access to the keys can access plaintext data.
- The [Extended Properties](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addextendedproperty-transact-sql) feature can be used to discontinue the processing of data subjects, as it allows users to add custom properties to database objects and tag data as "Discontinued" to support application logic to prevent the processing of associated cardholder data.
- [Row-Level Security](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) enables users to define policies to restrict access to data to discontinue processing.
- [SQL Database dynamic data masking](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) limits sensitive cardholder data exposure by masking the data to non-privileged users or applications. Dynamic data masking can automatically discover potentially sensitive data and suggest the appropriate masks to be applied. This helps to identify and reduce access to cardholder data such that it does not exit the database via unauthorized access. Customers are responsible for adjusting dynamic data masking settings to adhere to their database schema.

### Identity management

The following technologies provide capabilities to manage access to data in the Azure environment:

- [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) is Microsoft&#39;s multi-tenant cloud-based directory and identity management service. All users for this solution are created in Azure Active Directory, including users accessing the Azure SQL Database.
- Authentication to the application is performed using Azure Active Directory. For more information, see [integrating applications with Azure Active Directory](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md). Additionally, the database column encryption uses Azure Active Directory to authenticate the application to Azure SQL Database. For more information, see how to [protect sensitive data in Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
- [Azure role-based access control](../../role-based-access-control/role-assignments-portal.md) enables administrators to define fine-grained access permissions to grant only the amount of access that users need to perform their jobs. Instead of giving every user unrestricted permission for Azure resources, administrators can allow only certain actions for accessing data. Subscription access is limited to the subscription administrator.
- [Azure Active Directory Privileged Identity Management](../../active-directory/privileged-identity-management/pim-getting-started.md) enables customers to minimize the number of users who have access to certain information. Administrators can use Azure Active Directory Privileged Identity Management to discover, restrict, and monitor privileged identities and their access to resources. This functionality can also be used to enforce on-demand, just-in-time administrative access when needed.
- [Azure Active Directory Identity Protection](../../active-directory/identity-protection/overview.md) detects potential vulnerabilities affecting an organization&#39;s identities, configures automated responses to detected suspicious actions related to an organization&#39;s identities, and investigates suspicious incidents to take appropriate action to resolve them.

### Security

**Secrets management**: The solution uses [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) for the management of keys and secrets. Azure Key Vault helps safeguard cryptographic keys and secrets used by cloud applications and services. The following Azure Key Vault capabilities help customers protect and access such data:

- Advanced access policies are configured on a need basis.
- Key Vault access policies are defined with minimum required permissions to keys and secrets.
- All keys and secrets in Key Vault have expiration dates.
- All keys in Key Vault are protected by specialized hardware security modules. The key type is an HSM Protected 2048-bit RSA Key.
- All users and identities are granted minimum required permissions using role-based access control.
- Diagnostics logs for Key Vault are enabled with a retention period of at least 365 days.
- Permitted cryptographic operations for keys are restricted to the ones required.
- The solution is integrated with Azure Key Vault to manage IaaS virtual machine disk-encryption keys and secrets.

**Patch management**: Windows virtual machines deployed as part of this reference architecture are configured by default to receive automatic updates from Windows Update Service. This solution also includes the [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) service through which updated deployments can be created to patch virtual machines when needed.

**Malware protection**: [Microsoft Antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware) for Virtual Machines provides real-time protection capability that helps identify and remove viruses, spyware, and other malicious software, with configurable alerts when known malicious or unwanted software attempts to install or run on protected virtual machines.

**Azure Security Center**: With [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro), customers can centrally apply and manage security policies across workloads, limit exposure to threats, and detect and respond to attacks. Additionally, Azure Security Center accesses existing configurations of Azure services to provide configuration and service recommendations to help improve security posture and protect data.

Azure Security Center uses a variety of detection capabilities to alert customers of potential attacks targeting their environments. These alerts contain valuable information about what triggered the alert, the resources targeted, and the source of the attack. Azure Security Center has a set of [predefined security alerts](https://docs.microsoft.com/azure/security-center/security-center-alerts-type), which are triggered when a threat, or suspicious activity takes place. [Custom alert rules](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) in Azure Security Center allow customers to define new security alerts based on data that is already collected from their environment.

Azure Security Center provides prioritized security alerts and incidents, making it simpler for customers to discover and address potential security issues. A [threat intelligence report](https://docs.microsoft.com/azure/security-center/security-center-threat-report) is generated for each detected threat to assist incident response teams in investigating and remediating threats.

**Azure Application Gateway**:
The architecture reduces the risk of security vulnerabilities using an Azure Application Gateway with a web application firewall configured, and the OWASP ruleset enabled. Additional capabilities include:

- [End-to-end-SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- Enable [SSL Offload](../../application-gateway/create-ssl-portal.md)
- Disable [TLS v1.0 and v1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Web application firewall](../../application-gateway/waf-overview.md) (prevention mode)
- [Prevention mode](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) with OWASP 3.0 ruleset
- Enable [diagnostics logging](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)
- [Custom health probes](../../application-gateway/quick-create-portal.md)
- [Azure Security Center](https://azure.microsoft.com/services/security-center) and [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) provide additional protection and notifications. Azure Security Center also provides a reputation system.

### Business continuity

**High availability**: The solution deploys all virtual machines in an [Availability Set](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets). Availability sets ensure that the virtual machines are distributed across multiple isolated hardware clusters to improve availability. At least one virtual machine is available during a planned or unplanned maintenance event, meeting the 99.95% Azure SLA.

**Recovery Services Vault**: The [Recovery Services Vault](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) houses backup data and protects all configurations of Azure Virtual Machines in this architecture. With a Recovery Services Vault, customers can restore files and folders from an IaaS virtual machine without restoring the entire virtual machine, enabling faster restore times.

**Cloud Witness**: [Cloud Witness](https://docs.microsoft.com/windows-server/failover-clustering/whats-new-in-failover-clustering#BKMK_CloudWitness) is a type of Failover Cluster quorum witness in Windows Server 2016 that leverages Azure as the arbitration point. The Cloud Witness, like any other quorum witness, gets a vote and can participate in the quorum calculations, but it uses the standard publicly available Azure Blob Storage. This eliminates the extra maintenance overhead of virtual machines hosted in a public cloud.

### Logging and auditing

Azure services extensively log system and user activity, as well as system health:
- **Activity logs**: [Activity logs](../../azure-monitor/platform/activity-logs-overview.md) provide insight into operations performed on resources in a subscription. Activity logs can help determine an operation's initiator, time of occurrence, and status.
- **Diagnostic logs**: [Diagnostic logs](../../azure-monitor/platform/diagnostic-logs-overview.md) include all logs emitted by every resource. These logs include Windows event system logs, Azure Storage logs, Key Vault audit logs, and Application Gateway access and firewall logs. All diagnostic logs write to a centralized and encrypted Azure storage account for archival. The retention is user-configurable, up to 730 days, to meet organization-specific retention requirements.

**Azure Monitor logs**: These logs are consolidated in [Azure Monitor logs](https://azure.microsoft.com/services/log-analytics/) for processing, storing, and dashboard reporting. Once collected, the data is organized into separate tables for each data type within Log Analytics workspaces, which allows all data to be analyzed together regardless of its original source. Furthermore, Azure Security Center integrates with Azure Monitor logs allowing customers to use Kusto queries to access their security event data and combine it with data from other services.

The following Azure [monitoring solutions](../../monitoring/monitoring-solutions.md) are included as a part of this architecture:
-	[Active Directory Assessment](../../azure-monitor/insights/ad-assessment.md): The Active Directory Health Check solution assesses the risk and health of server environments on a regular interval and provides a prioritized list of recommendations specific to the deployed server infrastructure.
- [SQL Assessment](../../azure-monitor/insights/sql-assessment.md): The SQL Health Check solution assesses the risk and health of server environments on a regular interval and provides customers with a prioritized list of recommendations specific to the deployed server infrastructure.
- [Agent Health](../../monitoring/monitoring-solution-agenthealth.md): The Agent Health solution reports how many agents are deployed and their geographic distribution, as well as how many agents which are unresponsive and the number of agents which are submitting operational data.
-	[Activity Log Analytics](../../azure-monitor/platform/collect-activity-logs.md): The Activity Log Analytics solution assists with analysis of the Azure activity logs across all Azure subscriptions for a customer.

**Azure Automation**: [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) stores, runs, and manages runbooks. In this solution, runbooks help collect logs from Azure SQL Database. The Automation [Change Tracking](../../automation/change-tracking.md) solution enables customers to easily identify changes in the environment.

**Azure Monitor**:
[Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) helps users track performance, maintain security, and identify trends by enabling organizations to audit, create alerts, and archive data, including tracking API calls in their Azure resources.

## Threat model

The data flow diagram (DFD) for this reference architecture is available for [download](https://aka.ms/pcidss-iaaswa-tm) or can be found below. This model can help customers understand the points of potential risk in the system infrastructure when making modifications.

![IaaS Web Application for PCI DSS reference architecture diagram](images/pcidss-iaaswa-threat-model.png "IaaS Web Application for PCI DSS reference architecture diagram")

## Compliance documentation

The [Azure Security and Compliance Blueprint - PCI DSS Customer Responsibility Matrix](https://aka.ms/pcidss-crm) lists responsibilities for all PCI DSS 3.2 requirements.

The [Azure Security and Compliance Blueprint - PCI DSS IaaS Web Application Implementation Matrix](https://aka.ms/pcidss-iaaswa-cim) provides information on which PCI DSS 3.2 requirements are addressed by the IaaS Web Application architecture, including detailed descriptions of how the implementation meets the requirements of each covered article.

## Guidance and recommendations

### VPN and ExpressRoute

A secure VPN tunnel or [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) needs to be configured to securely establish a connection to the resources deployed as a part of this IaaS Web Application reference architecture. By appropriately setting up a VPN or ExpressRoute, customers can add a layer of protection for data in transit.

By implementing a secure VPN tunnel with Azure, a virtual private connection between an on-premises network and an Azure Virtual Network can be created. This connection takes place over the Internet and allows customers to securely “tunnel” information inside an encrypted link between the customer's network and Azure. Site-to-Site VPN is a secure, mature technology that has been deployed by enterprises of all sizes for decades. The [IPsec tunnel mode](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) is used in this option as an encryption mechanism.

Because traffic within the VPN tunnel does traverse the Internet with a site-to-site VPN, Microsoft offers another, even more secure connection option. Azure ExpressRoute is a dedicated WAN link between Azure and an on-premises location or an Exchange hosting provider. As ExpressRoute connections do not go over the Internet, these connections offer more reliability, faster speeds, lower latencies, and higher security than typical connections over the Internet. Furthermore, because this is a direct connection of customer's telecommunication provider, the data does not travel over the Internet and therefore is not exposed to it.

Best practices for implementing a secure hybrid network that extends an on-premises network to Azure are [available](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

## Disclaimer

- This document is for informational purposes only. MICROSOFT MAKES NO WARRANTIES, EXPRESS, IMPLIED, OR STATUTORY, AS TO THE INFORMATION IN THIS DOCUMENT. This document is provided "as-is." Information and views expressed in this document, including URL and other Internet website references, may change without notice. Customers reading this document bear the risk of using it.
- This document does not provide customers with any legal rights to any intellectual property in any Microsoft product or solutions.
- Customers may copy and use this document for internal reference purposes.
- Certain recommendations in this document may result in increased data, network, or compute resource usage in Azure, and may increase a customer's Azure license or subscription costs.
- This architecture is intended to serve as a foundation for customers to adjust to their specific requirements and should not be used as-is in a production environment.
- This document is developed as a reference and should not be used to define all means by which a customer can meet specific compliance requirements and regulations. Customers should seek legal support from their organization on approved customer implementations.
