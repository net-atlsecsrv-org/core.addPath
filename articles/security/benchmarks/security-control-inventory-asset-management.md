---
title: Azure Security Control - Inventory and Asset Management
description: Azure Security Control Inventory and Asset Management
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark

---

# Security Control: Inventory and Asset Management

Inventory and Asset Management recommendations focus on addressing issues related to actively managing (inventory, track, and correct) all Azure resources so that only authorized resources are given access, and unauthorized and unmanaged resources are identified and removed.

## 6.1: Use automated Asset Discovery solution

| Azure ID | CIS IDs | Responsibility |
|--|--|--|
| 6.1 | 1.1, 1.2, 1.3, 1.4, 9.1, 12.1 | Customer |

Use Azure Resource Graph to query/discover all resources (such as compute, storage, network, ports, and protocols etc.) within your subscription(s).  Ensure appropriate (read) permissions in your tenant and enumerate all Azure subscriptions as well as resources within your subscriptions.

Although classic Azure resources may be discovered via Resource Graph, it is highly recommended to create and use Azure Resource Manager resources going forward.

- [How to create queries with Azure Resource Graph](../../governance/resource-graph/first-query-portal.md)

- [How to view your Azure Subscriptions](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

- [Understand Azure RBAC](../../role-based-access-control/overview.md)

## 6.2: Maintain asset metadata

| Azure ID | CIS IDs | Responsibility |
|--|--|--|
| 6.2 | 1.5 | Customer |

Apply tags to Azure resources giving metadata to logically organize them into a taxonomy.

- [How to create and use Tags](../../azure-resource-manager/management/tag-resources.md)

## 6.3: Delete unauthorized Azure resources

| Azure ID | CIS IDs | Responsibility |
|--|--|--|
| 6.3 | 1.6 | Customer |

Use tagging, management groups, and separate subscriptions, where appropriate, to organize and track assets. Reconcile inventory on a regular basis and ensure unauthorized resources are deleted from the subscription in a timely manner.

- [How to create additional Azure subscriptions](../../cost-management-billing/manage/create-subscription.md)

- [How to create Management Groups](../../governance/management-groups/create-management-group-portal.md)

- [How to create and use Tags](../../azure-resource-manager/management/tag-resources.md)

## 6.4: Define and Maintain an inventory of approved Azure resources

| Azure ID | CIS IDs | Responsibility |
|--|--|--|
| 6.4 | 2.1 | Customer |

Create an inventory of approved Azure resources and approved software for compute resources as per our organizational needs.

## 6.5: Monitor for unapproved Azure resources

| Azure ID | CIS IDs | Responsibility |
|--|--|--|
| 6.5 | 2.3, 2.4 | Customer |

Use Azure Policy to put restrictions on the type of resources that can be created in your subscription(s).

Use Azure Resource Graph to query/discover resources within their subscription(s).  Ensure that all Azure resources present in the environment are approved.

- [How to configure and manage Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

- [How to create queries with Azure Graph](../../governance/resource-graph/first-query-portal.md)

## 6.6: Monitor for unapproved software applications within compute resources

| Azure ID | CIS IDs | Responsibility |
|--|--|--|
| 6.6 | 2.3, 2.4 | Customer |

Use Azure virtual machine Inventory to automate the collection of information about all software on Virtual Machines. Software Name, Version, Publisher, and Refresh time are available from the Azure portal. To get access to install date and other information, enable guest-level diagnostics and bring the Windows Event Logs into a Log Analytics Workspace.

- [How to enable Azure virtual machine Inventory](../../automation/automation-tutorial-installed-software.md)

## 6.7: Remove unapproved Azure resources and software applications

| Azure ID | CIS IDs | Responsibility |
|--|--|--|
| 6.7 | 2.5 | Customer |

Use Azure Security Center's File Integrity Monitoring (Change Tracking) and virtual machine inventory to identify all software installed on Virtual Machines. You can implement your own process for removing unauthorized software. You can also use a third party solution to identify unapproved software.

- [How to use File Integrity Monitoring](../../security-center/security-center-file-integrity-monitoring.md)

- [Understand Azure Change Tracking](../../automation/change-tracking/overview.md)

- [How to enable Azure virtual machine inventory](../../automation/automation-tutorial-installed-software.md)

## 6.8: Use only approved applications

| Azure ID | CIS IDs | Responsibility |
|--|--|--|
| 6.8 | 2.6 | Customer |

Use Azure Security Center Adaptive Application Controls to ensure that only authorized software executes and all unauthorized software is blocked from executing on Azure Virtual Machines.

- [How to use Azure Security Center Adaptive Application Controls](../../security-center/security-center-adaptive-application.md)

## 6.9: Use only approved Azure services

| Azure ID | CIS IDs | Responsibility |
|--|--|--|
| 6.9 | 2.6 | Customer |

Use Azure Policy to restrict which services you can provision in your environment.

- [How to configure and manage Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

- [How to deny a specific resource type with Azure Policy](../../governance/policy/samples/index.md)

## 6.10: Maintain an inventory of approved software titles

| Azure ID | CIS IDs | Responsibility |
|--|--|--|
| 6.10 | 2.7 | Customer |

Use Azure Security Center Adaptive Application Controls to specify which file types a rule may or may not apply to.

Implement third party solution if this does not meet the requirement.

- [How to use Azure Security Center Adaptive Application Controls](../../security-center/security-center-adaptive-application.md)

## 6.11: Limit users' ability to interact with Azure Resource Manager

| Azure ID | CIS IDs | Responsibility |
|--|--|--|
| 6.11 | 2.9 | Customer |

Use Azure Conditional Access to limit users' ability to interact with Azure Resources Manager by configuring "Block access" for the "Microsoft Azure Management" App.

- [How to configure Conditional Access to block access to Azure Resources Manager](../../role-based-access-control/conditional-access-azure-management.md)

## 6.12: Limit users' ability to execute scripts within compute resources

| Azure ID | CIS IDs | Responsibility |
|--|--|--|
| 6.12 | 2.9 | Customer |

Depending on the type of scripts, you may use operating system specific configurations or third-party resources to limit users' ability to execute scripts within Azure compute resources.  You can also leverage Azure Security Center Adaptive Application Controls to ensure that only authorized software executes and all unauthorized software is blocked from executing on Azure Virtual Machines.

- [How to control PowerShell script execution in Windows Environments](/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6)

- [How to use Azure Security Center Adaptive Application Controls](../../security-center/security-center-adaptive-application.md)

## 6.13: Physically or logically segregate high risk applications

| Azure ID | CIS IDs | Responsibility |
|--|--|--|
| 6.13 | 2.9 | Customer |

Software that is required for business operations, but may incur higher risk for the organization, should be isolated within its own virtual machine and/or virtual network and sufficiently secured with either an Azure Firewall or Network Security Group.

- [How to create a virtual network](../../virtual-network/quick-create-portal.md)

- [How to create an NSG with a security config](../../virtual-network/tutorial-filter-network-traffic.md)


## Next steps

- See the next Security Control: [Secure Configuration](security-control-secure-configuration.md)