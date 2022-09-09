---
title: Publishing guide by offer type - Microsoft commercial marketplace
description: This article describes the offer types that are available in the Microsoft commercial marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 10/06/2020
---

# Publishing guide by offer type

This article describes the offer types that are available in the commercial marketplace. The *offer type* defines the offer structure, which includes the metadata, artifacts, and other content presented in the commercial marketplace.

After you [decide on a publishing option](determine-your-listing-type.md), you must choose an offer type before you start creating your offer in Partner Center. The offer type will correspond to the type of solution, app, or service offer that you wish to publish, as well as its alignment to Microsoft products and services.

You can configure a single offer type in different ways to enable different publishing options, listing option, provisioning, or pricing. The publishing option and configuration of the offer type also align to the offer eligibility and technical requirements.

Be sure to review the online store and offer type eligibility requirements and the technical publishing requirements before creating your offer.

## List of offer types

The following table shows the commercial marketplace offer types in Partner Center.

| **Offer type**    | **Description**  |
| :------------------- | :-------------------|
| [**Azure Application**](plan-azure-application-offer.md) | There are two kinds of Azure application plans: _solution template_ and _managed application_. Both plan types support automating the deployment and configuration of a solution beyond a single virtual machine (VM). You can automate the process of providing multiple resources, including VMs, networking, and storage resources to provide complex solutions, such as IaaS solutions. Both plan types can employ many different kinds of Azure resources, including but not limited to VMs.<ul><li>**Solution template** plans are one of the main ways to publish a solution in the commercial marketplace. Solution template plans are not transactable in the commercial marketplace, but they can be used to deploy paid VM offers that are billed through the commercial marketplace. Use the solution template plan type when the customer will manage the solution and the transactions are billed through another plan.</li><br><li>**Managed application** plans enable you to easily build and deliver fully managed, turnkey applications for your customers. They have the same capabilities as solution template plans, with some key differences:</li><ul><li> The resources are deployed to a resource group and are managed by the publisher of the app. The resource group is present in the consumer's subscription, but an identity in the publisher's tenant has access to the resource group.</li><li>As the publisher, you specify the cost for ongoing support of the solution and transactions are supported through the commercial marketplace.</li></ul>Use the managed application plan type when you or your customer requires that the solution is managed by a partner or you will deploy a subscription-based solution.</ul> |
| [**Azure Container**](marketplace-containers.md) | Use the Azure Container offer type when your solution is a Docker container image provisioned as a Kubernetes-based Azure container service. |
| [**Azure virtual machine**](marketplace-virtual-machines.md) | Use the virtual machine offer type when you deploy a virtual appliance to the subscription associated with your customer. |
| [**Consulting service**](consulting-services.md) | Consulting services help to connect customers with services to support and extend their use of Azure, Dynamics 365, or Power Suite services.|
| [**Dynamics 365**](appsource-offer-publishing-guide.md) | You can publish AppSource offers that build on or extend Dynamics 365 Business Central, Dynamics 365 Customer Engagement, Power Apps, and Finance and Operations apps.|
| [**IoT Edge module**](iot-edge-module.md) | Azure IoT Edge modules are the smallest computation units managed by IoT Edge, and can contain Microsoft services (such as Azure Stream Analytics), 3rd-party services, or your own solution-specific code. |
| [**Managed service**](partner-center-portal/create-new-managed-service-offer.md) | You can create managed service offers and manage customer-delegated subscriptions or resource groups through [Azure Lighthouse](../lighthouse/overview.md).|
| [**Power BI app**<br/>**Microsoft 365**](appsource-offer-publishing-guide.md) | You can publish AppSource offers that build on or extend Power BI and Microsoft 365.|
| [**Software as a Service**](plan-saas-offer.md) | Use the software as a service (SaaS) offer type to enable your customer to buy your SaaS-based, technical solution as a subscription. For information on single sign-on requirements for SaaS offers, see [Azure AD and transactable SaaS offers in the commercial marketplace](azure-ad-saas.md). |


## Next steps

- Review the eligibility requirements in the corresponding article for your offer type to finalize the selection and configuration of your offer.
- Review the publishing patterns for each online store for examples on how your solution maps to an offer type and configuration.