---
title: Save compute costs with reserved capacity
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Learn how to buy Azure SQL Database and SQL Managed Instance reserved capacity to save on your compute costs.
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.custom: sqldbrb=2
ms.devlang:
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: sstein
ms.date: 08/29/2019
---
# Save costs for resources with reserved capacity - Azure SQL Database & SQL Managed Instance
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)] 

Save money with Azure SQL Database and SQL Managed Instance by committing to a reservation for compute resources compared to pay-as-you-go prices. With reserved capacity, you make a commitment for SQL Database and/or SQL Managed Instance use for a period of one or three years to get a significant discount on the compute costs. To purchase reserved capacity, you need to specify the Azure region, deployment type, performance tier, and term.

You do not need to assign the reservation to a specific database or managed instance. Matching existing deployments that are already running or ones that are newly deployed automatically get the benefit. By purchasing a reservation, you commit to usage for the compute costs for a period of one or three years. As soon as you buy a reservation, the compute charges that match the reservation attributes are no longer charged at the pay-as-you go rates. A reservation does not cover software, networking, or storage charges associated with the service. At the end of the reservation term, the billing benefit expires and the database or managed instance is billed at the pay-as-you go price. Reservations do not automatically renew. For pricing information, see the [reserved capacity offering](https://azure.microsoft.com/pricing/details/sql-database/managed/).

You can buy reserved capacity in the [Azure portal](https://portal.azure.com). Pay for the reservation [up front or with monthly payments](../../cost-management-billing/reservations/prepare-buy-reservation.md). To buy reserved capacity:

- You must be in the owner role for at least one Enterprise or individual subscription with pay-as-you-go rates.
- For Enterprise subscriptions, **Add Reserved Instances** must be enabled in the [EA portal](https://ea.azure.com). Or, if that setting is disabled, you must be an EA Admin on the subscription. Reserved capacity.

For more information about how enterprise customers and Pay-As-You-Go customers are charged for reservation purchases, see [Understand Azure reservation usage for your Enterprise enrollment](../../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) and [Understand Azure reservation usage for your Pay-As-You-Go subscription](../../cost-management-billing/reservations/understand-reserved-instance-usage.md).

## Determine correct size before purchase

The size of reservation should be based on the total amount of compute used by the existing or soon-to-be-deployed database or managed instance within a specific region and using the same performance tier and hardware generation.

For example, let's suppose that you are running one general purpose, Gen5 – 16 vCore elastic pool and two business critical Gen5 – 4 vCore single databases. Further, let's supposed that you plan to deploy within the next month an additional general purpose Gen5 – 16 vCore elastic pool and one business critical Gen5 – 32 vCore elastic pool. Also, let's suppose that you know that you will need these resources for at least 1 year. In this case, you should purchase a 32 (2x16) vCores 1-year reservation for single database/elastic pool general purpose - Gen5 and a 40 (2x4 + 32) vCore 1-year reservation for single database/elastic pool business critical - Gen5.

## Buy reserved capacity

1. Sign in to the [Azure portal](https://portal.azure.com).
2. Select **All services** > **Reservations**.
3. Select **Add** and then in the **Purchase Reservations** pane, select **SQL Database** to purchase a new reservation for SQL Database.
4. Fill in the required fields. Existing databases in SQL Database and SQL Managed Instance that match the attributes you select qualify to get the reserved capacity discount. The actual number of databases or managed instances that get the discount depends on the scope and quantity selected.

    ![Screenshot before submitting the reserved capacity purchase](./media/reserved-capacity-overview/sql-reserved-vcores-purchase.png)

    The following table describes required fields.
    
    | Field      | Description|
    |------------|--------------|
    |Subscription|The subscription used to pay for the capacity reservation. The payment method on the subscription is charged the upfront costs for the reservation. The subscription type must be an enterprise agreement (offer number MS-AZR-0017P or MS-AZR-0148P) or an individual agreement with pay-as-you-go pricing (offer number MS-AZR-0003P or MS-AZR-0023P). For an enterprise subscription, the charges are deducted from the enrollment's monetary commitment balance or charged as overage. For an individual subscription with pay-as-you-go pricing, the charges are billed to the credit card or invoice payment method on the subscription.|
    |Scope       |The vCore reservation's scope can cover one subscription or multiple subscriptions (shared scope). If you select <br/><br/>**Shared**, the vCore reservation discount is applied to the database or managed instance running in any subscriptions within your billing context. For enterprise customers, the shared scope is the enrollment and includes all subscriptions within the enrollment. For Pay-As-You-Go customers, the shared scope is all Pay-As-You-Go subscriptions created by the account administrator.<br/><br/>**Single subscription**, the vCore reservation discount is applied to the databases or managed instances in this subscription. <br/><br/>**Single resource group**, the reservation discount is applied to the instances of databases or managed instances in the selected subscription and the selected resource group within that subscription.|
    |Region      |The Azure region that's covered by the capacity reservation.|
    |Deployment Type|The SQL resource type that you want to buy the reservation for.|
    |Performance Tier|The service tier for the databases or managed instances. |
    |Term        |One year or three years.|
    |Quantity    |The amount of compute resources being purchased within the capacity reservation. The quantity is a number of vCores in the selected Azure region and Performance tier that are being reserved and will get the billing discount. For example, if you run or plan to run multiple databases with the total compute capacity of Gen5 16 vCores in the East US region, then you would specify the quantity as 16 to maximize the benefit for all the databases. |

1. Review the cost of the capacity reservation in the **Costs** section.
1. Select **Purchase**.
1. Select **View this Reservation** to see the status of your purchase.

## Cancel, exchange, or refund reservations

You can cancel, exchange, or refund reservations with certain limitations. For more information, see [Self-service exchanges and refunds for Azure Reservations](../../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## vCore size flexibility

vCore size flexibility helps you scale up or down within a performance tier and region, without losing the reserved capacity benefit. Reserved capacity also provides you with the flexibility to temporarily move your hot databases in and out of elastic pools (within the same region and performance tier) as part of your normal operations without losing the reserved capacity benefit. By keeping an unapplied buffer in your reservation, you can effectively manage the performance spikes without exceeding your budget.

## Limitation

You cannot reserve DTU-based (basic, standard, or premium) databases in SQL Database.

## Need help? Contact us

If you have questions or need help, [create a support request](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## Next steps

The vCore reservation discount is applied automatically to the number of databases or managed instances that match the capacity reservation scope and attributes. You can update the scope of the capacity reservation through the [Azure portal](https://portal.azure.com), PowerShell, Azure CLI, or the API.

To learn how to manage the capacity reservation, see [manage reserved capacity](../../cost-management-billing/reservations/manage-reserved-vm-instance.md).

To learn more about Azure Reservations, see the following articles:

- [What are Azure Reservations?](../../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Manage Azure Reservations](../../cost-management-billing/reservations/manage-reserved-vm-instance.md)
- [Understand Azure Reservations discount](../../cost-management-billing/reservations/understand-reservation-charges.md)
- [Understand reservation usage for your Pay-As-You-Go subscription](../../cost-management-billing/reservations/understand-reserved-instance-usage.md)
- [Understand reservation usage for your Enterprise enrollment](../../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
- [Azure Reservations in Partner Center Cloud Solution Provider (CSP) program](https://docs.microsoft.com/partner-center/azure-reservations)
