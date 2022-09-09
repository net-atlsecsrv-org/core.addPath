---
title: Understand reservations discount for Azure SQL Database | Microsoft Docs
description: Learn how a reservation discount is applied to running Azure SQL databases. The discount is applied to these databases on an hourly basis.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 10/13/2020
ms.author: banders
---

# How a reservation discount is applied to Azure SQL Database

After you buy an Azure SQL Database reserved capacity, the reservation discount is automatically applied to SQL databases that match the attributes and quantity of the reservation. A reservation applies to the compute costs of your SQL Database, including the primary replica and any billable secondary replicas. You're charged for software, storage, and networking at the normal rates. You can cover the licensing costs for SQL Database with [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/).

Note that reservation discounts do not apply to Azure SQL Database serverless.

For Reserved Virtual Machine Instances, see [Understand Azure Reserved VM Instances discount](../manage/understand-vm-reservation-charges.md).

## How reservation discount is applied

A reservation discount is "*use-it-or-lose-it*". So, if you don't have matching resources for any hour, then you lose a reservation quantity for that hour. You can't carry forward unused reserved hours.

When you shut down a resource, the reservation discount automatically applies to another matching resource in the specified scope. If no matching resources are found in the specified scope, then the reserved hours are *lost*.

## Discount applied to running SQL databases

The SQL Database reserved capacity discount is applied to running SQL databases on an hourly basis. The reservation that you buy is matched to the compute usage emitted by the running SQL databases. For SQL databases that don't run the full hour, the reservation is automatically applied to other SQL databases matching the reservation attributes. The discount can apply to SQL databases that are running concurrently. If you don't have SQL databases that run for the full hour that match the reservation attributes, you don't get the full benefit of the reservation discount for that hour.

The following examples show how the SQL Database reserved capacity discount applies depending on the number of cores you bought, and when they're running.

- Scenario 1: You buy a SQL Database reserved capacity for an 8 core SQL Database. You run a 16 core SQL Database that matches the rest of the attributes of the reservation. You're charged the pay-as-you-go price for 8 cores of SQL Database compute usage. You get the reservation discount for one hour of 8 core SQL Database compute usage.

For the rest of these examples, assume that the SQL Database reserved capacity you buy is for a 16 core SQL Database and the rest of the reservation attributes match the running SQL databases.

- Scenario 2: You run two SQL databases with 8 cores each for an hour. The 16 core reservation discount is applied to compute usage for both the 8 cores SQL databases.
- Scenario 3: You run one 16 core SQL Database from 1 pm to 1:30 pm. You run another 16 core SQL Database from 1:30 to 2 pm. Both are covered by the reservation discount.
- Scenario 4: You run one 16 core SQL Database from 1 pm to 1:45 pm. You run another 16 core SQL Database from 1:30 to 2 pm. You're charged the pay-as-you-go price for the 15-minute overlap. The reservation discount applies to the compute usage for the rest of the time.
- Scenario 5: You run one 4 core SQL Hyperscale database with three secondary replicas, each having 4 cores. The reservation applies to compute usage for the primary and for all secondary replicas.

To understand and view the application of your Azure Reservations in billing usage reports, see [Understand Azure reservation usage](understand-reserved-instance-usage-ea.md).

## Need help? Contact us

If you have questions or need help,  [create a support request](https://go.microsoft.com/fwlink/?linkid=2083458).

## Next steps

To learn more about Azure Reservations, see the following articles:

- [What are Azure Reservations?](save-compute-costs-reservations.md)
- [Prepay for Virtual Machines with Azure Reserved VM Instances](../../virtual-machines/prepay-reserved-vm-instances.md)
- [Prepay for SQL Database compute resources with Azure SQL Database reserved capacity](../../azure-sql/database/reserved-capacity-overview.md)
- [Manage Azure Reservations](manage-reserved-vm-instance.md)
- [Understand reservation usage for your Pay-As-You-Go subscription](understand-reserved-instance-usage.md)
- [Understand reservation usage for your Enterprise enrollment](understand-reserved-instance-usage-ea.md)
- [Understand reservation usage for CSP subscriptions](/partner-center/azure-reservations)