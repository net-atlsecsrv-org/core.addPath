---
title: Prepay for compute with reserved capacity - Azure Database for MySQL 
description: Prepay for Azure Database for MySQL compute resources with reserved capacity
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 05/20/2020
---

# Prepay for Azure Database for MySQL compute resources with reserved capacity

Azure Database for MySQL now helps you save money by prepaying for compute resources compared to pay-as-you-go prices. With Azure Database for MySQL reserved capacity, you make an upfront commitment on MySQL server for a one or three year period to get a significant discount on the compute costs. To purchase Azure Database for MySQL reserved capacity, you need to specify the Azure region, deployment type, performance tier, and term. </br>

You do not need to assign the reservation to specific Azure Database for MySQL servers. An already running Azure Database for MySQL or ones that are newly deployed, will automatically get the benefit of reserved pricing. By purchasing a reservation, you are pre-paying for the compute costs for a period of one or three years. As soon as you buy a reservation, the Azure database for MySQL compute charges that match the reservation attributes are no longer charged at the pay-as-you go rates. A reservation does not cover software, networking, or storage charges associated with the MySQL Database server. At the end of the reservation term, the billing benefit expires, and the Azure Database for MySQL are billed at the pay-as-you go price. Reservations do not auto-renew. For pricing information, see the [Azure Database for MySQL reserved capacity offering](https://azure.microsoft.com/pricing/details/mysql/). </br>

You can buy Azure Database for MySQL reserved capacity in the [Azure portal](https://portal.azure.com/). Pay for the reservation [up front or with monthly payments](../cost-management-billing/reservations/monthly-payments-reservations.md). To buy the reserved capacity:

* You must be in the owner role for at least one Enterprise or individual subscription with pay-as-you-go rates.
* For Enterprise subscriptions, **Add Reserved Instances** must be enabled in the [EA portal](https://ea.azure.com/). Or, if that setting is disabled, you must be an EA Admin on the subscription.
* For Cloud Solution Provider (CSP) program, only the admin agents or sales agents can purchase Azure Database for MySQL reserved capacity. </br>

The details on how enterprise customers and Pay-As-You-Go customers are charged for reservation purchases, see [understand Azure reservation usage for your Enterprise enrollment](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea) and [understand Azure reservation usage for your Pay-As-You-Go subscription](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage).


## Determine the right database size before purchase

The size of reservation should be based on the total amount of compute used by the existing or soon-to-be-deployed server within a specific region and using the same performance tier and hardware generation.</br>

For example, let's suppose that you are running one general purpose, Gen5 – 32 vCore MySQL database, and two memory optimized, Gen5 – 16 vCore MySQL databases. Further, let's supposed that you plan to deploy within the next month an additional general purpose, Gen5 – 32 vCore database server, and one memory optimized, Gen5 – 16 vCore database server. Let's suppose that you know that you will need these resources for at least 1 year. In this case, you should purchase a 64 (2x32) vCores, 1 year reservation for single database general purpose - Gen5 and a 48 (2x16 + 16) vCore 1 year reservation for single database memory optimized - Gen5


## Buy Azure Database for MySQL reserved capacity

1. Sign in to the [Azure portal](https://portal.azure.com/).
2. Select **All services** > **Reservations**.
3. Select **Add** and then in the Purchase reservations pane, select **Azure Database for MySQL** to purchase a new reservation for your MySQL databases.
4. Fill-in the required fields. Existing or new databases that match the attributes you select qualify to get the reserved capacity discount. The actual number of your Azure Database for MySQL servers that get the discount depend on the scope and quantity selected.


:::image type="content" source="media/concepts-reserved-pricing/mysql-reserved-price.png" alt-text="Overview of reserved pricing":::


The following table describes required fields.

| Field | Description |
| :------------ | :------- |
| Subscription   | The subscription used to pay for the Azure Database for MySQL reserved capacity reservation. The payment method on the subscription is charged the upfront costs for the Azure Database for MySQL reserved capacity reservation. The subscription type must be an enterprise agreement (offer numbers: MS-AZR-0017P or MS-AZR-0148P) or an individual agreement with pay-as-you-go pricing (offer numbers: MS-AZR-0003P or MS-AZR-0023P). For an enterprise subscription, the charges are deducted from the enrollment's monetary commitment balance or charged as overage. For an individual subscription with pay-as-you-go pricing, the charges are billed to the credit card or invoice payment method on the subscription.
| Scope | The vCore reservation's scope can cover one subscription or multiple subscriptions (shared scope). If you select: </br></br> **Shared**, the vCore reservation discount is applied to Azure Database for MySQL servers running in any subscriptions within your billing context. For enterprise customers, the shared scope is the enrollment and includes all subscriptions within the enrollment. For Pay-As-You-Go customers, the shared scope is all Pay-As-You-Go subscriptions created by the account administrator.</br></br> **Single subscription**, the vCore reservation discount is applied to Azure Database for MySQL servers in this subscription. </br></br> **Single resource group**, the reservation discount is applied to Azure Database for MySQL servers in the selected subscription and the selected resource group within that subscription.
| Region | The Azure region that's covered by the Azure Database for MySQL reserved capacity reservation.
| Deployment Type | The Azure Database for MySQL resource type that you want to buy the reservation for.
| Performance Tier | The service tier for the Azure Database for MySQL servers.
| Term | One year
| Quantity | The amount of compute resources being purchased within the Azure Database for MySQL reserved capacity reservation. The quantity is a number of vCores in the selected Azure region and Performance tier that are being reserved and will get the billing discount. For example, if you are running or planning to run an Azure Database for MySQL servers with the total compute capacity of Gen5 16 vCores in the East US region, then you would specify quantity as 16 to maximize the benefit for all servers.

## Cancel, exchange, or refund reservations

You can cancel, exchange, or refund reservations with certain limitations. For more information, see [Self-service exchanges and refunds for Azure Reservations](https://docs.microsoft.com/azure/billing/billing-azure-reservations-self-service-exchange-and-refund).

## vCore size flexibility

vCore size flexibility helps you scale up or down within a performance tier and region, without losing the reserved capacity benefit. 

## Need help ? Contact us

If you have questions or need help, [create a support request](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## Next steps

The vCore reservation discount is applied automatically to the number of Azure Database for MySQL servers that match the Azure Database for MySQL reserved capacity reservation scope and attributes. You can update the scope of the Azure database for MySQL reserved capacity reservation through Azure portal, PowerShell, CLI or through the API. </br></br>
To learn how to manage the Azure Database for MySQL reserved capacity, see manage Azure Database for MySQL reserved capacity.

To learn more about Azure Reservations, see the following articles:

* [What are Azure Reservations](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations)?
* [Manage Azure Reservations](https://docs.microsoft.com/azure/billing/billing-manage-reserved-vm-instance)
* [Understand Azure Reservations discount](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges)
* [Understand reservation usage for your Pay-As-You-Go subscription](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges-mysql)
* [Understand reservation usage for your Enterprise enrollment](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea)
* [Azure Reservations in Partner Center Cloud Solution Provider (CSP) program](https://docs.microsoft.com/partner-center/azure-reservations)

