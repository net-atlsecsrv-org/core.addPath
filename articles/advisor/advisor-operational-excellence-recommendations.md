---
title: Improve operational excellency with Advisor
description: Use Azure Advisor to optimize and mature your operational excellence for your Azure subscriptions.
ms.topic: article
ms.date: 10/24/2019
---

# Achieve operational excellence by using Azure Advisor

Operational excellence recommendations in Azure Advisor can help you with: 
- Process and workflow efficiency.
- Resource manageability.
- Deployment best practices. 

You can get these recommendations on the **Operational Excellence** tab of the Advisor dashboard.

## Create Azure Service Health alerts to be notified when Azure problems affect you

We recommend that you set up Azure Service Health alerts so you'll be notified when Azure service problems affect you. [Azure Service Health](https://azure.microsoft.com/features/service-health/) is a free service that provides personalized guidance and support when you're affected by an Azure service problem. Advisor identifies subscriptions that don't have alerts configured and recommends configuring them.


## Design your storage accounts to prevent reaching the maximum subscription limit

An Azure region can support a maximum of 250 storage accounts per subscription. After you reach that limit, you won't be able to create storage accounts in that region/subscription combination. Advisor checks your subscriptions and provides recommendations for you to design for fewer storage accounts for any region/subscription that's close to reaching the limit.

## Ensure you have access to Azure cloud experts when you need it

When running a business-critical workload, it's important to have access to technical support when you need it. Advisor identifies potential business-critical subscriptions that don't have technical support included in their support plan. It recommends upgrading to an option that includes technical support.

## Delete and re-create your pool to remove a deprecated internal component

If your pool is using a deprecated internal component, delete and re-create the pool for improved stability and performance.

## Repair invalid log alert rules

Azure Advisor detects alert rules that have invalid queries specified in their condition section. 
You can create log alert rules in Azure Monitor and use them to run analytics queries at specified intervals. The results of the query determine if an alert needs to be triggered. Analytics queries can become invalid over time because of changes in referenced resources, tables, or commands. Advisor recommends that you correct the query in the alert rule to prevent it from being automatically disabled and ensure monitoring coverage of your resources in Azure. [Learn more about troubleshooting alert rules.](https://aka.ms/aa_logalerts_queryrepair)

## Use Azure Policy recommendations

Azure Policy is a service in Azure that you can use to create, assign, and manage policies. These policies enforce rules and effects on your resources. The following Azure Policy recommendations can help you achieve operational excellency: 

**Manage tags.** This policy adds or replaces the specified tag and value when any resource is created or updated. You can remediate existing resources by triggering a remediation task. This policy doesn't modify tags on resource groups.

**Enforce geo-compliance requirements.** This policy enables you to restrict the locations your organization can specify when deploying resources. 

**Specify allowed virtual machine SKUs for deployments.** This policy enables you to specify a set of virtual machine SKUs that your organization can deploy.

**Enforce *Audit VMs that do not use managed disks*.**

**Enable *Inherit a tag from resource groups*.** This policy adds or replaces the specified tag and value from the parent resource group when any resource is created or updated. You can remediate existing resources by triggering a remediation task.

## Next steps

To learn more about Advisor recommendations, see:
* [Introduction to Advisor](advisor-overview.md)
* [Get started](advisor-get-started.md)
* [Advisor cost recommendations](advisor-cost-recommendations.md)
* [Advisor performance recommendations](advisor-performance-recommendations.md)
* [Advisor reliability recommendations](advisor-high-availability-recommendations.md)
* [Advisor security recommendations](advisor-security-recommendations.md)
* [Advisor REST API](/rest/api/advisor/)
