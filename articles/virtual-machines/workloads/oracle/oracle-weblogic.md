---
title: What are solutions for running Oracle WebLogic Server on Azure Virtual Machines
description: Learn how to run Oracle WebLogic Server on Microsoft Azure Virtual Machines.
author: rezar
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 09/23/2020
ms.author: rezar
ms.reviewer: cynthn

---
# What are solutions for running Oracle WebLogic Server on Azure Virtual Machines?

This page describes the solutions for running Oracle WebLogic Server (WLS) on Azure virtual machines. These solutions are jointly developed and supported by Oracle and Microsoft.

WLS is a leading Java application server running some of the most mission critical enterprise Java applications across the globe. WLS forms the middleware foundation for the Oracle software suite. Oracle and Microsoft are committed to empowering WLS customers with choice and flexibility to run workloads on Azure as a leading cloud platform.

The Azure WLS solutions are aimed at making it as easy as possible to lift and shift your Java EE applications to Azure virtual machines by automating most boilerplate operations. The solutions automatically provision virtual network, storage, Java, and Linux resources. With minimal effort, WebLogic Server is installed. The solutions can set up security with a network security group, load balancing with Azure App Gateway and authentication with Azure Active Directory. You may also automatically connect to your existing database including Azure PostgreSQL, Azure SQL, and Oracle DB on the Oracle Cloud or Azure. The road map for the solutions includes the ability to enable distributed logging and distributed caching via Oracle Coherence. Microsoft and Oracle are partnering to enable similar functionality for WebLogic and the Azure Kubernetes Service (AKS).

:::image type="content" source="media/oracle-weblogic/wls-on-azure.gif" alt-text="You can use the Azure portal to deploy WebLogic Server on Azure":::

There are four offers available to meet different scenarios: [single node without an admin server](https://portal.azure.com/#create/oracle.20191001-arm-oraclelinux-wls20191001-arm-oraclelinux-wls), [single node with an admin server](https://portal.azure.com/#create/oracle.20191009-arm-oraclelinux-wls-admin20191009-arm-oraclelinux-wls-admin), [cluster](https://portal.azure.com/#create/oracle.20191007-arm-oraclelinux-wls-cluster20191007-arm-oraclelinux-wls-cluster), and [dynamic cluster](https://portal.azure.com/#create/oracle.20191021-arm-oraclelinux-wls-dynamic-cluster20191021-arm-oraclelinux-wls-dynamic-cluster). The offers are available free of charge. These offers are described and linked below.

_These offers are Bring-Your-Own-License_. They assume you've already got the appropriate licenses with Oracle and are properly licensed to run offers in Azure.

The offers support a range of operating system, Java, and WLS versions through base images (such as WebLogic Server 14 and JDK 11 on Oracle Linux 7.6). These base images are also available on Azure on their own. The base images are suitable for customers that require complex, customized Azure deployments. The current set of base images is available [here](https://azuremarketplace.microsoft.com/en-us/marketplace/apps?search=WebLogic%20Server%20Base%20Image&page=1).

_If you're interested in working closely on your migration scenarios with the engineering team developing these offers, select the [CONTACT ME](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/oracle.oraclelinux-wls-cluster?tab=Overview) button_ on the [marketplace offer overview page](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/oracle.oraclelinux-wls-cluster?tab=Overview). Program managers, architects, and engineers will reach back out to you shortly and start close collaboration. The opportunity to collaborate on a migration scenario is free while the offers are under active initial development.

## Oracle WebLogic Server Single Node

[This offer](https://portal.azure.com/#create/oracle.20191001-arm-oraclelinux-wls20191001-arm-oraclelinux-wls) provisions a single virtual machine and installs WLS on it. It doesn't create a domain or start the administration server. The single node offer is useful for scenarios with highly customized domain configuration.

## Oracle WebLogic Server with Admin Server

[This offer](https://portal.azure.com/#create/oracle.20191009-arm-oraclelinux-wls-admin20191009-arm-oraclelinux-wls-admin) provisions a single virtual machine and installs WLS on it. It creates a domain and starts up the administration server. You can manage the domain and get started with application deployments right away.

## Oracle WebLogic Server Cluster

[This offer](https://portal.azure.com/#create/oracle.20191007-arm-oraclelinux-wls-cluster20191007-arm-oraclelinux-wls-cluster) creates a highly available cluster of WLS virtual machines. The administration server and all managed servers are started by default. You can manage the cluster and get started with highly available applications right away.

## Oracle WebLogic Server Dynamic Cluster

[This offer](https://portal.azure.com/#create/oracle.20191021-arm-oraclelinux-wls-dynamic-cluster20191021-arm-oraclelinux-wls-dynamic-cluster) creates a highly available and scalable dynamic cluster of WLS virtual machines. The administration server and all managed servers are started by default.

The solutions will enable a wide range of production-ready deployment architectures with relative ease. You can meet a majority of migration cases in the most productive way possible by allowing a focus on business application development.

:::image type="content" source="media/oracle-weblogic/weblogic-architecture-vms.png" alt-text="Complex WebLogic Server deployments are enabled on Azure":::

Beyond what is automatically provisioned by the solutions, customers have complete flexibility to customize their deployments further. It's likely on top of deploying applications customers will integrate further Azure resources with their deployments. Customers are encouraged to provide feedback on further improving the solutions.

## Next steps

Explore the offers on Azure.

> [!div class="nextstepaction"]
> [Oracle WebLogic Server Single Node](https://portal.azure.com/#create/oracle.20191001-arm-oraclelinux-wls20191001-arm-oraclelinux-wls)

> [!div class="nextstepaction"]
> [Oracle WebLogic Server with Admin Server](https://portal.azure.com/#create/oracle.20191009-arm-oraclelinux-wls-admin20191009-arm-oraclelinux-wls-admin)

> [!div class="nextstepaction"]
> [Oracle WebLogic Server Cluster](https://portal.azure.com/#create/oracle.20191007-arm-oraclelinux-wls-cluster20191007-arm-oraclelinux-wls-cluster)

> [!div class="nextstepaction"]
> [Oracle WebLogic Server Dynamic Cluster](https://portal.azure.com/#create/oracle.20191021-arm-oraclelinux-wls-dynamic-cluster20191021-arm-oraclelinux-wls-dynamic-cluster)
