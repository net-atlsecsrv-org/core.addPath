---
title: What is Azure Lighthouse?
description: Azure Lighthouse lets service providers deliver managed services for their customers with higher automation and efficiency at scale.
author: JnHs
ms.author: jenhayes
ms.date: 08/22/2019
ms.topic: overview
ms.service: lighthouse
manager: carmonm
---
# What is Azure Lighthouse?

Azure Lighthouse offers service providers a single control plane to view and manage Azure across all their customers with higher automation, scale, and enhanced governance. With Azure Lighthouse, service providers can deliver managed services using comprehensive and robust management tooling built into the Azure platform. This offering can also benefit enterprise IT organizations managing resources across multiple tenants.

![Overview diagram of Azure Lighthouse](media/azure-lighthouse-overview.jpg)

## Benefits

Azure Lighthouse helps you to profitably and efficiently build and deliver managed services for your customers. The benefits include:

- **Management at scale**: Customer engagement and life-cycle operations to manage customer resources are easier and more scalable.
- **Greater visibility and precision for customers**: Customers whose resources you're managing will have greater visibility into your actions and precise control over the scope they delegate for management, while your IP is preserved.
- **Comprehensive and unified platform tooling**: Our tooling experience addresses key service provider scenarios, including multiple licensing models such as EA, CSP and pay-as-you-go. The new capabilities work with existing tools and APIs, licensing models, and partner programs such as the [Cloud Solution Provider program (CSP)](https://docs.microsoft.com/partner-center/csp-overview). The Azure Lighthouse options you choose can be integrated into your existing workflows and applications, and you can track your impact on customer engagements by [linking your partner ID](https://docs.microsoft.com/azure/billing/billing-partner-admin-link-started).

There are no additional costs associated with using Azure Lighthouse to manage your customers' Azure resources.

## Capabilities

Azure Lighthouse includes multiple ways to help streamline customer engagement and management:

- **Azure delegated resource management**: Manage your customers' Azure resources securely from within your own tenant, without having to switch context and control planes. For more info, see [Azure delegated resource management](./concepts/azure-delegated-resource-management.md).
- **New Azure portal experiences**: View cross-tenant info in the new **My customers** page in the [Azure portal](https://portal.azure.com). A corresponding **Service providers** blade lets your customers view and manage service provider access. For more info, see [View and manage customers](./how-to/view-manage-customers.md) and [View and manage service providers](./how-to/view-manage-service-providers.md).
- **Azure Resource Manager templates**: Perform management tasks more easily, including onboarding customers for Azure delegated resource management. For more info, see our [samples repo](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates) and [Onboard a customer to Azure delegated resource management](how-to/onboard-customer.md).
- **Managed Services offers in Azure Marketplace**: Offer your services to customers through private or public offers, and have them automatically onboarded to Azure delegated resource management, as an alternate to onboarding using Azure Resource Manager templates. For more info, see [Managed services offers in Azure Marketplace](./concepts/managed-services-offers.md).
- **Azure managed applications**: Package and ship applications that are easy for your customers to deploy and use in their own subscriptions. The application is deployed into a resource group that you access from your tenant, letting you manage the service as part of the overall Azure Lighthouse experience. For more info, see [Azure managed applications overview](https://docs.microsoft.com/azure/managed-applications/overview).

> [!NOTE]
> The capabilities described above are currently available in public clouds. For regional availability of individual services, see [Products available by region](https://azure.microsoft.com/global-infrastructure/services/).

## Next steps

- Learn about [Azure delegated resource management](concepts/azure-delegated-resource-management.md).
- Learn about [cross-tenant management experiences](concepts/cross-tenant-management-experience.md).
