---
title: Overview - Azure Database for MySQL
description: Learn about the Azure Database for MySQL service, a relational database service in the Microsoft cloud based on the MySQL Community Edition.
author: ajlam
ms.service: mysql
ms.author: andrela
ms.custom: mvc
ms.topic: overview
ms.date: 3/18/2020
---

# What is Azure Database for MySQL?

Azure Database for MySQL is a relational database service in the Microsoft cloud based on the [MySQL Community Edition](https://www.mysql.com/products/community/) (available under the GPLv2 license) database engine, versions 5.6, 5.7, and 8.0. Azure Database for MySQL delivers:

- Built-in high availability with no additional cost.
- Predictable performance, using inclusive pay-as-you-go pricing.
- Scale as needed within seconds.
- Secured to protect sensitive data at-rest and in-motion.
- Automatic backups and point-in-time-restore for up to 35 days.
- Enterprise-grade security and compliance.

These capabilities require almost no administration and all are provided at no additional cost. They allow you to focus on rapid app development and accelerating your time to market rather than allocating precious time and resources to managing virtual machines and infrastructure. In addition, you can continue to develop your application with the open-source tools and platform of your choice to deliver with the speed and efficiency your business demands, all without having to learn new skills.

![Azure Database for MySQL conceptual diagram](media/overview/1-azure-db-for-mysql-conceptual-diagram.png)

This article is an introduction to Azure Database for MySQL core concepts and features related to performance, scalability, and manageability, with links to explore details. See these quickstarts to get you started:

- [Create an Azure Database for MySQL server using Azure portal](quickstart-create-mysql-server-database-using-azure-portal.md)
- [Create an Azure Database for MySQL server using Azure CLI](quickstart-create-mysql-server-database-using-azure-cli.md)

For a set of Azure CLI samples, see:

- [Azure CLI samples for Azure Database for MySQL](sample-scripts-azure-cli.md)

## Automated patching
The service performs automated patching of the underlying hardware, OS, and database engine. The patching includes security and software updates for the underlying hardware, OS and database engine. For MySQL engine, minor version upgrades are automatic and included as part of the patching release. When the community releases a minor version, it is automatically integrated as part of the testing cycle for the service. The testing of the minor version is performed on some of the canonical workloads for MySQL. The minor versions release of MySQL engine is evaluated for reliability (no crashes), availability, security and performance. Not every minor version is released to production in the service but is evaluted based on the criticality of the bug fixes and new incremental value. This is to strike the right balance between new incremental value and minimizing the variables in the system for stability. There is no user action or configuration settings required for patching. The patching frequency is service managed based on the criticality of the payload. In general, the service follows monthly release schedule as part of the continuous integration and release. Users can subscribe to the [planned maintenance notification](concepts-monitoring.md) to receive notification of the upcoming maintenance 72 hours before the event.

## Adjust performance and scale within seconds
The Azure Database for MySQL service offers several service tiers: Basic, General Purpose, and Memory Optimized. Each tier offers different performance and capabilities to support lightweight to heavyweight database workloads. You can build your first app on a small database for a few dollars a month, and then adjust the scale to meet the needs of your solution. Dynamic scalability enables your database to transparently respond to rapidly changing resource requirements. You only pay for the resources you need, and only when you need them. See [Pricing tiers](concepts-service-tiers.md) for details.

## Monitoring and alerting
How do you decide when to dial up and down? You use the built-in performance monitoring and alerting features, combined with the performance ratings based on vCores. Using these tools, you can quickly assess the impact of scaling vCores up or down based on your current or projected performance needs. See [Alerts](howto-alert-on-metric.md) for details.

## Keep your app and business running
Azure's industry leading 99.99% availability service level agreement (SLA), powered by a global network of Microsoft-managed datacenters, helps keep your app running 24/7. With every Azure Database for MySQL server, you take advantage of built-in security, fault tolerance, and data protection that you would otherwise have to buy or design, build, and manage. With Azure Database for MySQL, you can use point-in-time restore to recover a server to an earlier state, as far back as 35 days.

## Secure your data
Azure database services have a tradition of data security that Azure Database for MySQL upholds, with features that limit access, protect data at-rest and in-motion, and help you monitor activity. Visit the [Azure Trust Center](https://www.microsoft.com/trustcenter/security) for information about Azure's platform security. For more information about Azure Database for MySQL security features, see the [security overview](concepts-security.md).

## Contacts
For any questions or suggestions you might have about working with Azure Database for MySQL, send an email to the Azure Database for MySQL Team ([@Ask Azure DB for MySQL](mailto:AskAzureDBforMySQL@service.microsoft.com)). This email address is not a technical support alias.

In addition, consider the following points of contact as appropriate:

- To contact Azure Support, [file a ticket from the Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- To fix an issue with your account, file a [support request](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) in the Azure portal.
- To provide feedback or to request new features, create an entry via [UserVoice](https://feedback.azure.com/forums/597982-azure-database-for-mysql).

## Next steps
Now that you've read an introduction to Azure Database for MySQL and answered the question "What is Azure Database for MySQL?" you're ready to:

- See the pricing page for cost comparisons and calculators. [Pricing](https://azure.microsoft.com/pricing/details/mysql/)
- Get started by creating your first server. [Create an Azure Database for MySQL server using Azure portal](quickstart-create-mysql-server-database-using-azure-portal.md)
- Build your first app using your preferred language:
  - [Python](./connect-python.md)
  - [Node.JS](./connect-nodejs.md)
  - [Java](./connect-java.md)
  - [Ruby](./connect-ruby.md)
  - [PHP](./connect-php.md)
  - [.NET (C#)](./connect-csharp.md)
  - [Go](./connect-go.md)
