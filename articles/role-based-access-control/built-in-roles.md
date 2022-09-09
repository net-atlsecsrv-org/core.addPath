---
title: Built-in roles for Azure resources | Microsoft Docs
description: Describes the built-in roles for role-based access control (RBAC) and Azure resources. Lists the Actions, NotActions, DataActions, and NotDataActions.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''

ms.service: role-based-access-control
ms.devlang:
ms.topic: reference
ms.tgt_pltfrm:
ms.workload: identity
ms.date: 05/16/2019
ms.author: rolyon
ms.reviewer: bagovind

ms.custom: it-pro
---
# Built-in roles for Azure resources

[Role-based access control (RBAC)](overview.md) has several built-in roles for Azure resources that you can assign to users, groups, service principals, and managed identities. Role assignments are the way you control access to Azure resources. If the built-in roles don't meet the specific needs of your organization, you can create your own [custom roles for Azure resources](custom-roles.md).

This article lists the built-in roles for Azure resources, which are always evolving. To get the latest roles, use [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) or [az role definition list](/cli/azure/role/definition#az-role-definition-list). If you are looking for administrator roles for Azure Active Directory, see [Administrator role permissions in Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

## Built-in role descriptions

The following table provides a brief description of each built-in role. Click the role name to see the list of `Actions`, `NotActions`, `DataActions`, and `NotDataActions` for each role. For information about what these actions mean and how they apply to the management and data planes, see [Understand role definitions for Azure resources](role-definitions.md).


| Built-in role | Description |
| --- | --- |
| [Owner](#owner) | Lets you manage everything, including access to resources. |
| [Contributor](#contributor) | Lets you manage everything except access to resources. |
| [Reader](#reader) | Lets you view everything, but not make any changes. |
| [AcrDelete](#acrdelete) | acr delete |
| [AcrImageSigner](#acrimagesigner) | acr image signer |
| [AcrPull](#acrpull) | acr pull |
| [AcrPush](#acrpush) | acr push |
| [AcrQuarantineReader](#acrquarantinereader) | acr quarantine data reader |
| [AcrQuarantineWriter](#acrquarantinewriter) | acr quarantine data writer |
| [API Management Service Contributor](#api-management-service-contributor) | Can manage service and the APIs |
| [API Management Service Operator Role](#api-management-service-operator-role) | Can manage service but not the APIs |
| [API Management Service Reader Role](#api-management-service-reader-role) | Read-only access to service and APIs |
| [Application Insights Component Contributor](#application-insights-component-contributor) | Can manage Application Insights components |
| [Application Insights Snapshot Debugger](#application-insights-snapshot-debugger) | Gives user permission to view and download debug snapshots collected with the Application Insights Snapshot Debugger. Note that these permissions are not included in the [Owner](#owner) or [Contributor](#contributor) roles. |
| [Automation Job Operator](#automation-job-operator) | Create and Manage Jobs using Automation Runbooks. |
| [Automation Operator](#automation-operator) | Automation Operators are able to start, stop, suspend, and resume jobs |
| [Automation Runbook Operator](#automation-runbook-operator) | Read Runbook properties - to be able to create Jobs of the runbook. |
| [Avere Contributor](#avere-contributor) | Can create and manage an Avere vFXT cluster. |
| [Avere Operator](#avere-operator) | Used by the Avere vFXT cluster to manage the cluster |
| [Azure Kubernetes Service Cluster Admin Role](#azure-kubernetes-service-cluster-admin-role) | List cluster admin credential action. |
| [Azure Kubernetes Service Cluster User Role](#azure-kubernetes-service-cluster-user-role) | List cluster user credential action. |
| [Azure Maps Data Reader (Preview)](#azure-maps-data-reader-preview) | Grants access to read map related data from an Azure maps account. |
| [Azure Stack Registration Owner](#azure-stack-registration-owner) | Lets you manage Azure Stack registrations. |
| [Backup Contributor](#backup-contributor) | Lets you manage backup service,but can't create vaults and give access to others |
| [Backup Operator](#backup-operator) | Lets you manage backup services, except removal of backup, vault creation and giving access to others |
| [Backup Reader](#backup-reader) | Can view backup services, but can't make changes |
| [Billing Reader](#billing-reader) | Allows read access to billing data |
| [BizTalk Contributor](#biztalk-contributor) | Lets you manage BizTalk services, but not access to them. |
| [Blockchain Member Node Access (Preview)](#blockchain-member-node-access-preview) | Allows for access to Blockchain Member nodes |
| [CDN Endpoint Contributor](#cdn-endpoint-contributor) | Can manage CDN endpoints, but can’t grant access to other users. |
| [CDN Endpoint Reader](#cdn-endpoint-reader) | Can view CDN endpoints, but can’t make changes. |
| [CDN Profile Contributor](#cdn-profile-contributor) | Can manage CDN profiles and their endpoints, but can’t grant access to other users. |
| [CDN Profile Reader](#cdn-profile-reader) | Can view CDN profiles and their endpoints, but can’t make changes. |
| [Classic Network Contributor](#classic-network-contributor) | Lets you manage classic networks, but not access to them. |
| [Classic Storage Account Contributor](#classic-storage-account-contributor) | Lets you manage classic storage accounts, but not access to them. |
| [Classic Storage Account Key Operator Service Role](#classic-storage-account-key-operator-service-role) | Classic Storage Account Key Operators are allowed to list and regenerate keys on Classic Storage Accounts |
| [Classic Virtual Machine Contributor](#classic-virtual-machine-contributor) | Lets you manage classic virtual machines, but not access to them, and not the virtual network or storage account they’re connected to. |
| [Cognitive Services Contributor](#cognitive-services-contributor) | Lets you create, read, update, delete and manage keys of Cognitive Services. |
| [Cognitive Services Data Reader (Preview)](#cognitive-services-data-reader-preview) | Lets you read Cognitive Services data. |
| [Cognitive Services User](#cognitive-services-user) | Lets you read and list keys of Cognitive Services. |
| [Cosmos DB Account Reader Role](#cosmos-db-account-reader-role) | Can read Azure Cosmos DB account data. See [DocumentDB Account Contributor](#documentdb-account-contributor) for managing Azure Cosmos DB accounts. |
| [Cosmos DB Operator](#cosmos-db-operator) | Lets you manage Azure Cosmos DB accounts, but not access data in them. Prevents access to account keys and connection strings. |
| [CosmosBackupOperator](#cosmosbackupoperator) | Can submit restore request for a Cosmos DB database or a container for an account |
| [Cost Management Contributor](#cost-management-contributor) | Can view costs and manage cost configuration (e.g. budgets, exports) |
| [Cost Management Reader](#cost-management-reader) | Can view cost data and configuration (e.g. budgets, exports) |
| [Data Box Contributor](#data-box-contributor) | Lets you manage everything under Data Box Service except giving access to others. |
| [Data Box Reader](#data-box-reader) | Lets you manage Data Box Service except creating order or editing order details and giving access to others. |
| [Data Factory Contributor](#data-factory-contributor) | Create and manage data factories, as well as child resources within them. |
| [Data Lake Analytics Developer](#data-lake-analytics-developer) | Lets you submit, monitor, and manage your own jobs but not create or delete Data Lake Analytics accounts. |
| [Data Purger](#data-purger) | Can purge analytics data |
| [DevTest Labs User](#devtest-labs-user) | Lets you connect, start, restart, and shutdown your virtual machines in your Azure DevTest Labs. |
| [DNS Zone Contributor](#dns-zone-contributor) | Lets you manage DNS zones and record sets in Azure DNS, but does not let you control who has access to them. |
| [DocumentDB Account Contributor](#documentdb-account-contributor) | Can manage Azure Cosmos DB accounts. Azure Cosmos DB is formerly known as DocumentDB. |
| [Event Hubs Data Owner](#event-hubs-data-owner) | Allows full access to Azure Event Hubs resources | 
| [EventGrid EventSubscription Contributor](#eventgrid-eventsubscription-contributor) | Lets you manage EventGrid event subscription operations. |
| [EventGrid EventSubscription Reader](#eventgrid-eventsubscription-reader) | Lets you read EventGrid event subscriptions. |
| [HDInsight Cluster Operator](#hdinsight-cluster-operator) | Lets you read and modify HDInsight cluster configurations. |
| [HDInsight Domain Services Contributor](#hdinsight-domain-services-contributor) | Can Read, Create, Modify and Delete Domain Services related operations needed for HDInsight Enterprise Security Package |
| [Intelligent Systems Account Contributor](#intelligent-systems-account-contributor) | Lets you manage Intelligent Systems accounts, but not access to them. |
| [Key Vault Contributor](#key-vault-contributor) | Lets you manage key vaults, but not access to them. |
| [Lab Creator](#lab-creator) | Lets you create, manage, delete your managed labs under your Azure Lab Accounts. |
| [Log Analytics Contributor](#log-analytics-contributor) | Log Analytics Contributor can read all monitoring data and edit monitoring settings. Editing monitoring settings includes adding the VM extension to VMs; reading storage account keys to be able to configure collection of logs from Azure Storage; creating and configuring Automation accounts; adding solutions; and configuring Azure diagnostics on all Azure resources. |
| [Log Analytics Reader](#log-analytics-reader) | Log Analytics Reader can view and search all monitoring data as well as and view monitoring settings, including viewing the configuration of Azure diagnostics on all Azure resources. |
| [Logic App Contributor](#logic-app-contributor) | Lets you manage logic app, but not access to them. |
| [Logic App Operator](#logic-app-operator) | Lets you read, enable and disable logic app. |
| [Managed Application Operator Role](#managed-application-operator-role) | Lets you read and perform actions on Managed Application resources |
| [Managed Applications Reader](#managed-applications-reader) | Lets you read resources in a managed app and request JIT access. |
| [Managed Identity Contributor](#managed-identity-contributor) | Create, Read, Update, and Delete User Assigned Identity |
| [Managed Identity Operator](#managed-identity-operator) | Read and Assign User Assigned Identity |
| [Management Group Contributor](#management-group-contributor) | Management Group Contributor Role |
| [Management Group Reader](#management-group-reader) | Management Group Reader Role |
| [Monitoring Contributor](#monitoring-contributor) | Can read all monitoring data and edit monitoring settings. See also [Get started with roles, permissions, and security with Azure Monitor](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles). |
| [Monitoring Metrics Publisher](#monitoring-metrics-publisher) | Enables publishing metrics against Azure resources |
| [Monitoring Reader](#monitoring-reader) | Can read all monitoring data (metrics, logs, etc.). See also [Get started with roles, permissions, and security with Azure Monitor](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles). |
| [Network Contributor](#network-contributor) | Lets you manage networks, but not access to them. |
| [New Relic APM Account Contributor](#new-relic-apm-account-contributor) | Lets you manage New Relic Application Performance Management accounts and applications, but not access to them. |
| [Reader and Data Access](#reader-and-data-access) | Lets you view everything but will not let you delete or create a storage account or contained resource. It will also allow read/write access to all data contained in a storage account via access to storage account keys. |
| [Redis Cache Contributor](#redis-cache-contributor) | Lets you manage Redis caches, but not access to them. |
| [Resource Policy Contributor (Preview)](#resource-policy-contributor-preview) | (Preview) Backfilled users from EA, with rights to create/modify resource policy, create support ticket and read resources/hierarchy. |
| [Scheduler Job Collections Contributor](#scheduler-job-collections-contributor) | Lets you manage Scheduler job collections, but not access to them. |
| [Search Service Contributor](#search-service-contributor) | Lets you manage Search services, but not access to them. |
| [Security Admin](#security-admin) | In Security Center only: Can view security policies, view security states, edit security policies, view alerts and recommendations, dismiss alerts and recommendations |
| [Security Manager (Legacy)](#security-manager-legacy) | This is a legacy role. Please use Security Administrator instead |
| [Security Reader](#security-reader) | In Security Center only: Can view recommendations and alerts, view security policies, view security states, but cannot make changes |
| [Service Bus Data Owner](#service-bus-data-owner) | Allows for full access to Azure Service Bus resources |
| [Site Recovery Contributor](#site-recovery-contributor) | Lets you manage Site Recovery service except vault creation and role assignment |
| [Site Recovery Operator](#site-recovery-operator) | Lets you failover and failback but not perform other Site Recovery management operations |
| [Site Recovery Reader](#site-recovery-reader) | Lets you view Site Recovery status but not perform other management operations |
| [Spatial Anchors Account Contributor](#spatial-anchors-account-contributor) | Lets you manage spatial anchors in your account, but not delete them |
| [Spatial Anchors Account Owner](#spatial-anchors-account-owner) | Lets you manage spatial anchors in your account, including deleting them |
| [Spatial Anchors Account Reader](#spatial-anchors-account-reader) | Lets you locate and read properties of spatial anchors in your account |
| [SQL DB Contributor](#sql-db-contributor) | Lets you manage SQL databases, but not access to them. Also, you can't manage their security-related policies or their parent SQL servers. |
| [SQL Managed Instance Contributor](#sql-managed-instance-contributor) | Lets you manage SQL Managed Instances and required network configuration, but can’t give access to others. |
| [SQL Security Manager](#sql-security-manager) | Lets you manage the security-related policies of SQL servers and databases, but not access to them. |
| [SQL Server Contributor](#sql-server-contributor) | Lets you manage SQL servers and databases, but not access to them, and not their security -related policies. |
| [Storage Account Contributor](#storage-account-contributor) | Lets you manage storage accounts, but not access to them. |
| [Storage Account Key Operator Service Role](#storage-account-key-operator-service-role) | Storage Account Key Operators are allowed to list and regenerate keys on Storage Accounts |
| [Storage Blob Data Contributor](#storage-blob-data-contributor) | Allows for read, write and delete access to Azure Storage blob containers and data |
| [Storage Blob Data Owner](#storage-blob-data-owner) | Allows for full access to Azure Storage blob containers and data, including assigning POSIX access control. |
| [Storage Blob Data Reader](#storage-blob-data-reader) | Allows for read access to Azure Storage blob containers and data |
| [Storage Queue Data Contributor](#storage-queue-data-contributor) | Allows for read, write, and delete access to Azure Storage queues and queue messages |
| [Storage Queue Data Message Processor](#storage-queue-data-message-processor) | Allows for peek, receive, and delete access to Azure Storage queue messages |
| [Storage Queue Data Message Sender](#storage-queue-data-message-sender) | Allows for sending of Azure Storage queue messages |
| [Storage Queue Data Reader](#storage-queue-data-reader) | Allows for read access to Azure Storage queues and queue messages |
| [Support Request Contributor](#support-request-contributor) | Lets you create and manage Support requests |
| [Traffic Manager Contributor](#traffic-manager-contributor) | Lets you manage Traffic Manager profiles, but does not let you control who has access to them. |
| [User Access Administrator](#user-access-administrator) | Lets you manage user access to Azure resources. |
| [Virtual Machine Administrator Login](#virtual-machine-administrator-login) | View Virtual Machines in the portal and login as administrator |
| [Virtual Machine Contributor](#virtual-machine-contributor) | Lets you manage virtual machines, but not access to them, and not the virtual network or storage account they're connected to. |
| [Virtual Machine User Login](#virtual-machine-user-login) | View Virtual Machines in the portal and login as a regular user. |
| [Web Plan Contributor](#web-plan-contributor) | Lets you manage the web plans for websites, but not access to them. |
| [Website Contributor](#website-contributor) | Lets you manage websites (not web plans), but not access to them. |


## Owner
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Lets you manage everything, including access to resources. |
> | **Id** | 8e3af657-a8ff-443c-a75c-2fe8c4bcb635 |
> | **Actions** |  |
> | * | Create and manage resources of all types |
> | **NotActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## Contributor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Lets you manage everything except access to resources. |
> | **Id** | b24988ac-6180-42a0-ab88-20f7382dd24c |
> | **Actions** |  |
> | * | Create and manage resources of all types |
> | **NotActions** |  |
> | Microsoft.Authorization/*/Delete | Delete roles and role assignments |
> | Microsoft.Authorization/*/Write | Create roles and role assignments |
> | Microsoft.Authorization/elevateAccess/Action | Grants the caller User Access Administrator access at the tenant scope |
> | Microsoft.Blueprint/blueprintAssignments/write | Create or update any blueprint artifacts |
> | Microsoft.Blueprint/blueprintAssignments/delete | Delete any blueprint artifacts |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## Reader
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Lets you view everything, but not make any changes. |
> | **Id** | acdd72a7-3385-48ef-bd42-f606fba81ae7 |
> | **Actions** |  |
> | */read | Read resources of all types, except secrets. |
> | **NotActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## AcrDelete
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | acr delete |
> | **Id** | c2f4ef07-c644-48eb-af81-4b1b4947fb11 |
> | **Actions** |  |
> | Microsoft.ContainerRegistry/registries/artifacts/delete | Delete artifact in a container registry. |
> | **NotActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## AcrImageSigner
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | acr image signer |
> | **Id** | 6cef56e8-d556-48e5-a04f-b8e64114680f |
> | **Actions** |  |
> | Microsoft.ContainerRegistry/registries/sign/write | Push/Pull content trust metadata for a container registry. |
> | **NotActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## AcrPull
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | acr pull |
> | **Id** | 7f951dda-4ed3-4680-a7ca-43fe172d538d |
> | **Actions** |  |
> | Microsoft.ContainerRegistry/registries/pull/read | Pull or Get images from a container registry. |
> | **NotActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## AcrPush
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | acr push |
> | **Id** | 8311e382-0749-4cb8-b61a-304f252e45ec |
> | **Actions** |  |
> | Microsoft.ContainerRegistry/registries/pull/read | Pull or Get images from a container registry. |
> | Microsoft.ContainerRegistry/registries/push/write | Push or Write images to a container registry. |
> | **NotActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## AcrQuarantineReader
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | acr quarantine data reader |
> | **Id** | cdda3590-29a3-44f6-95f2-9f980659eb04 |
> | **Actions** |  |
> | Microsoft.ContainerRegistry/registries/quarantineRead/read | Pull or Get quarantined images from container registry |
> | **NotActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## AcrQuarantineWriter
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | acr quarantine data writer |
> | **Id** | c8d4ff99-41c3-41a8-9f60-21dfdad59608 |
> | **Actions** |  |
> | Microsoft.ContainerRegistry/registries/quarantineRead/read | Pull or Get quarantined images from container registry |
> | Microsoft.ContainerRegistry/registries/quarantineWrite/write | Write/Modify quarantine state of quarantined images |
> | **NotActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## API Management Service Contributor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Can manage service and the APIs |
> | **Id** | 312a565d-c81f-4fd8-895a-4e21e48d571c |
> | **Actions** |  |
> | Microsoft.ApiManagement/service/* | Create and manage API Management service |
> | Microsoft.Authorization/*/read | Read authorization |
> | Microsoft.Insights/alertRules/* | Create and manage alert rules |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Gets the availability statuses for all resources in the specified scope |
> | Microsoft.Resources/deployments/* | Create and manage resource group deployments |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Gets or lists resource groups. |
> | Microsoft.Support/* | Create and manage support tickets |
> | **NotActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## API Management Service Operator Role
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Can manage service but not the APIs |
> | **Id** | e022efe7-f5ba-4159-bbe4-b44f577e9b61 |
> | **Actions** |  |
> | Microsoft.ApiManagement/service/*/read | Read API Management Service instances |
> | Microsoft.ApiManagement/service/backup/action | Backup API Management Service to the specified container in a user provided storage account |
> | Microsoft.ApiManagement/service/delete | Delete API Management Service instance |
> | Microsoft.ApiManagement/service/managedeployments/action | Change SKU/units, add/remove regional deployments of API Management Service |
> | Microsoft.ApiManagement/service/read | Read metadata for an API Management Service instance |
> | Microsoft.ApiManagement/service/restore/action | Restore API Management Service from the specified container in a user provided storage account |
> | Microsoft.ApiManagement/service/updatecertificate/action | Upload SSL certificate for an API Management Service |
> | Microsoft.ApiManagement/service/updatehostname/action | Setup, update or remove custom domain names for an API Management Service |
> | Microsoft.ApiManagement/service/write | Create a new instance of API Management Service |
> | Microsoft.Authorization/*/read | Read authorization |
> | Microsoft.Insights/alertRules/* | Create and manage alert rules |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Gets the availability statuses for all resources in the specified scope |
> | Microsoft.Resources/deployments/* | Create and manage resource group deployments |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Gets or lists resource groups. |
> | Microsoft.Support/* | Create and manage support tickets |
> | **NotActions** |  |
> | Microsoft.ApiManagement/service/users/keys/read | Get keys associated with user |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## API Management Service Reader Role
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Read-only access to service and APIs |
> | **Id** | 71522526-b88f-4d52-b57f-d31fc3546d0d |
> | **Actions** |  |
> | Microsoft.ApiManagement/service/*/read | Read API Management Service instances |
> | Microsoft.ApiManagement/service/read | Read metadata for an API Management Service instance |
> | Microsoft.Authorization/*/read | Read authorization |
> | Microsoft.Insights/alertRules/* | Create and manage alert rules |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Gets the availability statuses for all resources in the specified scope |
> | Microsoft.Resources/deployments/* | Create and manage resource group deployments |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Gets or lists resource groups. |
> | Microsoft.Support/* | Create and manage support tickets |
> | **NotActions** |  |
> | Microsoft.ApiManagement/service/users/keys/read | Get keys associated with user |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## Application Insights Component Contributor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Can manage Application Insights components |
> | **Id** | ae349356-3a1b-4a5e-921d-050484c6347e |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Read roles and role assignments |
> | Microsoft.Insights/alertRules/* | Create and manage alert rules |
> | Microsoft.Insights/components/* | Create and manage Insights components |
> | Microsoft.Insights/webtests/* | Create and manage web tests |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Gets the availability statuses for all resources in the specified scope |
> | Microsoft.Resources/deployments/* | Create and manage resource group deployments |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Gets or lists resource groups. |
> | Microsoft.Support/* | Create and manage support tickets |
> | **NotActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## Application Insights Snapshot Debugger
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Gives user permission to view and download debug snapshots collected with the Application Insights Snapshot Debugger. Note that these permissions are not included in the [Owner](#owner) or [Contributor](#contributor) roles. |
> | **Id** | 08954f03-6346-4c2e-81c0-ec3a5cfae23b |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Read roles and role assignments |
> | Microsoft.Insights/alertRules/* | Create and manage Insights alert rules |
> | Microsoft.Insights/components/*/read |  |
> | Microsoft.Resources/deployments/* | Create and manage resource group deployments |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Gets or lists resource groups. |
> | Microsoft.Support/* | Create and manage support tickets |
> | **NotActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## Automation Job Operator
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Create and Manage Jobs using Automation Runbooks. |
> | **Id** | 4fe576fe-1146-4730-92eb-48519fa6bf9f |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Read roles and role assignments |
> | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Reads Hybrid Runbook Worker Resources |
> | Microsoft.Automation/automationAccounts/jobs/read | Gets an Azure Automation job |
> | Microsoft.Automation/automationAccounts/jobs/resume/action | Resumes an Azure Automation job |
> | Microsoft.Automation/automationAccounts/jobs/stop/action | Stops an Azure Automation job |
> | Microsoft.Automation/automationAccounts/jobs/streams/read | Gets an Azure Automation job stream |
> | Microsoft.Automation/automationAccounts/jobs/suspend/action | Suspends an Azure Automation job |
> | Microsoft.Automation/automationAccounts/jobs/write | Creates an Azure Automation job |
> | Microsoft.Automation/automationAccounts/jobs/output/read | Gets the output of a job |
> | Microsoft.Insights/alertRules/* | Create and manage Insights alert rules |
> | Microsoft.Resources/deployments/* | Create and manage resource group deployments |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Gets or lists resource groups. |
> | Microsoft.Support/* | Create and manage support tickets |
> | **NotActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## Automation Operator
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Automation Operators are able to start, stop, suspend, and resume jobs |
> | **Id** | d3881f73-407a-4167-8283-e981cbba0404 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Read roles and role assignments |
> | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Reads Hybrid Runbook Worker Resources |
> | Microsoft.Automation/automationAccounts/jobs/read | Gets an Azure Automation job |
> | Microsoft.Automation/automationAccounts/jobs/resume/action | Resumes an Azure Automation job |
> | Microsoft.Automation/automationAccounts/jobs/stop/action | Stops an Azure Automation job |
> | Microsoft.Automation/automationAccounts/jobs/streams/read | Gets an Azure Automation job stream |
> | Microsoft.Automation/automationAccounts/jobs/suspend/action | Suspends an Azure Automation job |
> | Microsoft.Automation/automationAccounts/jobs/write | Creates an Azure Automation job |
> | Microsoft.Automation/automationAccounts/jobSchedules/read | Gets an Azure Automation job schedule |
> | Microsoft.Automation/automationAccounts/jobSchedules/write | Creates an Azure Automation job schedule |
> | Microsoft.Automation/automationAccounts/linkedWorkspace/read | Gets the workspace linked to the automation account |
> | Microsoft.Automation/automationAccounts/read | Gets an Azure Automation account |
> | Microsoft.Automation/automationAccounts/runbooks/read | Gets an Azure Automation runbook |
> | Microsoft.Automation/automationAccounts/schedules/read | Gets an Azure Automation schedule asset |
> | Microsoft.Automation/automationAccounts/schedules/write | Creates or updates an Azure Automation schedule asset |
> | Microsoft.Insights/alertRules/* | Create and manage Insights alert rules |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Gets the availability statuses for all resources in the specified scope |
> | Microsoft.Resources/deployments/* | Create and manage resource group deployments |
> | Microsoft.Automation/automationAccounts/jobs/output/read | Gets the output of a job |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Gets or lists resource groups. |
> | Microsoft.Support/* | Create and manage support tickets |
> | **NotActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## Automation Runbook Operator
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Read Runbook properties - to be able to create Jobs of the runbook. |
> | **Id** | 5fb5aef8-1081-4b8e-bb16-9d5d0385bab5 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Read roles and role assignments |
> | Microsoft.Automation/automationAccounts/runbooks/read | Gets an Azure Automation runbook |
> | Microsoft.Insights/alertRules/* | Create and manage Insights alert rules |
> | Microsoft.Resources/deployments/* | Create and manage resource group deployments |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Gets or lists resource groups. |
> | Microsoft.Support/* | Create and manage support tickets |
> | **NotActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## Avere Contributor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Can create and manage an Avere vFXT cluster. |
> | **Id** | 4f8fab4f-1852-4a58-a46a-8eaf358af14a |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Read roles and role assignments |
> | Microsoft.Compute/*/read |  |
> | Microsoft.Compute/availabilitySets/* |  |
> | Microsoft.Compute/virtualMachines/* |  |
> | Microsoft.Compute/disks/* |  |
> | Microsoft.Network/*/read |  |
> | Microsoft.Network/networkInterfaces/* |  |
> | Microsoft.Network/virtualNetworks/read | Get the virtual network definition |
> | Microsoft.Network/virtualNetworks/subnets/read | Gets a virtual network subnet definition |
> | Microsoft.Network/virtualNetworks/subnets/join/action | Joins a virtual network. Not Alertable. |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Joins resource such as storage account or SQL database to a subnet. Not alertable. |
> | Microsoft.Network/networkSecurityGroups/join/action | Joins a network security group. Not Alertable. |
> | Microsoft.Resources/deployments/* | Create and manage resource group deployments |
> | Microsoft.Insights/alertRules/* | Create and manage Insights alert rules |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Gets or lists resource groups. |
> | Microsoft.Storage/*/read |  |
> | Microsoft.Storage/storageAccounts/* |  |
> | Microsoft.Support/* | Create and manage support tickets |
> | Microsoft.Resources/subscriptions/resourceGroups/resources/read | Gets the resources for the resource group. |
> | **NotActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | Returns the result of deleting a blob |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Returns a blob or a list of blobs |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | Returns the result of writing a blob |
> | **NotDataActions** |  |
> | *none* |  |

## Avere Operator
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Used by the Avere vFXT cluster to manage the cluster |
> | **Id** | c025889f-8102-4ebf-b32c-fc0c6f0c6bd9 |
> | **Actions** |  |
> | Microsoft.Compute/virtualMachines/read | Get the properties of a virtual machine |
> | Microsoft.Network/networkInterfaces/read | Gets a network interface definition.  |
> | Microsoft.Network/networkInterfaces/write | Creates a network interface or updates an existing network interface.  |
> | Microsoft.Network/virtualNetworks/read | Get the virtual network definition |
> | Microsoft.Network/virtualNetworks/subnets/read | Gets a virtual network subnet definition |
> | Microsoft.Network/virtualNetworks/subnets/join/action | Joins a virtual network. Not Alertable. |
> | Microsoft.Network/networkSecurityGroups/join/action | Joins a network security group. Not Alertable. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Gets or lists resource groups. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/delete | Returns the result of deleting a container |
> | Microsoft.Storage/storageAccounts/blobServices/containers/read | Returns list of containers |
> | Microsoft.Storage/storageAccounts/blobServices/containers/write | Returns the result of put blob container |
> | **NotActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | Returns the result of deleting a blob |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Returns a blob or a list of blobs |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | Returns the result of writing a blob |
> | **NotDataActions** |  |
> | *none* |  |

## Azure Kubernetes Service Cluster Admin Role
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | List cluster admin credential action. |
> | **Id** | 0ab0b1a8-8aac-4efd-b8c2-3ee1fb270be8 |
> | **Actions** |  |
> | Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action | List the clusterAdmin credential of a managed cluster |
> | **NotActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## Azure Kubernetes Service Cluster User Role
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | List cluster user credential action. |
> | **Id** | 4abbcc35-e782-43d8-92c5-2d3f1bd2253f |
> | **Actions** |  |
> | Microsoft.ContainerService/managedClusters/listClusterUserCredential/action | List the clusterUser credential of a managed cluster |
> | **NotActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## Azure Maps Data Reader (Preview)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Grants access to read map related data from an Azure maps account. |
> | **Id** | 423170ca-a8f6-4b0f-8487-9e4eb8f49bfa |
> | **Actions** |  |
> | *none* |  |
> | **NotActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | Microsoft.Maps/accounts/data/read | Grants data read access to a maps account. |
> | **NotDataActions** |  |
> | *none* |  |

## Azure Stack Registration Owner
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Lets you manage Azure Stack registrations. |
> | **Id** | 6f12a6df-dd06-4f3e-bcb1-ce8be600526a |
> | **Actions** |  |
> | Microsoft.AzureStack/registrations/products/listDetails/action | Retrieves extended details for an Azure Stack Marketplace product |
> | Microsoft.AzureStack/registrations/products/read | Gets the properties of an Azure Stack Marketplace product |
> | Microsoft.AzureStack/registrations/read | Gets the properties of an Azure Stack registration |
> | **NotActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## Backup Contributor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Lets you manage backup service,but can't create vaults and give access to others |
> | **Id** | 5e467623-bb1f-42f4-a55d-6e525e11384b |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Read roles and role assignments |
> | Microsoft.Network/virtualNetworks/read | Get the virtual network definition |
> | Microsoft.RecoveryServices/locations/* |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/* | Manage results of operation on backup management |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/* | Create and manage backup containers inside backup fabrics of Recovery Services vault |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action | Refreshes the container list |
> | Microsoft.RecoveryServices/Vaults/backupJobs/* | Create and manage backup jobs |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Export Jobs |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/operationResults/read |  |
> | Microsoft.RecoveryServices/Vaults/backupManagementMetaData/* | Create and manage meta data related to backup management |
> | Microsoft.RecoveryServices/Vaults/backupOperationResults/* | Create and manage Results of backup management operations |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/* | Create and manage backup policies |
> | Microsoft.RecoveryServices/Vaults/backupProtectableItems/* | Create and manage items which can be backed up |
> | Microsoft.RecoveryServices/Vaults/backupProtectedItems/* | Create and manage backed up items |
> | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/* | Create and manage containers holding backup items |
> | Microsoft.RecoveryServices/Vaults/backupSecurityPIN/* |  |
> | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Returns summaries for Protected Items and Protected Servers for a Recovery Services . |
> | Microsoft.RecoveryServices/Vaults/certificates/* | Create and manage certificates related to backup in Recovery Services vault |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/* | Create and manage extended info related to vault |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Gets the alerts for the Recovery services vault. |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
> | Microsoft.RecoveryServices/Vaults/read | The Get Vault operation gets an object representing the Azure resource of type 'vault' |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/* | Create and manage registered identities |
> | Microsoft.RecoveryServices/Vaults/usages/* | Create and manage usage of Recovery Services vault |
> | Microsoft.Resources/deployments/* | Create and manage resource group deployments |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Gets or lists resource groups. |
> | Microsoft.Storage/storageAccounts/read | Returns the list of storage accounts or gets the properties for the specified storage account. |
> | Microsoft.RecoveryServices/Vaults/backupstorageconfig/* |  |
> | Microsoft.RecoveryServices/Vaults/backupconfig/* |  |
> | Microsoft.RecoveryServices/Vaults/backupValidateOperation/action | Validate Operation on Protected Item |
> | Microsoft.RecoveryServices/Vaults/write | Create Vault operation creates an Azure resource of type 'vault' |
> | Microsoft.RecoveryServices/Vaults/backupOperations/read | Returns Backup Operation Status for Recovery Services Vault. |
> | Microsoft.RecoveryServices/Vaults/backupEngines/read | Returns all the backup management servers registered with vault. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/* |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read | Get all protectable containers |
> | Microsoft.RecoveryServices/locations/backupStatus/action | Check Backup Status for Recovery Services Vaults |
> | Microsoft.RecoveryServices/locations/backupPreValidateProtection/action |  |
> | Microsoft.RecoveryServices/locations/backupValidateFeatures/action | Validate Features |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | Resolves the alert. |
> | Microsoft.RecoveryServices/operations/read | Operation returns the list of Operations for a Resource Provider |
> | Microsoft.RecoveryServices/locations/operationStatus/read | Gets Operation Status for a given Operation |
> | Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read | List all backup Protection Intents |
> | Microsoft.Support/* | Create and manage support tickets |
> | **NotActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## Backup Operator
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Lets you manage backup services, except removal of backup, vault creation and giving access to others |
> | **Id** | 00c29273-979b-4161-815c-10b084fb9324 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Read roles and role assignments |
> | Microsoft.Network/virtualNetworks/read | Get the virtual network definition |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | Returns status of the operation |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | Gets result of Operation performed on Protection Container. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action | Performs Backup for Protected Item. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Gets Result of Operation Performed on Protected Items. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | Returns the status of Operation performed on Protected Items. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Returns object details of the Protected Item |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action | Provision Instant Item Recovery for Protected Item |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Get Recovery Points for Protected Items. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action | Restore Recovery Points for Protected Items. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action | Revoke Instant Item Recovery for Protected Item |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | Create a backup Protected Item |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Returns all registered containers |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action | Refreshes the container list |
> | Microsoft.RecoveryServices/Vaults/backupJobs/* | Create and manage backup jobs |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Export Jobs |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/operationResults/read |  |
> | Microsoft.RecoveryServices/Vaults/backupManagementMetaData/read |  |
> | Microsoft.RecoveryServices/Vaults/backupOperationResults/* | Create and manage Results of backup management operations |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | Get Results of Policy Operation. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Returns all Protection Policies |
> | Microsoft.RecoveryServices/Vaults/backupProtectableItems/* | Create and manage items which can be backed up |
> | Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | Returns the list of all Protected Items. |
> | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Returns all containers belonging to the subscription |
> | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Returns summaries for Protected Items and Protected Servers for a Recovery Services . |
> | Microsoft.RecoveryServices/Vaults/certificates/write | The Update Resource Certificate operation updates the resource/vault credential certificate. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | The Get Extended Info operation gets an object's Extended Info representing the Azure resource of type ?vault? |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/write | The Get Extended Info operation gets an object's Extended Info representing the Azure resource of type ?vault? |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Gets the alerts for the Recovery services vault. |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
> | Microsoft.RecoveryServices/Vaults/read | The Get Vault operation gets an object representing the Azure resource of type 'vault' |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | The Get Operation Results operation can be used get the operation status and result for the asynchronously submitted operation |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | The Get Containers operation can be used get the containers registered for a resource. |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/write | The Register Service Container operation can be used to register a container with Recovery Service. |
> | Microsoft.RecoveryServices/Vaults/usages/read | Returns usage details for a Recovery Services Vault. |
> | Microsoft.Resources/deployments/* | Create and manage resource group deployments |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Gets or lists resource groups. |
> | Microsoft.Storage/storageAccounts/read | Returns the list of storage accounts or gets the properties for the specified storage account. |
> | Microsoft.RecoveryServices/Vaults/backupstorageconfig/* |  |
> | Microsoft.RecoveryServices/Vaults/backupValidateOperation/action | Validate Operation on Protected Item |
> | Microsoft.RecoveryServices/Vaults/backupOperations/read | Returns Backup Operation Status for Recovery Services Vault. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read | Get Status of Policy Operation. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/write | Creates a registered container |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/inquire/action | Do inquiry for workloads within a container |
> | Microsoft.RecoveryServices/Vaults/backupEngines/read | Returns all the backup management servers registered with vault. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write | Create a backup Protection Intent |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read | Get a backup Protection Intent |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read | Get all protectable containers |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read | Get all items in a container |
> | Microsoft.RecoveryServices/locations/backupStatus/action | Check Backup Status for Recovery Services Vaults |
> | Microsoft.RecoveryServices/locations/backupPreValidateProtection/action |  |
> | Microsoft.RecoveryServices/locations/backupValidateFeatures/action | Validate Features |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | Resolves the alert. |
> | Microsoft.RecoveryServices/operations/read | Operation returns the list of Operations for a Resource Provider |
> | Microsoft.RecoveryServices/locations/operationStatus/read | Gets Operation Status for a given Operation |
> | Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read | List all backup Protection Intents |
> | Microsoft.Support/* | Create and manage support tickets |
> | **NotActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## Backup Reader
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Can view backup services, but can't make changes |
> | **Id** | a795c7a0-d4a2-40c1-ae25-d81f01202912 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Read roles and role assignments |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp is internal operation used by service |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | Returns status of the operation |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | Gets result of Operation performed on Protection Container. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Gets Result of Operation Performed on Protected Items. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | Returns the status of Operation performed on Protected Items. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Returns object details of the Protected Item |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Get Recovery Points for Protected Items. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Returns all registered containers |
> | Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read | Returns the Result of Job Operation. |
> | Microsoft.RecoveryServices/Vaults/backupJobs/read | Returns all Job Objects |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Export Jobs |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/operationResults/read |  |
> | Microsoft.RecoveryServices/Vaults/backupManagementMetaData/read |  |
> | Microsoft.RecoveryServices/Vaults/backupOperationResults/read | Returns Backup Operation Result for Recovery Services Vault. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | Get Results of Policy Operation. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Returns all Protection Policies |
> | Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | Returns the list of all Protected Items. |
> | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Returns all containers belonging to the subscription |
> | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Returns summaries for Protected Items and Protected Servers for a Recovery Services . |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | The Get Extended Info operation gets an object's Extended Info representing the Azure resource of type ?vault? |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Gets the alerts for the Recovery services vault. |
> | Microsoft.RecoveryServices/Vaults/read | The Get Vault operation gets an object representing the Azure resource of type 'vault' |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | The Get Operation Results operation can be used get the operation status and result for the asynchronously submitted operation |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | The Get Containers operation can be used get the containers registered for a resource. |
> | Microsoft.RecoveryServices/Vaults/backupstorageconfig/read | Returns Storage Configuration for Recovery Services Vault. |
> | Microsoft.RecoveryServices/Vaults/backupconfig/read | Returns Configuration for Recovery Services Vault. |
> | Microsoft.RecoveryServices/Vaults/backupOperations/read | Returns Backup Operation Status for Recovery Services Vault. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read | Get Status of Policy Operation. |
> | Microsoft.RecoveryServices/Vaults/backupEngines/read | Returns all the backup management servers registered with vault. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read | Get a backup Protection Intent |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read | Get all items in a container |
> | Microsoft.RecoveryServices/locations/backupStatus/action | Check Backup Status for Recovery Services Vaults |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | Resolves the alert. |
> | Microsoft.RecoveryServices/operations/read | Operation returns the list of Operations for a Resource Provider |
> | Microsoft.RecoveryServices/locations/operationStatus/read | Gets Operation Status for a given Operation |
> | Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read | List all backup Protection Intents |
> | Microsoft.RecoveryServices/Vaults/usages/read | Returns usage details for a Recovery Services Vault. |
> | **NotActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## Billing Reader
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Allows read access to billing data |
> | **Id** | fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Read roles and role assignments |
> | Microsoft.Billing/*/read | Read Billing information |
> | Microsoft.Commerce/*/read |  |
> | Microsoft.Consumption/*/read |  |
> | Microsoft.Management/managementGroups/read | List management groups for the authenticated user. |
> | Microsoft.CostManagement/*/read |  |
> | Microsoft.Support/* | Create and manage support tickets |
> | **NotActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## BizTalk Contributor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Lets you manage BizTalk services, but not access to them. |
> | **Id** | 5e3c6656-6cfa-4708-81fe-0de47ac73342 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Read roles and role assignments |
> | Microsoft.BizTalkServices/BizTalk/* | Create and manage BizTalk services |
> | Microsoft.Insights/alertRules/* | Create and manage alert rules |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Gets the availability statuses for all resources in the specified scope |
> | Microsoft.Resources/deployments/* | Create and manage resource group deployments |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Gets or lists resource groups. |
> | Microsoft.Support/* | Create and manage support tickets |
> | **NotActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## Blockchain Member Node Access (Preview)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Allows for access to Blockchain Member nodes |
> | **Id** | 31a002a1-acaf-453e-8a5b-297c9ca1ea24 |
> | **Actions** |  |
> | Microsoft.Blockchain/blockchainMembers/transactionNodes/read | Gets or Lists existing Blockchain Member Transaction Node(s). |
> | **NotActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | Microsoft.Blockchain/blockchainMembers/transactionNodes/connect/action | Connects to a Blockchain Member Transaction Node. |
> | **NotDataActions** |  |
> | *none* |  |

## CDN Endpoint Contributor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Can manage CDN endpoints, but can’t grant access to other users. |
> | **Id** | 426e0c7f-0c7e-4658-b36f-ff54d6c29b45 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Read roles and role assignments |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/endpoints/* |  |
> | Microsoft.Insights/alertRules/* | Create and manage Insights alert rules |
> | Microsoft.Resources/deployments/* | Create and manage resource group deployments |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Gets or lists resource groups. |
> | Microsoft.Support/* | Create and manage support tickets |
> | **NotActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## CDN Endpoint Reader
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Can view CDN endpoints, but can’t make changes. |
> | **Id** | 871e35f6-b5c1-49cc-a043-bde969a0f2cd |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Read roles and role assignments |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/endpoints/*/read |  |
> | Microsoft.Insights/alertRules/* | Create and manage Insights alert rules |
> | Microsoft.Resources/deployments/* | Create and manage resource group deployments |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Gets or lists resource groups. |
> | Microsoft.Support/* | Create and manage support tickets |
> | **NotActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## CDN Profile Contributor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Can manage CDN profiles and their endpoints, but can’t grant access to other users. |
> | **Id** | ec156ff8-a8d1-4d15-830c-5b80698ca432 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Read roles and role assignments |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/* |  |
> | Microsoft.Insights/alertRules/* | Create and manage Insights alert rules |
> | Microsoft.Resources/deployments/* | Create and manage resource group deployments |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Gets or lists resource groups. |
> | Microsoft.Support/* | Create and manage support tickets |
> | **NotActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## CDN Profile Reader
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Can view CDN profiles and their endpoints, but can’t make changes. |
> | **Id** | 8f96442b-4075-438f-813d-ad51ab4019af |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Read roles and role assignments |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/*/read |  |
> | Microsoft.Insights/alertRules/* | Create and manage Insights alert rules |
> | Microsoft.Resources/deployments/* | Create and manage resource group deployments |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Gets or lists resource groups. |
> | Microsoft.Support/* | Create and manage support tickets |
> | **NotActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## Classic Network Contributor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Lets you manage classic networks, but not access to them. |
> | **Id** | b34d265f-36f7-4a0d-a4d4-e158ca92e90f |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Read authorization |
> | Microsoft.ClassicNetwork/* | Create and manage classic networks |
> | Microsoft.Insights/alertRules/* | Create and manage Insights alert rules |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Gets the availability statuses for all resources in the specified scope |
> | Microsoft.Resources/deployments/* | Create and manage resource group deployments |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Gets or lists resource groups. |
> | Microsoft.Support/* | Create and manage support tickets |
> | **NotActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## Classic Storage Account Contributor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Lets you manage classic storage accounts, but not access to them. |
> | **Id** | 86e8f5dc-a6e9-4c67-9d15-de283e8eac25 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Read authorization |
> | Microsoft.ClassicStorage/storageAccounts/* | Create and manage storage accounts |
> | Microsoft.Insights/alertRules/* | Create and manage Insights alert rules |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Gets the availability statuses for all resources in the specified scope |
> | Microsoft.Resources/deployments/* | Create and manage resource group deployments |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Gets or lists resource groups. |
> | Microsoft.Support/* | Create and manage support tickets |
> | **NotActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## Classic Storage Account Key Operator Service Role
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Classic Storage Account Key Operators are allowed to list and regenerate keys on Classic Storage Accounts |
> | **Id** | 985d6b00-f706-48f5-a6fe-d0ca12fb668d |
> | **Actions** |  |
> | Microsoft.ClassicStorage/storageAccounts/listkeys/action | Lists the access keys for the storage accounts. |
> | Microsoft.ClassicStorage/storageAccounts/regeneratekey/action | Regenerates the existing access keys for the storage account. |
> | **NotActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## Classic Virtual Machine Contributor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Lets you manage classic virtual machines, but not access to them, and not the virtual network or storage account they’re connected to. |
> | **Id** | d73bb868-a0df-4d4d-bd69-98a00b01fccb |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Read authorization |
> | Microsoft.ClassicCompute/domainNames/* | Create and manage classic compute domain names |
> | Microsoft.ClassicCompute/virtualMachines/* | Create and manage virtual machines |
> | Microsoft.ClassicNetwork/networkSecurityGroups/join/action |  |
> | Microsoft.ClassicNetwork/reservedIps/link/action | Link a reserved Ip |
> | Microsoft.ClassicNetwork/reservedIps/read | Gets the reserved Ips |
> | Microsoft.ClassicNetwork/virtualNetworks/join/action | Joins the virtual network. |
> | Microsoft.ClassicNetwork/virtualNetworks/read | Get the virtual network. |
> | Microsoft.ClassicStorage/storageAccounts/disks/read | Returns the storage account disk. |
> | Microsoft.ClassicStorage/storageAccounts/images/read | Returns the storage account image. (Deprecated. Use 'Microsoft.ClassicStorage/storageAccounts/vmImages') |
> | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Lists the access keys for the storage accounts. |
> | Microsoft.ClassicStorage/storageAccounts/read | Return the storage account with the given account. |
> | Microsoft.Insights/alertRules/* | Create and manage Insights alert rules |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Gets the availability statuses for all resources in the specified scope |
> | Microsoft.Resources/deployments/* | Create and manage resource group deployments |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Gets or lists resource groups. |
> | Microsoft.Support/* | Create and manage support tickets |
> | **NotActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## Cognitive Services Contributor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Lets you create, read, update, delete and manage keys of Cognitive Services. |
> | **Id** | 25fbc0a9-bd7c-42a3-aa1a-3b75d497ee68 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Read roles and role assignments |
> | Microsoft.CognitiveServices/* |  |
> | Microsoft.Features/features/read | Gets the features of a subscription. |
> | Microsoft.Features/providers/features/read | Gets the feature of a subscription in a given resource provider. |
> | Microsoft.Insights/alertRules/* | Create and manage Insights alert rules |
> | Microsoft.Insights/diagnosticSettings/* | Creates, updates, or reads the diagnostic setting for Analysis Server |
> | Microsoft.Insights/logDefinitions/read | Read log definitions |
> | Microsoft.Insights/metricdefinitions/read | Read metric definitions |
> | Microsoft.Insights/metrics/read | Read metrics |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Gets the availability statuses for all resources in the specified scope |
> | Microsoft.Resources/deployments/* | Create and manage resource group deployments |
> | Microsoft.Resources/deployments/operations/read | Gets or lists deployment operations. |
> | Microsoft.Resources/subscriptions/operationresults/read | Get the subscription operation results. |
> | Microsoft.Resources/subscriptions/read | Gets the list of subscriptions. |
> | Microsoft.Resources/subscriptions/resourcegroups/deployments/* |  |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Gets or lists resource groups. |
> | Microsoft.Support/* | Create and manage support tickets |
> | **NotActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## Cognitive Services Data Reader (Preview)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Lets you read Cognitive Services data. |
> | **Id** | b59867f0-fa02-499b-be73-45a86b5b3e1c |
> | **Actions** |  |
> | *none* |  |
> | **NotActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | Microsoft.CognitiveServices/*/read |  |
> | **NotDataActions** |  |
> | *none* |  |

## Cognitive Services User
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Lets you read and list keys of Cognitive Services. |
> | **Id** | a97b65f3-24c7-4388-baec-2e87135dc908 |
> | **Actions** |  |
> | Microsoft.CognitiveServices/*/read |  |
> | Microsoft.CognitiveServices/accounts/listkeys/action | List Keys |
> | Microsoft.Insights/alertRules/read | Read a classic metric alert |
> | Microsoft.Insights/diagnosticSettings/read | Read a resource diagnostic setting |
> | Microsoft.Insights/logDefinitions/read | Read log definitions |
> | Microsoft.Insights/metricdefinitions/read | Read metric definitions |
> | Microsoft.Insights/metrics/read | Read metrics |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Gets the availability statuses for all resources in the specified scope |
> | Microsoft.Resources/deployments/operations/read | Gets or lists deployment operations. |
> | Microsoft.Resources/subscriptions/operationresults/read | Get the subscription operation results. |
> | Microsoft.Resources/subscriptions/read | Gets the list of subscriptions. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Gets or lists resource groups. |
> | Microsoft.Support/* | Create and manage support tickets |
> | **NotActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | Microsoft.CognitiveServices/* |  |
> | **NotDataActions** |  |
> | *none* |  |

## Cosmos DB Account Reader Role
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Can read Azure Cosmos DB account data. See [DocumentDB Account Contributor](#documentdb-account-contributor) for managing Azure Cosmos DB accounts. |
> | **Id** | fbdf93bf-df7d-467e-a4d2-9458aa1360c8 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Read roles and role assignments, can read permissions given to each user |
> | Microsoft.DocumentDB/*/read | Read any collection |
> | Microsoft.DocumentDB/databaseAccounts/readonlykeys/action | Reads the database account readonly keys. |
> | Microsoft.Insights/MetricDefinitions/read | Read metric definitions |
> | Microsoft.Insights/Metrics/read | Read metrics |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Gets or lists resource groups. |
> | Microsoft.Support/* | Create and manage support tickets |
> | **NotActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## Cosmos DB Operator
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Lets you manage Azure Cosmos DB accounts, but not access data in them. Prevents access to account keys and connection strings. |
> | **Id** | 230815da-be43-4aae-9cb4-875f7bd000aa |
> | **Actions** |  |
> | Microsoft.DocumentDb/databaseAccounts/* |  |
> | Microsoft.Insights/alertRules/* | Create and manage Insights alert rules |
> | Microsoft.Authorization/*/read | Read roles and role assignments |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Gets the availability statuses for all resources in the specified scope |
> | Microsoft.Resources/deployments/* | Create and manage resource group deployments |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Gets or lists resource groups. |
> | Microsoft.Support/* | Create and manage support tickets |
> | **NotActions** |  |
> | Microsoft.DocumentDB/databaseAccounts/readonlyKeys/* |  |
> | Microsoft.DocumentDB/databaseAccounts/regenerateKey/* |  |
> | Microsoft.DocumentDB/databaseAccounts/listKeys/* |  |
> | Microsoft.DocumentDB/databaseAccounts/listConnectionStrings/* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## CosmosBackupOperator
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Can submit restore request for a Cosmos DB database or a container for an account |
> | **Id** | db7b14f2-5adf-42da-9f96-f2ee17bab5cb |
> | **Actions** |  |
> | Microsoft.DocumentDB/databaseAccounts/backup/action | Submit a request to configure backup |
> | Microsoft.DocumentDB/databaseAccounts/restore/action | Submit a restore request |
> | **NotActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## Cost Management Contributor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Can view costs and manage cost configuration (e.g. budgets, exports) |
> | **Id** | 434105ed-43f6-45c7-a02f-909b2ba83430 |
> | **Actions** |  |
> | Microsoft.Consumption/* |  |
> | Microsoft.CostManagement/* |  |
> | Microsoft.Billing/billingPeriods/read | Lists available billing periods |
> | Microsoft.Resources/subscriptions/read | Gets the list of subscriptions. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Gets or lists resource groups. |
> | Microsoft.Support/* | Create and manage support tickets |
> | Microsoft.Advisor/configurations/read | Get configurations |
> | Microsoft.Advisor/recommendations/read | Reads recommendations |
> | **NotActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## Cost Management Reader
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Can view cost data and configuration (e.g. budgets, exports) |
> | **Id** | 72fafb9e-0641-4937-9268-a91bfd8191a3 |
> | **Actions** |  |
> | Microsoft.Consumption/*/read |  |
> | Microsoft.CostManagement/*/read |  |
> | Microsoft.Billing/billingPeriods/read | Lists available billing periods |
> | Microsoft.Resources/subscriptions/read | Gets the list of subscriptions. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Gets or lists resource groups. |
> | Microsoft.Support/* | Create and manage support tickets |
> | Microsoft.Advisor/configurations/read | Get configurations |
> | Microsoft.Advisor/recommendations/read | Reads recommendations |
> | **NotActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## Data Box Contributor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Lets you manage everything under Data Box Service except giving access to others. |
> | **Id** | add466c9-e687-43fc-8d98-dfcf8d720be5 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Read roles and role assignments |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Gets the availability statuses for all resources in the specified scope |
> | Microsoft.Resources/deployments/* | Create and manage resource group deployments |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Gets or lists resource groups. |
> | Microsoft.Support/* | Create and manage support tickets |
> | Microsoft.Databox/* |  |
> | **NotActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## Data Box Reader
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Lets you manage Data Box Service except creating order or editing order details and giving access to others. |
> | **Id** | 028f4ed7-e2a9-465e-a8f4-9c0ffdfdc027 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Read roles and role assignments |
> | Microsoft.Databox/*/read |  |
> | Microsoft.Databox/jobs/listsecrets/action |  |
> | Microsoft.Databox/jobs/listcredentials/action | Lists the unencrypted credentials related to the order. |
> | Microsoft.Databox/locations/availableSkus/action | This method returns the list of available skus. |
> | Microsoft.Databox/locations/validateAddress/action | Validates the shipping address and provides alternate addresses if any. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Gets the availability statuses for all resources in the specified scope |
> | Microsoft.Support/* | Create and manage support tickets |
> | **NotActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## Data Factory Contributor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Create and manage data factories, as well as child resources within them. |
> | **Id** | 673868aa-7521-48a0-acc6-0f60742d39f5 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Read roles and role Assignments |
> | Microsoft.DataFactory/dataFactories/* | Create and manage data factories, and child resources within them. |
> | Microsoft.DataFactory/factories/* | Create and manage data factories, and child resources within them. |
> | Microsoft.Insights/alertRules/* | Create and manage alert rules |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Gets the availability statuses for all resources in the specified scope |
> | Microsoft.Resources/deployments/* | Create and manage resource group deployments |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Gets or lists resource groups. |
> | Microsoft.Support/* | Create and manage support tickets |
> | **NotActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## Data Lake Analytics Developer
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Lets you submit, monitor, and manage your own jobs but not create or delete Data Lake Analytics accounts. |
> | **Id** | 47b7735b-770e-4598-a7da-8b91488b4c88 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Read roles and role assignments |
> | Microsoft.BigAnalytics/accounts/* |  |
> | Microsoft.DataLakeAnalytics/accounts/* |  |
> | Microsoft.Insights/alertRules/* | Create and manage Insights alert rules |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Gets the availability statuses for all resources in the specified scope |
> | Microsoft.Resources/deployments/* | Create and manage resource group deployments |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Gets or lists resource groups. |
> | Microsoft.Support/* | Create and manage support tickets |
> | **NotActions** |  |
> | Microsoft.BigAnalytics/accounts/Delete |  |
> | Microsoft.BigAnalytics/accounts/TakeOwnership/action |  |
> | Microsoft.BigAnalytics/accounts/Write |  |
> | Microsoft.DataLakeAnalytics/accounts/Delete | Delete a DataLakeAnalytics account. |
> | Microsoft.DataLakeAnalytics/accounts/TakeOwnership/action | Grant permissions to cancel jobs submitted by other users. |
> | Microsoft.DataLakeAnalytics/accounts/Write | Create or update a DataLakeAnalytics account. |
> | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Write | Create or update a linked DataLakeStore account of a DataLakeAnalytics account. |
> | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Delete | Unlink a DataLakeStore account from a DataLakeAnalytics account. |
> | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Write | Create or update a linked Storage account of a DataLakeAnalytics account. |
> | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Delete | Unlink a Storage account from a DataLakeAnalytics account. |
> | Microsoft.DataLakeAnalytics/accounts/firewallRules/Write | Create or update a firewall rule. |
> | Microsoft.DataLakeAnalytics/accounts/firewallRules/Delete | Delete a firewall rule. |
> | Microsoft.DataLakeAnalytics/accounts/computePolicies/Write | Create or update a compute policy. |
> | Microsoft.DataLakeAnalytics/accounts/computePolicies/Delete | Delete a compute policy. |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## Data Purger
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Can purge analytics data |
> | **Id** | 150f5e0c-0603-4f03-8c7f-cf70034c4e90 |
> | **Actions** |  |
> | Microsoft.Insights/components/*/read |  |
> | Microsoft.Insights/components/purge/action | Purging data from Application Insights |
> | Microsoft.OperationalInsights/workspaces/*/read |  |
> | Microsoft.OperationalInsights/workspaces/purge/action | Delete specified data from workspace |
> | **NotActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## DevTest Labs User
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Lets you connect, start, restart, and shutdown your virtual machines in your Azure DevTest Labs. |
> | **Id** | 76283e04-6283-4c54-8f91-bcf1374a3c64 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Read roles and role Assignments |
> | Microsoft.Compute/availabilitySets/read | Get the properties of an availability set |
> | Microsoft.Compute/virtualMachines/*/read | Read the properties of a virtual machine (VM sizes, runtime status, VM extensions, etc.) |
> | Microsoft.Compute/virtualMachines/deallocate/action | Powers off the virtual machine and releases the compute resources |
> | Microsoft.Compute/virtualMachines/read | Get the properties of a virtual machine |
> | Microsoft.Compute/virtualMachines/restart/action | Restarts the virtual machine |
> | Microsoft.Compute/virtualMachines/start/action | Starts the virtual machine |
> | Microsoft.DevTestLab/*/read | Read the properties of a lab |
> | Microsoft.DevTestLab/labs/claimAnyVm/action | Claim a random claimable virtual machine in the lab. |
> | Microsoft.DevTestLab/labs/createEnvironment/action | Create virtual machines in a lab. |
> | Microsoft.DevTestLab/labs/ensureCurrentUserProfile/action | Ensure the current user has a valid profile in the lab. |
> | Microsoft.DevTestLab/labs/formulas/delete | Delete formulas. |
> | Microsoft.DevTestLab/labs/formulas/read | Read formulas. |
> | Microsoft.DevTestLab/labs/formulas/write | Add or modify formulas. |
> | Microsoft.DevTestLab/labs/policySets/evaluatePolicies/action | Evaluates lab policy. |
> | Microsoft.DevTestLab/labs/virtualMachines/claim/action | Take ownership of an existing virtual machine |
> | Microsoft.DevTestLab/labs/virtualmachines/listApplicableSchedules/action | Lists the applicable start/stop schedules, if any. |
> | Microsoft.DevTestLab/labs/virtualMachines/getRdpFileContents/action | Gets a string that represents the contents of the RDP file for the virtual machine |
> | Microsoft.Network/loadBalancers/backendAddressPools/join/action | Joins a load balancer backend address pool. Not Alertable. |
> | Microsoft.Network/loadBalancers/inboundNatRules/join/action | Joins a load balancer inbound nat rule. Not Alertable. |
> | Microsoft.Network/networkInterfaces/*/read | Read the properties of a network interface (for example, all the load balancers that the network interface is a part of) |
> | Microsoft.Network/networkInterfaces/join/action | Joins a Virtual Machine to a network interface. Not Alertable. |
> | Microsoft.Network/networkInterfaces/read | Gets a network interface definition.  |
> | Microsoft.Network/networkInterfaces/write | Creates a network interface or updates an existing network interface.  |
> | Microsoft.Network/publicIPAddresses/*/read | Read the properties of a public IP address |
> | Microsoft.Network/publicIPAddresses/join/action | Joins a public ip address. Not Alertable. |
> | Microsoft.Network/publicIPAddresses/read | Gets a public ip address definition. |
> | Microsoft.Network/virtualNetworks/subnets/join/action | Joins a virtual network. Not Alertable. |
> | Microsoft.Resources/deployments/operations/read | Gets or lists deployment operations. |
> | Microsoft.Resources/deployments/read | Gets or lists deployments. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Gets or lists resource groups. |
> | Microsoft.Storage/storageAccounts/listKeys/action | Returns the access keys for the specified storage account. |
> | **NotActions** |  |
> | Microsoft.Compute/virtualMachines/vmSizes/read | Lists available sizes the virtual machine can be updated to |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## DNS Zone Contributor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Lets you manage DNS zones and record sets in Azure DNS, but does not let you control who has access to them. |
> | **Id** | befefa01-2a29-4197-83a8-272ff33ce314 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Read roles and role assignments |
> | Microsoft.Insights/alertRules/* | Create and manage alert rules |
> | Microsoft.Network/dnsZones/* | Create and manage DNS zones and records |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Gets the availability statuses for all resources in the specified scope |
> | Microsoft.Resources/deployments/* | Create and manage resource group deployments |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Gets or lists resource groups. |
> | Microsoft.Support/* | Create and manage Support tickets |
> | **NotActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## DocumentDB Account Contributor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Can manage Azure Cosmos DB accounts. Azure Cosmos DB is formerly known as DocumentDB. |
> | **Id** | 5bd9cd88-fe45-4216-938b-f97437e15450 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Read roles and role Assignments |
> | Microsoft.DocumentDb/databaseAccounts/* | Create and manage Azure Cosmos DB accounts |
> | Microsoft.Insights/alertRules/* | Create and manage alert rules |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Gets the availability statuses for all resources in the specified scope |
> | Microsoft.Resources/deployments/* | Create and manage resource group deployments |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Gets or lists resource groups. |
> | Microsoft.Support/* | Create and manage support tickets |
> | **NotActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## Event Hubs Data Owner

> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Allows for full access to Azure Event Hubs resources. |
> | **Id** | f526a384-b230-433a-b45c-95f59c4a2dec |
> | **Actions** |  |
> | Microsoft.EventHubs/* | Allows full management access to Event Hubs namespace |
> | **NotActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | Microsoft.EventHubs/* | Allows full data access to Event Hubs namespace |
> | **NotDataActions** |  |
> | *none* |  |

## EventGrid EventSubscription Contributor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Lets you manage EventGrid event subscription operations. |
> | **Id** | 428e0ff0-5e57-4d9c-a221-2c70d0e0a443 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Read roles and role assignments |
> | Microsoft.EventGrid/eventSubscriptions/* |  |
> | Microsoft.EventGrid/topicTypes/eventSubscriptions/read | List global event subscriptions by topic type |
> | Microsoft.EventGrid/locations/eventSubscriptions/read | List regional event subscriptions |
> | Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read | List regional event subscriptions by topictype |
> | Microsoft.Insights/alertRules/* | Create and manage Insights alert rules |
> | Microsoft.Resources/deployments/* | Create and manage resource group deployments |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Gets or lists resource groups. |
> | Microsoft.Support/* | Create and manage support tickets |
> | **NotActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## EventGrid EventSubscription Reader
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Lets you read EventGrid event subscriptions. |
> | **Id** | 2414bbcf-6497-4faf-8c65-045460748405 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Read roles and role assignments |
> | Microsoft.EventGrid/eventSubscriptions/read | Read an eventSubscription |
> | Microsoft.EventGrid/topicTypes/eventSubscriptions/read | List global event subscriptions by topic type |
> | Microsoft.EventGrid/locations/eventSubscriptions/read | List regional event subscriptions |
> | Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read | List regional event subscriptions by topictype |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Gets or lists resource groups. |
> | **NotActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## HDInsight Cluster Operator
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Lets you read and modify HDInsight cluster configurations. |
> | **Id** | 61ed4efc-fab3-44fd-b111-e24485cc132a |
> | **Actions** |  |
> | Microsoft.HDInsight/*/read |  |
> | Microsoft.HDInsight/clusters/getGatewaySettings/action | Get gateway settings for HDInsight Cluster |
> | Microsoft.HDInsight/clusters/updateGatewaySettings/action | Update gateway settings for HDInsight Cluster |
> | Microsoft.HDInsight/clusters/configurations/* |  |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Gets or lists resource groups. |
> | Microsoft.Resources/deployments/operations/read | Gets or lists deployment operations. |
> | Microsoft.Insights/alertRules/* | Create and manage Insights alert rules |
> | Microsoft.Authorization/*/read | Read roles and role assignments |
> | Microsoft.Support/* | Create and manage support tickets |
> | **NotActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## HDInsight Domain Services Contributor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Can Read, Create, Modify and Delete Domain Services related operations needed for HDInsight Enterprise Security Package |
> | **Id** | 8d8d5a11-05d3-4bda-a417-a08778121c7c |
> | **Actions** |  |
> | Microsoft.AAD/*/read |  |
> | Microsoft.AAD/domainServices/*/read |  |
> | Microsoft.AAD/domainServices/oucontainer/* |  |
> | **NotActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## Intelligent Systems Account Contributor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Lets you manage Intelligent Systems accounts, but not access to them. |
> | **Id** | 03a6d094-3444-4b3d-88af-7477090a9e5e |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Read roles and role Assignments |
> | Microsoft.Insights/alertRules/* | Create and manage alert rules |
> | Microsoft.IntelligentSystems/accounts/* | Create and manage intelligent systems accounts |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Gets the availability statuses for all resources in the specified scope |
> | Microsoft.Resources/deployments/* | Create and manage resource group deployments |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Gets or lists resource groups. |
> | Microsoft.Support/* | Create and manage support tickets |
> | **NotActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## Key Vault Contributor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Lets you manage key vaults, but not access to them. |
> | **Id** | f25e0fa2-a7c8-4377-a976-54943a77a395 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Read roles and role assignments |
> | Microsoft.Insights/alertRules/* | Create and manage Insights alert rules |
> | Microsoft.KeyVault/* |  |
> | Microsoft.Resources/deployments/* | Create and manage resource group deployments |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Gets or lists resource groups. |
> | Microsoft.Support/* | Create and manage support tickets |
> | **NotActions** |  |
> | Microsoft.KeyVault/locations/deletedVaults/purge/action | Purge a soft deleted key vault |
> | Microsoft.KeyVault/hsmPools/* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## Lab Creator
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Lets you create, manage, delete your managed labs under your Azure Lab Accounts. |
> | **Id** | b97fb8bc-a8b2-4522-a38b-dd33c7e65ead |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Read roles and role assignments |
> | Microsoft.LabServices/labAccounts/*/read |  |
> | Microsoft.LabServices/labAccounts/createLab/action | Create a lab in a lab account. |
> | Microsoft.LabServices/labAccounts/sizes/getRegionalAvailability/action |  |
> | Microsoft.LabServices/labAccounts/getRegionalAvailability/action | Get regional availability information for each size category configured under a lab account |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Gets or lists resource groups. |
> | Microsoft.Support/* | Create and manage support tickets |
> | **NotActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## Log Analytics Contributor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Log Analytics Contributor can read all monitoring data and edit monitoring settings. Editing monitoring settings includes adding the VM extension to VMs; reading storage account keys to be able to configure collection of logs from Azure Storage; creating and configuring Automation accounts; adding solutions; and configuring Azure diagnostics on all Azure resources. |
> | **Id** | 92aaf0da-9dab-42b6-94a3-d43ce8d16293 |
> | **Actions** |  |
> | */read | Read resources of all types, except secrets. |
> | Microsoft.Automation/automationAccounts/* |  |
> | Microsoft.ClassicCompute/virtualMachines/extensions/* |  |
> | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Lists the access keys for the storage accounts. |
> | Microsoft.Compute/virtualMachines/extensions/* |  |
> | Microsoft.Insights/alertRules/* | Create and manage Insights alert rules |
> | Microsoft.Insights/diagnosticSettings/* | Creates, updates, or reads the diagnostic setting for Analysis Server |
> | Microsoft.OperationalInsights/* |  |
> | Microsoft.OperationsManagement/* |  |
> | Microsoft.Resources/deployments/* | Create and manage resource group deployments |
> | Microsoft.Resources/subscriptions/resourcegroups/deployments/* |  |
> | Microsoft.Storage/storageAccounts/listKeys/action | Returns the access keys for the specified storage account. |
> | Microsoft.Support/* | Create and manage support tickets |
> | **NotActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## Log Analytics Reader
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Log Analytics Reader can view and search all monitoring data as well as and view monitoring settings, including viewing the configuration of Azure diagnostics on all Azure resources. |
> | **Id** | 73c42c96-874c-492b-b04d-ab87d138a893 |
> | **Actions** |  |
> | */read | Read resources of all types, except secrets. |
> | Microsoft.OperationalInsights/workspaces/analytics/query/action | Search using new engine. |
> | Microsoft.OperationalInsights/workspaces/search/action | Executes a search query |
> | Microsoft.Support/* | Create and manage support tickets |
> | **NotActions** |  |
> | Microsoft.OperationalInsights/workspaces/sharedKeys/read | Retrieves the shared keys for the workspace. These keys are used to connect Microsoft Operational Insights agents to the workspace. |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## Logic App Contributor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Lets you manage logic app, but not access to them. |
> | **Id** | 87a39d53-fc1b-424a-814c-f7e04687dc9e |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Read roles and role assignments |
> | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Lists the access keys for the storage accounts. |
> | Microsoft.ClassicStorage/storageAccounts/read | Return the storage account with the given account. |
> | Microsoft.Insights/alertRules/* | Create and manage Insights alert rules |
> | Microsoft.Insights/diagnosticSettings/* | Creates, updates, or reads the diagnostic setting for Analysis Server |
> | Microsoft.Insights/logdefinitions/* | This permission is necessary for users who need access to Activity Logs via the portal. List log categories in Activity Log. |
> | Microsoft.Insights/metricDefinitions/* | Read metric definitions (list of available metric types for a resource). |
> | Microsoft.Logic/* | Manages Logic Apps resources. |
> | Microsoft.Resources/deployments/* | Create and manage resource group deployments |
> | Microsoft.Resources/subscriptions/operationresults/read | Get the subscription operation results. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Gets or lists resource groups. |
> | Microsoft.Storage/storageAccounts/listkeys/action | Returns the access keys for the specified storage account. |
> | Microsoft.Storage/storageAccounts/read | Returns the list of storage accounts or gets the properties for the specified storage account. |
> | Microsoft.Support/* | Create and manage support tickets |
> | Microsoft.Web/connectionGateways/* | Create and manages a Connection Gateway. |
> | Microsoft.Web/connections/* | Create and manages a Connection. |
> | Microsoft.Web/customApis/* | Creates and manages a Custom API. |
> | Microsoft.Web/serverFarms/join/action |  |
> | Microsoft.Web/serverFarms/read | Get the properties on an App Service Plan |
> | Microsoft.Web/sites/functions/listSecrets/action | List Secrets Web Apps Functions. |
> | **NotActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## Logic App Operator
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Lets you read, enable and disable logic app. |
> | **Id** | 515c2055-d9d4-4321-b1b9-bd0c9a0f79fe |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Read roles and role assignments |
> | Microsoft.Insights/alertRules/*/read | Read Insights alert rules |
> | Microsoft.Insights/diagnosticSettings/*/read | Gets diagnostic settings for Logic Apps |
> | Microsoft.Insights/metricDefinitions/*/read | Gets the available metrics for Logic Apps. |
> | Microsoft.Logic/*/read | Reads Logic Apps resources. |
> | Microsoft.Logic/workflows/disable/action | Disables the workflow. |
> | Microsoft.Logic/workflows/enable/action | Enables the workflow. |
> | Microsoft.Logic/workflows/validate/action | Validates the workflow. |
> | Microsoft.Resources/deployments/operations/read | Gets or lists deployment operations. |
> | Microsoft.Resources/subscriptions/operationresults/read | Get the subscription operation results. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Gets or lists resource groups. |
> | Microsoft.Support/* | Create and manage support tickets |
> | Microsoft.Web/connectionGateways/*/read | Read Connection Gateways. |
> | Microsoft.Web/connections/*/read | Read Connections. |
> | Microsoft.Web/customApis/*/read | Read Custom API. |
> | Microsoft.Web/serverFarms/read | Get the properties on an App Service Plan |
> | **NotActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## Managed Application Operator Role
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Lets you read and perform actions on Managed Application resources |
> | **Id** | c7393b34-138c-406f-901b-d8cf2b17e6ae |
> | **Actions** |  |
> | */read | Read resources of all types, except secrets. |
> | Microsoft.Solutions/applications/read | Retrieves a list of applications. |
> | Microsoft.Solutions/*/action |  |
> | **NotActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## Managed Applications Reader
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Lets you read resources in a managed app and request JIT access. |
> | **Id** | b9331d33-8a36-4f8c-b097-4f54124fdb44 |
> | **Actions** |  |
> | */read | Read resources of all types, except secrets. |
> | Microsoft.Resources/deployments/* | Create and manage resource group deployments |
> | Microsoft.Solutions/jitRequests/* |  |
> | **NotActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## Managed Identity Contributor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Create, Read, Update, and Delete User Assigned Identity |
> | **Id** | e40ec5ca-96e0-45a2-b4ff-59039f2c2b59 |
> | **Actions** |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/read |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/write |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/delete |  |
> | Microsoft.Authorization/*/read | Read roles and role assignments |
> | Microsoft.Insights/alertRules/* | Create and manage Insights alert rules |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Gets or lists resource groups. |
> | Microsoft.Resources/deployments/* | Create and manage resource group deployments |
> | Microsoft.Support/* | Create and manage support tickets |
> | **NotActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## Managed Identity Operator
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Read and Assign User Assigned Identity |
> | **Id** | f1a07417-d97a-45cb-824c-7a7467783830 |
> | **Actions** |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/read |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/assign/action |  |
> | Microsoft.Authorization/*/read | Read roles and role assignments |
> | Microsoft.Insights/alertRules/* | Create and manage Insights alert rules |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Gets or lists resource groups. |
> | Microsoft.Resources/deployments/* | Create and manage resource group deployments |
> | Microsoft.Support/* | Create and manage support tickets |
> | **NotActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## Management Group Contributor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Management Group Contributor Role |
> | **Id** | 5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c |
> | **Actions** |  |
> | Microsoft.Management/managementGroups/delete | Delete management group. |
> | Microsoft.Management/managementGroups/read | List management groups for the authenticated user. |
> | Microsoft.Management/managementGroups/subscriptions/delete | De-associates subscription from the management group. |
> | Microsoft.Management/managementGroups/subscriptions/write | Associates existing subscription with the management group. |
> | Microsoft.Management/managementGroups/write | Create or update a management group. |
> | **NotActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## Management Group Reader
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Management Group Reader Role |
> | **Id** | ac63b705-f282-497d-ac71-919bf39d939d |
> | **Actions** |  |
> | Microsoft.Management/managementGroups/read | List management groups for the authenticated user. |
> | **NotActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## Monitoring Contributor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Can read all monitoring data and edit monitoring settings. See also [Get started with roles, permissions, and security with Azure Monitor](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles). |
> | **Id** | 749f88d5-cbae-40b8-bcfc-e573ddc772fa |
> | **Actions** |  |
> | */read | Read resources of all types, except secrets. |
> | Microsoft.AlertsManagement/alerts/* |  |
> | Microsoft.AlertsManagement/alertsSummary/* |  |
> | Microsoft.Insights/actiongroups/* |  |
> | Microsoft.Insights/activityLogAlerts/* |  |
> | Microsoft.Insights/AlertRules/* | Read/write/delete alert rules. |
> | Microsoft.Insights/components/* | Read/write/delete Application Insights components. |
> | Microsoft.Insights/DiagnosticSettings/* | Read/write/delete diagnostic settings. |
> | Microsoft.Insights/eventtypes/* | List Activity Log events (management events) in a subscription. This permission is applicable to both programmatic and portal access to the Activity Log. |
> | Microsoft.Insights/LogDefinitions/* | This permission is necessary for users who need access to Activity Logs via the portal. List log categories in Activity Log. |
> | Microsoft.Insights/metricalerts/* |  |
> | Microsoft.Insights/MetricDefinitions/* | Read metric definitions (list of available metric types for a resource). |
> | Microsoft.Insights/Metrics/* | Read metrics for a resource. |
> | Microsoft.Insights/Register/Action | Register the Microsoft Insights provider |
> | Microsoft.Insights/scheduledqueryrules/* |  |
> | Microsoft.Insights/webtests/* | Read/write/delete Application Insights web tests. |
> | Microsoft.OperationalInsights/workspaces/intelligencepacks/* | Read/write/delete log analytics solution packs. |
> | Microsoft.OperationalInsights/workspaces/savedSearches/* | Read/write/delete log analytics saved searches. |
> | Microsoft.OperationalInsights/workspaces/search/action | Executes a search query |
> | Microsoft.OperationalInsights/workspaces/sharedKeys/action | Retrieves the shared keys for the workspace. These keys are used to connect Microsoft Operational Insights agents to the workspace. |
> | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/* | Read/write/delete log analytics storage insight configurations. |
> | Microsoft.Support/* | Create and manage support tickets |
> | Microsoft.WorkloadMonitor/monitors/* |  |
> | Microsoft.WorkloadMonitor/notificationSettings/* |  |
> | **NotActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## Monitoring Metrics Publisher
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Enables publishing metrics against Azure resources |
> | **Id** | 3913510d-42f4-4e42-8a64-420c390055eb |
> | **Actions** |  |
> | Microsoft.Insights/Register/Action | Register the Microsoft Insights provider |
> | Microsoft.Support/* | Create and manage support tickets |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Gets or lists resource groups. |
> | **NotActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | Microsoft.Insights/Metrics/Write | Write metrics |
> | **NotDataActions** |  |
> | *none* |  |

## Monitoring Reader
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Can read all monitoring data (metrics, logs, etc.). See also [Get started with roles, permissions, and security with Azure Monitor](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles). |
> | **Id** | 43d0d8ad-25c7-4714-9337-8ba259a9fe05 |
> | **Actions** |  |
> | */read | Read resources of all types, except secrets. |
> | Microsoft.OperationalInsights/workspaces/search/action | Executes a search query |
> | Microsoft.Support/* | Create and manage support tickets |
> | **NotActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## Network Contributor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Lets you manage networks, but not access to them. |
> | **Id** | 4d97b98b-1d4f-4787-a291-c67834d212e7 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Read roles and role Assignments |
> | Microsoft.Insights/alertRules/* | Create and manage alert rules |
> | Microsoft.Network/* | Create and manage networks |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Gets the availability statuses for all resources in the specified scope |
> | Microsoft.Resources/deployments/* | Create and manage resource group deployments |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Gets or lists resource groups. |
> | Microsoft.Support/* | Create and manage support tickets |
> | **NotActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## New Relic APM Account Contributor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Lets you manage New Relic Application Performance Management accounts and applications, but not access to them. |
> | **Id** | 5d28c62d-5b37-4476-8438-e587778df237 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Read roles and role assignments |
> | Microsoft.Insights/alertRules/* | Create and manage Insights alert rules |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Gets the availability statuses for all resources in the specified scope |
> | Microsoft.Resources/deployments/* | Create and manage resource group deployments |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Gets or lists resource groups. |
> | Microsoft.Support/* | Create and manage support tickets |
> | NewRelic.APM/accounts/* |  |
> | **NotActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## Reader and Data Access
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Lets you view everything but will not let you delete or create a storage account or contained resource. It will also allow read/write access to all data contained in a storage account via access to storage account keys. |
> | **Id** | c12c1c16-33a1-487b-954d-41c89c60f349 |
> | **Actions** |  |
> | Microsoft.Storage/storageAccounts/listKeys/action | Returns the access keys for the specified storage account. |
> | Microsoft.Storage/storageAccounts/ListAccountSas/action | Returns the Account SAS token for the specified storage account. |
> | Microsoft.Storage/storageAccounts/read | Returns the list of storage accounts or gets the properties for the specified storage account. |
> | **NotActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## Redis Cache Contributor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Lets you manage Redis caches, but not access to them. |
> | **Id** | e0f68234-74aa-48ed-b826-c38b57376e17 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Read roles and role Assignments |
> | Microsoft.Cache/redis/* | Create and manage Redis caches |
> | Microsoft.Insights/alertRules/* | Create and manage alert rules |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Gets the availability statuses for all resources in the specified scope |
> | Microsoft.Resources/deployments/* | Create and manage resource group deployments |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Gets or lists resource groups. |
> | Microsoft.Support/* | Create and manage support tickets |
> | **NotActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## Resource Policy Contributor (Preview)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | (Preview) Backfilled users from EA, with rights to create/modify resource policy, create support ticket and read resources/hierarchy. |
> | **Id** | 36243c78-bf99-498c-9df9-86d9f8d28608 |
> | **Actions** |  |
> | */read | Read resources of all types, except secrets. |
> | Microsoft.Authorization/policyassignments/* | Create and manage policy assignments |
> | Microsoft.Authorization/policydefinitions/* | Create and manage policy definitions |
> | Microsoft.Authorization/policysetdefinitions/* | Create and manage policy sets |
> | Microsoft.PolicyInsights/* |  |
> | Microsoft.Support/* | Create and manage support tickets |
> | **NotActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## Scheduler Job Collections Contributor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Lets you manage Scheduler job collections, but not access to them. |
> | **Id** | 188a0f2f-5c9e-469b-ae67-2aa5ce574b94 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Read roles and role Assignments |
> | Microsoft.Insights/alertRules/* | Create and manage alert rules |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Gets the availability statuses for all resources in the specified scope |
> | Microsoft.Resources/deployments/* | Create and manage resource group deployments |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Gets or lists resource groups. |
> | Microsoft.Scheduler/jobcollections/* | Create and manage job collections |
> | Microsoft.Support/* | Create and manage support tickets |
> | **NotActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## Search Service Contributor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Lets you manage Search services, but not access to them. |
> | **Id** | 7ca78c08-252a-4471-8644-bb5ff32d4ba0 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Read roles and role Assignments |
> | Microsoft.Insights/alertRules/* | Create and manage alert rules |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Gets the availability statuses for all resources in the specified scope |
> | Microsoft.Resources/deployments/* | Create and manage resource group deployments |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Gets or lists resource groups. |
> | Microsoft.Search/searchServices/* | Create and manage search services |
> | Microsoft.Support/* | Create and manage support tickets |
> | **NotActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## Security Admin
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | In Security Center only: Can view security policies, view security states, edit security policies, view alerts and recommendations, dismiss alerts and recommendations |
> | **Id** | fb1c8493-542b-48eb-b624-b4c8fea62acd |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Read roles and role assignments |
> | Microsoft.Authorization/policyAssignments/* | Create and manage policy assignments |
> | Microsoft.Authorization/policyDefinitions/* | Create and manage policy definitions |
> | Microsoft.Authorization/policySetDefinitions/* | Create and manage policy sets |
> | Microsoft.Insights/alertRules/* | Create and manage alert rules |
> | Microsoft.Management/managementGroups/read | List management groups for the authenticated user. |
> | Microsoft.operationalInsights/workspaces/*/read | View log analytics data |
> | Microsoft.Resources/deployments/* | Create and manage resource group deployments |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Gets or lists resource groups. |
> | Microsoft.Security/* |  |
> | Microsoft.Support/* | Create and manage support tickets |
> | **NotActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## Security Manager (Legacy)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | This is a legacy role. Please use Security Administrator instead |
> | **Id** | e3d13bf0-dd5a-482e-ba6b-9b8433878d10 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Read roles and role assignments |
> | Microsoft.ClassicCompute/*/read | Read configuration information classic virtual machines |
> | Microsoft.ClassicCompute/virtualMachines/*/write | Write configuration for classic virtual machines |
> | Microsoft.ClassicNetwork/*/read | Read configuration information about classic network |
> | Microsoft.Insights/alertRules/* | Create and manage alert rules |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Gets the availability statuses for all resources in the specified scope |
> | Microsoft.Resources/deployments/* | Create and manage resource group deployments |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Gets or lists resource groups. |
> | Microsoft.Security/* | Create and manage security components and policies |
> | Microsoft.Support/* | Create and manage support tickets |
> | **NotActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## Security Reader
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | In Security Center only: Can view recommendations and alerts, view security policies, view security states, but cannot make changes |
> | **Id** | 39bc4728-0917-49c7-9d2c-d95423bc2eb4 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Read roles and role assignments |
> | Microsoft.Insights/alertRules/* | Create and manage alert rules |
> | Microsoft.operationalInsights/workspaces/*/read | View log analytics data |
> | Microsoft.Resources/deployments/* | Create and manage resource group deployments |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Gets or lists resource groups. |
> | Microsoft.Security/*/read | Read security components and policies |
> | Microsoft.Support/* | Create and manage support tickets |
> | Microsoft.Management/managementGroups/read | List management groups for the authenticated user. |
> | **NotActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## Service Bus Data Owner

> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Allows for full access to Azure Service Bus resources. |
> | **Id** | 090c5cfd-751d-490a-894a-3ce6f1109419 |
> | **Actions** |  |
> | Microsoft.ServiceBus/* | Allows full management access to Service Bus namespace |
> | **NotActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | Microsoft.ServiceBus/* | Allows full data access to Service Bus namespace |
> | **NotDataActions** |  |
> | *none* |  |

## Site Recovery Contributor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Lets you manage Site Recovery service except vault creation and role assignment |
> | **Id** | 6670b86e-a3f7-4917-ac9b-5d6ab1be4567 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Read roles and role assignments |
> | Microsoft.Insights/alertRules/* | Create and manage alert rules |
> | Microsoft.Network/virtualNetworks/read | Get the virtual network definition |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp is internal operation used by service |
> | Microsoft.RecoveryServices/locations/allocateStamp/action | AllocateStamp is internal operation used by service |
> | Microsoft.RecoveryServices/Vaults/certificates/write | The Update Resource Certificate operation updates the resource/vault credential certificate. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/* | Create and manage extended info related to vault |
> | Microsoft.RecoveryServices/Vaults/read | The Get Vault operation gets an object representing the Azure resource of type 'vault' |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/* | Create and manage registered identities |
> | Microsoft.RecoveryServices/vaults/replicationAlertSettings/* | Create or Update replication alert settings |
> | Microsoft.RecoveryServices/vaults/replicationEvents/read | Read any Events |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/* | Create and manage replication fabrics |
> | Microsoft.RecoveryServices/vaults/replicationJobs/* | Create and manage replication jobs |
> | Microsoft.RecoveryServices/vaults/replicationPolicies/* | Create and manage replication policies |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/* | Create and manage recovery plans |
> | Microsoft.RecoveryServices/Vaults/storageConfig/* | Create and manage storage configuration of Recovery Services vault |
> | Microsoft.RecoveryServices/Vaults/tokenInfo/read |  |
> | Microsoft.RecoveryServices/Vaults/usages/read | Returns usage details for a Recovery Services Vault. |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | The Vault Token operation can be used to get Vault Token for vault level backend operations. |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/* | Read alerts for the Recovery services vault |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Gets the availability statuses for all resources in the specified scope |
> | Microsoft.Resources/deployments/* | Create and manage resource group deployments |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Gets or lists resource groups. |
> | Microsoft.Storage/storageAccounts/read | Returns the list of storage accounts or gets the properties for the specified storage account. |
> | Microsoft.Support/* | Create and manage support tickets |
> | **NotActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## Site Recovery Operator
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Lets you failover and failback but not perform other Site Recovery management operations |
> | **Id** | 494ae006-db33-4328-bf46-533a6560a3ca |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Read roles and role assignments |
> | Microsoft.Insights/alertRules/* | Create and manage alert rules |
> | Microsoft.Network/virtualNetworks/read | Get the virtual network definition |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp is internal operation used by service |
> | Microsoft.RecoveryServices/locations/allocateStamp/action | AllocateStamp is internal operation used by service |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | The Get Extended Info operation gets an object's Extended Info representing the Azure resource of type ?vault? |
> | Microsoft.RecoveryServices/Vaults/read | The Get Vault operation gets an object representing the Azure resource of type 'vault' |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | The Get Operation Results operation can be used get the operation status and result for the asynchronously submitted operation |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | The Get Containers operation can be used get the containers registered for a resource. |
> | Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Read any Alerts Settings |
> | Microsoft.RecoveryServices/vaults/replicationEvents/read | Read any Events |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action | Checks Consistency of the Fabric |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/read | Read any Fabrics |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action | Reassociate Gateway |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action | Renew Certificate for Fabric |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Read any Networks |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | Read any Network Mappings |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read | Read any Protection Containers |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | Read any Protectable Items |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action | Apply Recovery Point |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action | Failover Commit |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action | Planned Failover |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | Read any Protected Items |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Read any Replication Recovery Points |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action | Repair replication |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action | ReProtect Protected Item |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action | Test Failover |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action | Test Failover Cleanup |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action | Failover |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action | Update Mobility Service |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | Read any Protection Container Mappings |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Read any Recovery Services Providers |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action | Refresh Provider |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read | Read any Storage Classifications |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | Read any Storage Classification Mappings |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | Read any vCenters |
> | Microsoft.RecoveryServices/vaults/replicationJobs/* | Create and manage replication jobs |
> | Microsoft.RecoveryServices/vaults/replicationPolicies/read | Read any Policies |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCommit/action | Failover Commit Recovery Plan |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action | Planned Failover Recovery Plan |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | Read any Recovery Plans |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action | ReProtect Recovery Plan |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action | Test Failover Recovery Plan |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailoverCleanup/action | Test Failover Cleanup Recovery Plan |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action | Failover Recovery Plan |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/* | Read alerts for the Recovery services vault |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | Microsoft.RecoveryServices/Vaults/storageConfig/read |  |
> | Microsoft.RecoveryServices/Vaults/tokenInfo/read |  |
> | Microsoft.RecoveryServices/Vaults/usages/read | Returns usage details for a Recovery Services Vault. |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | The Vault Token operation can be used to get Vault Token for vault level backend operations. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Gets the availability statuses for all resources in the specified scope |
> | Microsoft.Resources/deployments/* | Create and manage resource group deployments |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Gets or lists resource groups. |
> | Microsoft.Storage/storageAccounts/read | Returns the list of storage accounts or gets the properties for the specified storage account. |
> | Microsoft.Support/* | Create and manage support tickets |
> | **NotActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## Site Recovery Reader
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Lets you view Site Recovery status but not perform other management operations |
> | **Id** | dbaa88c4-0c30-4179-9fb3-46319faa6149 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Read roles and role assignments |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp is internal operation used by service |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | The Get Extended Info operation gets an object's Extended Info representing the Azure resource of type ?vault? |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Gets the alerts for the Recovery services vault. |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | Microsoft.RecoveryServices/Vaults/read | The Get Vault operation gets an object representing the Azure resource of type 'vault' |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | The Get Operation Results operation can be used get the operation status and result for the asynchronously submitted operation |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | The Get Containers operation can be used get the containers registered for a resource. |
> | Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Read any Alerts Settings |
> | Microsoft.RecoveryServices/vaults/replicationEvents/read | Read any Events |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/read | Read any Fabrics |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Read any Networks |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | Read any Network Mappings |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read | Read any Protection Containers |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | Read any Protectable Items |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | Read any Protected Items |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Read any Replication Recovery Points |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | Read any Protection Container Mappings |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Read any Recovery Services Providers |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read | Read any Storage Classifications |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | Read any Storage Classification Mappings |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | Read any vCenters |
> | Microsoft.RecoveryServices/vaults/replicationJobs/read | Read any Jobs |
> | Microsoft.RecoveryServices/vaults/replicationPolicies/read | Read any Policies |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | Read any Recovery Plans |
> | Microsoft.RecoveryServices/Vaults/storageConfig/read |  |
> | Microsoft.RecoveryServices/Vaults/tokenInfo/read |  |
> | Microsoft.RecoveryServices/Vaults/usages/read | Returns usage details for a Recovery Services Vault. |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | The Vault Token operation can be used to get Vault Token for vault level backend operations. |
> | Microsoft.Support/* | Create and manage support tickets |
> | **NotActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## Spatial Anchors Account Contributor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Lets you manage spatial anchors in your account, but not delete them |
> | **Id** | 8bbe83f1-e2a6-4df7-8cb4-4e04d4e5c827 |
> | **Actions** |  |
> | *none* |  |
> | **NotActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/create/action | Create spatial anchors |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read | Discover nearby spatial anchors |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read | Get properties of spatial anchors |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/query/read | Locate spatial anchors |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read | Submit diagnostics data to help improve the quality of the Azure Spatial Anchors service |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/write | Update spatial anchors properties |
> | **NotDataActions** |  |
> | *none* |  |

## Spatial Anchors Account Owner
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Lets you manage spatial anchors in your account, including deleting them |
> | **Id** | 70bbe301-9835-447d-afdd-19eb3167307c |
> | **Actions** |  |
> | *none* |  |
> | **NotActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/create/action | Create spatial anchors |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/delete | Delete spatial anchors |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read | Discover nearby spatial anchors |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read | Get properties of spatial anchors |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/query/read | Locate spatial anchors |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read | Submit diagnostics data to help improve the quality of the Azure Spatial Anchors service |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/write | Update spatial anchors properties |
> | **NotDataActions** |  |
> | *none* |  |

## Spatial Anchors Account Reader
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Lets you locate and read properties of spatial anchors in your account |
> | **Id** | 5d51204f-eb77-4b1c-b86a-2ec626c49413 |
> | **Actions** |  |
> | *none* |  |
> | **NotActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read | Discover nearby spatial anchors |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read | Get properties of spatial anchors |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/query/read | Locate spatial anchors |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read | Submit diagnostics data to help improve the quality of the Azure Spatial Anchors service |
> | **NotDataActions** |  |
> | *none* |  |

## SQL DB Contributor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Lets you manage SQL databases, but not access to them. Also, you can't manage their security-related policies or their parent SQL servers. |
> | **Id** | 9b7fa17d-e63e-47b0-bb0a-15c516ac86ec |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Read roles and role Assignments |
> | Microsoft.Insights/alertRules/* | Create and manage alert rules |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Gets the availability statuses for all resources in the specified scope |
> | Microsoft.Resources/deployments/* | Create and manage resource group deployments |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Gets or lists resource groups. |
> | Microsoft.Sql/locations/*/read |  |
> | Microsoft.Sql/servers/databases/* | Create and manage SQL databases |
> | Microsoft.Sql/servers/read | Return the list of servers or gets the properties for the specified server. |
> | Microsoft.Support/* | Create and manage support tickets |
> | Microsoft.Insights/metrics/read | Read metrics |
> | Microsoft.Insights/metricDefinitions/read | Read metric definitions |
> | **NotActions** |  |
> | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/managedInstances/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/auditingPolicies/* | Edit audit policies |
> | Microsoft.Sql/servers/databases/auditingSettings/* | Edit audit settings |
> | Microsoft.Sql/servers/databases/auditRecords/read | Retrieve the database blob audit records |
> | Microsoft.Sql/servers/databases/connectionPolicies/* | Edit connection policies |
> | Microsoft.Sql/servers/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Edit data masking policies |
> | Microsoft.Sql/servers/databases/extendedAuditingSettings/* |  |
> | Microsoft.Sql/servers/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/securityAlertPolicies/* | Edit security alert policies |
> | Microsoft.Sql/servers/databases/securityMetrics/* | Edit security metrics |
> | Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | Microsoft.Sql/servers/vulnerabilityAssessments/* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## SQL Managed Instance Contributor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Lets you manage SQL Managed Instances and required network configuration, but can’t give access to others. |
> | **Id** | 4939a1f6-9ae0-4e48-a1e0-f2cbe897382d |
> | **Actions** |  |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Gets the availability statuses for all resources in the specified scope |
> | Microsoft.Resources/deployments/* | Create and manage resource group deployments |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Gets or lists resource groups. |
> | Microsoft.Network/networkSecurityGroups/* |  |
> | Microsoft.Network/routeTables/* |  |
> | Microsoft.Sql/locations/*/read |  |
> | Microsoft.Sql/managedInstances/* |  |
> | Microsoft.Support/* | Create and manage support tickets |
> | Microsoft.Network/virtualNetworks/subnets/* |  |
> | Microsoft.Network/virtualNetworks/* |  |
> | Microsoft.Authorization/*/read | Read roles and role assignments |
> | Microsoft.Insights/alertRules/* | Create and manage Insights alert rules |
> | Microsoft.Insights/metrics/read | Read metrics |
> | Microsoft.Insights/metricDefinitions/read | Read metric definitions |
> | **NotActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## SQL Security Manager
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Lets you manage the security-related policies of SQL servers and databases, but not access to them. |
> | **Id** | 056cd41c-7e88-42e1-933e-88ba6a50c9c3 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Read Microsoft authorization |
> | Microsoft.Insights/alertRules/* | Create and manage Insights alert rules |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Joins resource such as storage account or SQL database to a subnet. Not alertable. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Gets the availability statuses for all resources in the specified scope |
> | Microsoft.Resources/deployments/* | Create and manage resource group deployments |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Gets or lists resource groups. |
> | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/managedInstances/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/auditingPolicies/* | Create and manage SQL server auditing policies |
> | Microsoft.Sql/servers/auditingSettings/* | Create and manage SQL server auditing setting |
> | Microsoft.Sql/servers/extendedAuditingSettings/read | Retrieve details of the extended server blob auditing policy configured on a given server |
> | Microsoft.Sql/servers/databases/auditingPolicies/* | Create and manage SQL server database auditing policies |
> | Microsoft.Sql/servers/databases/auditingSettings/* | Create and manage SQL server database auditing settings |
> | Microsoft.Sql/servers/databases/auditRecords/read | Read audit records |
> | Microsoft.Sql/servers/databases/connectionPolicies/* | Create and manage SQL server database connection policies |
> | Microsoft.Sql/servers/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Create and manage SQL server database data masking policies |
> | Microsoft.Sql/servers/databases/extendedAuditingSettings/read | Retrieve details of the extended blob auditing policy configured on a given database |
> | Microsoft.Sql/servers/databases/read | Return the list of databases or gets the properties for the specified database. |
> | Microsoft.Sql/servers/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/schemas/read | Get a database schema. |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/read | Get a database column. |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/schemas/tables/read | Get a database table. |
> | Microsoft.Sql/servers/databases/securityAlertPolicies/* | Create and manage SQL server database security alert policies |
> | Microsoft.Sql/servers/databases/securityMetrics/* | Create and manage SQL server database security metrics |
> | Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | Microsoft.Sql/servers/firewallRules/* |  |
> | Microsoft.Sql/servers/read | Return the list of servers or gets the properties for the specified server. |
> | Microsoft.Sql/servers/securityAlertPolicies/* | Create and manage SQL server security alert policies |
> | Microsoft.Sql/servers/vulnerabilityAssessments/* |  |
> | Microsoft.Support/* | Create and manage support tickets |
> | **NotActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## SQL Server Contributor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Lets you manage SQL servers and databases, but not access to them, and not their security -related policies. |
> | **Id** | 6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Read roles and role assignments |
> | Microsoft.Insights/alertRules/* | Create and manage Insights alert rules |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Gets the availability statuses for all resources in the specified scope |
> | Microsoft.Resources/deployments/* | Create and manage resource group deployments |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Gets or lists resource groups. |
> | Microsoft.Sql/locations/*/read |  |
> | Microsoft.Sql/servers/* | Create and manage SQL servers |
> | Microsoft.Support/* | Create and manage support tickets |
> | Microsoft.Insights/metrics/read | Read metrics |
> | Microsoft.Insights/metricDefinitions/read | Read metric definitions |
> | **NotActions** |  |
> | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/managedInstances/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/auditingPolicies/* | Edit SQL server auditing policies |
> | Microsoft.Sql/servers/auditingSettings/* | Edit SQL server auditing settings |
> | Microsoft.Sql/servers/databases/auditingPolicies/* | Edit SQL server database auditing policies |
> | Microsoft.Sql/servers/databases/auditingSettings/* | Edit SQL server database auditing settings |
> | Microsoft.Sql/servers/databases/auditRecords/read | Read audit records |
> | Microsoft.Sql/servers/databases/connectionPolicies/* | Edit SQL server database connection policies |
> | Microsoft.Sql/servers/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Edit SQL server database data masking policies |
> | Microsoft.Sql/servers/databases/extendedAuditingSettings/* |  |
> | Microsoft.Sql/servers/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/securityAlertPolicies/* | Edit SQL server database security alert policies |
> | Microsoft.Sql/servers/databases/securityMetrics/* | Edit SQL server database security metrics |
> | Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | Microsoft.Sql/servers/extendedAuditingSettings/* |  |
> | Microsoft.Sql/servers/securityAlertPolicies/* | Edit SQL server security alert policies |
> | Microsoft.Sql/servers/vulnerabilityAssessments/* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## Storage Account Contributor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Permits management of storage accounts. Does not provide access to data in the storage account. |
> | **Id** | 17d1049b-9a84-46fb-8f53-869881c3d3ab |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Read all authorization |
> | Microsoft.Insights/alertRules/* | Create and manage Insights alert rules |
> | Microsoft.Insights/diagnosticSettings/* | Manage diagnostic settings |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Joins resource such as storage account or SQL database to a subnet. Not alertable. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Gets the availability statuses for all resources in the specified scope |
> | Microsoft.Resources/deployments/* | Create and manage resource group deployments |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Gets or lists resource groups. |
> | Microsoft.Storage/storageAccounts/* | Create and manage storage accounts |
> | Microsoft.Support/* | Create and manage support tickets |
> | **NotActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## Storage Account Key Operator Service Role
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Permits listing and regenerating storage account access keys. |
> | **Id** | 81a9662b-bebf-436f-a333-f67b29880f12 |
> | **Actions** |  |
> | Microsoft.Storage/storageAccounts/listkeys/action | Return the access keys for the specified storage account. |
> | Microsoft.Storage/storageAccounts/regeneratekey/action | Regenerate the access keys for the specified storage account. |
> | **NotActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## Storage Blob Data Contributor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Read, write, and delete Azure Storage containers and blobs. To learn which actions are required for a given data operation, see [Permissions for calling blob and queue data operations](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-rest-operations). |
> | **Id** | ba92f5b4-2d11-453d-a403-e96b0029c9fe |
> | **Actions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/delete | Delete a container. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/read | Return a container or a list of containers. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/write | Modify a container's metadata or properties. |
> | **NotActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | Delete a blob. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Return a blob or a list of blobs. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | Write to a blob. |
> | **NotDataActions** |  |
> | *none* |  |

## Storage Blob Data Owner
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Provides full access to Azure Storage blob containers and data, including assigning POSIX access control. To learn which actions are required for a given data operation, see [Permissions for calling blob and queue data operations](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-rest-operations). |
> | **Id** | b7e6dc6d-f1e8-4753-8033-0f276bb0955b |
> | **Actions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/* | Full permissions on containers. |
> | **NotActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/* | Full permissions on blobs. |
> | **NotDataActions** |  |
> | *none* |  |

## Storage Blob Data Reader
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Read and list Azure Storage containers and blobs. To learn which actions are required for a given data operation, see [Permissions for calling blob and queue data operations](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-rest-operations). |
> | **Id** | 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1 |
> | **Actions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/read | Return a container or a list of containers. |
> | **NotActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Return a blob or a list of blobs. |
> | **NotDataActions** |  |
> | *none* |  |

## Storage Queue Data Contributor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Read, write, and delete Azure Storage queues and queue messages. To learn which actions are required for a given data operation, see [Permissions for calling blob and queue data operations](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-rest-operations). |
> | **Id** | 974c5e8b-45b9-4653-ba55-5f855dd0fb88 |
> | **Actions** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/delete | Delete a queue. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/read | Return a queue or a list of queues. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/write | Modify queue metadata or properties. |
> | **NotActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete | Delete one or more messages from a queue. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Peek or retrieve one or more messages from a queue. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/write | Add a message to a queue. |
> | **NotDataActions** |  |
> | *none* |  |

## Storage Queue Data Message Processor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Peek, retrieve, and delete a messages from an Azure Storage queue. To learn which actions are required for a given data operation, see [Permissions for calling blob and queue data operations](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-rest-operations). |
> | **Id** | 8a0f0c08-91a1-4084-bc3d-661d67233fed |
> | **Actions** |  |
> | *none* |  |
> | **NotActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Peek a message. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action | Retrieve and delete a message. |
> | **NotDataActions** |  |
> | *none* |  |

## Storage Queue Data Message Sender
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Add messages to an Azure Storage queue. To learn which actions are required for a given data operation, see [Permissions for calling blob and queue data operations](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-rest-operations). |
> | **Id** | c6a89b2d-59bc-44d0-9896-0f6e12d7b80a |
> | **Actions** |  |
> | *none* |  |
> | **NotActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action | Add a message to a queue. |
> | **NotDataActions** |  |
> | *none* |  |

## Storage Queue Data Reader
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Read and list Azure Storage queues and queue messages. To learn which actions are required for a given data operation, see [Permissions for calling blob and queue data operations](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-rest-operations). |
> | **Id** | 19e7f393-937e-4f77-808e-94535e297925 |
> | **Actions** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/read | Returns a queue or a list of queues. |
> | **NotActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Peek or retrieve one or more messages from a queue. |
> | **NotDataActions** |  |
> | *none* |  |

## Support Request Contributor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Lets you create and manage Support requests |
> | **Id** | cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Read authorization |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Gets or lists resource groups. |
> | Microsoft.Support/* | Create and manage support tickets |
> | **NotActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## Traffic Manager Contributor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Lets you manage Traffic Manager profiles, but does not let you control who has access to them. |
> | **Id** | a4b10055-b0c7-44c2-b00f-c7b5b3550cf7 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Read roles and role assignments |
> | Microsoft.Insights/alertRules/* | Create and manage Insights alert rules |
> | Microsoft.Network/trafficManagerProfiles/* |  |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Gets the availability statuses for all resources in the specified scope |
> | Microsoft.Resources/deployments/* | Create and manage resource group deployments |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Gets or lists resource groups. |
> | Microsoft.Support/* | Create and manage support tickets |
> | **NotActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## User Access Administrator
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Lets you manage user access to Azure resources. |
> | **Id** | 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9 |
> | **Actions** |  |
> | */read | Read resources of all Types, except secrets. |
> | Microsoft.Authorization/* | Manage authorization |
> | Microsoft.Support/* | Create and manage support tickets |
> | **NotActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## Virtual Machine Administrator Login
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | View Virtual Machines in the portal and login as administrator |
> | **Id** | 1c0163c0-47e6-4577-8991-ea5c82e286e4 |
> | **Actions** |  |
> | Microsoft.Network/publicIPAddresses/read | Gets a public ip address definition. |
> | Microsoft.Network/virtualNetworks/read | Get the virtual network definition |
> | Microsoft.Network/loadBalancers/read | Gets a load balancer definition |
> | Microsoft.Network/networkInterfaces/read | Gets a network interface definition.  |
> | Microsoft.Compute/virtualMachines/*/read |  |
> | **NotActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | Microsoft.Compute/virtualMachines/login/action | Log in to a virtual machine as a regular user |
> | Microsoft.Compute/virtualMachines/loginAsAdmin/action | Log in to a virtual machine with Windows administrator or Linux root user privileges |
> | **NotDataActions** |  |
> | *none* |  |

## Virtual Machine Contributor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Lets you manage virtual machines, but not access to them, and not the virtual network or storage account they're connected to. |
> | **Id** | 9980e02c-c2be-4d73-94e8-173b1dc7cf3c |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Read authorization |
> | Microsoft.Compute/availabilitySets/* | Create and manage compute availability sets |
> | Microsoft.Compute/locations/* | Create and manage compute locations |
> | Microsoft.Compute/virtualMachines/* | Create and manage virtual machines |
> | Microsoft.Compute/virtualMachineScaleSets/* | Create and manage virtual machine scale sets |
> | Microsoft.DevTestLab/schedules/* |  |
> | Microsoft.Insights/alertRules/* | Create and manage Insights alert rules |
> | Microsoft.Network/applicationGateways/backendAddressPools/join/action | Joins an application gateway backend address pool. Not Alertable. |
> | Microsoft.Network/loadBalancers/backendAddressPools/join/action | Joins a load balancer backend address pool. Not Alertable. |
> | Microsoft.Network/loadBalancers/inboundNatPools/join/action | Joins a load balancer inbound NAT pool. Not alertable. |
> | Microsoft.Network/loadBalancers/inboundNatRules/join/action | Joins a load balancer inbound nat rule. Not Alertable. |
> | Microsoft.Network/loadBalancers/probes/join/action | Allows using probes of a load balancer. For example, with this permission healthProbe property of VM scale set can reference the probe. Not alertable. |
> | Microsoft.Network/loadBalancers/read | Gets a load balancer definition |
> | Microsoft.Network/locations/* | Create and manage network locations |
> | Microsoft.Network/networkInterfaces/* | Create and manage network interfaces |
> | Microsoft.Network/networkSecurityGroups/join/action | Joins a network security group. Not Alertable. |
> | Microsoft.Network/networkSecurityGroups/read | Gets a network security group definition |
> | Microsoft.Network/publicIPAddresses/join/action | Joins a public ip address. Not Alertable. |
> | Microsoft.Network/publicIPAddresses/read | Gets a public ip address definition. |
> | Microsoft.Network/virtualNetworks/read | Get the virtual network definition |
> | Microsoft.Network/virtualNetworks/subnets/join/action | Joins a virtual network. Not Alertable. |
> | Microsoft.RecoveryServices/locations/* |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write | Create a backup Protection Intent |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Returns object details of the Protected Item |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | Create a backup Protected Item |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Returns all Protection Policies |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/write | Creates Protection Policy |
> | Microsoft.RecoveryServices/Vaults/read | The Get Vault operation gets an object representing the Azure resource of type 'vault' |
> | Microsoft.RecoveryServices/Vaults/usages/read | Returns usage details for a Recovery Services Vault. |
> | Microsoft.RecoveryServices/Vaults/write | Create Vault operation creates an Azure resource of type 'vault' |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Gets the availability statuses for all resources in the specified scope |
> | Microsoft.Resources/deployments/* | Create and manage resource group deployments |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Gets or lists resource groups. |
> | Microsoft.SqlVirtualMachine/* |  |
> | Microsoft.Storage/storageAccounts/listKeys/action | Returns the access keys for the specified storage account. |
> | Microsoft.Storage/storageAccounts/read | Returns the list of storage accounts or gets the properties for the specified storage account. |
> | Microsoft.Support/* | Create and manage support tickets |
> | **NotActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## Virtual Machine User Login
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | View Virtual Machines in the portal and login as a regular user. |
> | **Id** | fb879df8-f326-4884-b1cf-06f3ad86be52 |
> | **Actions** |  |
> | Microsoft.Network/publicIPAddresses/read | Gets a public ip address definition. |
> | Microsoft.Network/virtualNetworks/read | Get the virtual network definition |
> | Microsoft.Network/loadBalancers/read | Gets a load balancer definition |
> | Microsoft.Network/networkInterfaces/read | Gets a network interface definition.  |
> | Microsoft.Compute/virtualMachines/*/read |  |
> | **NotActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | Microsoft.Compute/virtualMachines/login/action | Log in to a virtual machine as a regular user |
> | **NotDataActions** |  |
> | *none* |  |

## Web Plan Contributor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Lets you manage the web plans for websites, but not access to them. |
> | **Id** | 2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Read authorization |
> | Microsoft.Insights/alertRules/* | Create and manage Insights alert rules |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Gets the availability statuses for all resources in the specified scope |
> | Microsoft.Resources/deployments/* | Create and manage resource group deployments |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Gets or lists resource groups. |
> | Microsoft.Support/* | Create and manage support tickets |
> | Microsoft.Web/serverFarms/* | Create and manage server farms |
> | Microsoft.Web/hostingEnvironments/Join/Action | Joins an App Service Environment |
> | **NotActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## Website Contributor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Lets you manage websites (not web plans), but not access to them. |
> | **Id** | de139f84-1756-47ae-9be6-808fbbe84772 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Read authorization |
> | Microsoft.Insights/alertRules/* | Create and manage Insights alert rules |
> | Microsoft.Insights/components/* | Create and manage Insights components |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Gets the availability statuses for all resources in the specified scope |
> | Microsoft.Resources/deployments/* | Create and manage resource group deployments |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Gets or lists resource groups. |
> | Microsoft.Support/* | Create and manage support tickets |
> | Microsoft.Web/certificates/* | Create and manage website certificates |
> | Microsoft.Web/listSitesAssignedToHostName/read | Get names of sites assigned to hostname. |
> | Microsoft.Web/serverFarms/join/action |  |
> | Microsoft.Web/serverFarms/read | Get the properties on an App Service Plan |
> | Microsoft.Web/sites/* | Create and manage websites (site creation also requires write permissions to the associated App Service Plan) |
> | **NotActions** |  |
> | *none* |  |
> | **DataActions** |  |
> | *none* |  |
> | **NotDataActions** |  |
> | *none* |  |

## Next steps

- [Custom roles for Azure resources](custom-roles.md)
- [Manage access to Azure resources using RBAC and the Azure portal](role-assignments-portal.md)
- [Permissions in Azure Security Center](../security-center/security-center-permissions.md)
