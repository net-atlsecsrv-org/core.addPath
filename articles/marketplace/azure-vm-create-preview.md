---
title: Add a preview audience for a virtual machine offer on Azure Marketplace
description: Learn how to add a preview audience for a virtual machine offer on Azure Marketplace.
ms.service: marketplace 
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: mingshen-ms 
ms.author: mingshen
ms.date: 10/19/2020
---

# How to add a preview audience for a virtual machine offer

On the **Preview** page (select from the left-nav menu in Partner Center), select a limited **Preview audience** for validating your offer before you publish it live to the broader commercial marketplace audience.

> [!IMPORTANT]
> After checking your offer on the **Preview** pane, select **Go live** to publish your offer for the commercial marketplace public audience.

Your preview audience is identified by **Azure subscription ID** GUIDs, along with an optional **Description** for each. Neither of these fields can be seen by customers. You can find your Azure subscription ID on the **Subscriptions** page in the Azure portal.

Add at least one Azure subscription ID, either individually (up to 10 IDs) or by uploading a CSV file (up to 100 IDs). By adding these subscription IDs, you define who can preview your offer before it is published live. If your offer is already live, you can still define a preview audience for testing offer changes or updates to your offer.

> [!NOTE]
> A preview audience differs from a private audience. A preview audience can access your offer *before* it's published live on Azure Marketplace. The preview audience can see and validate all plans, including those that will be available only to a private audience after your offer is fully published to Azure Marketplace. A private audience (defined on the plan **Pricing and Availability** pane) has exclusive access to a particular plan.

If you made changes, select **Save draft** before continuing to the next tab in the left-nav menu, **Plan overview**.

## Next steps

- [Create plans](azure-vm-create-plans.md)
