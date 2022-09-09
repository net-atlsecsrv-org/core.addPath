---
title: (DEPRECATED) Monitor an Azure Container Service cluster with Sysdig
description: Monitor an Azure Container Service cluster with Sysdig.
author: sauryadas
ms.service: container-service
ms.topic: conceptual
ms.date: 08/08/2016
ms.author: saudas
ms.custom: mvc
---

# (DEPRECATED) Monitor an Azure Container Service cluster with Sysdig

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

In this article, we will deploy Sysdig agents to all the agent nodes in your Azure Container Service cluster. You need an account with Sysdig for this configuration. 

## Prerequisites
[Deploy](container-service-deployment.md) and [connect](../container-service-connect.md) a cluster configured by Azure Container Service. Explore the [Marathon UI](container-service-mesos-marathon-ui.md). Go to [https://app.sysdigcloud.com](https://app.sysdigcloud.com) to set up a Sysdig cloud account. 

## Sysdig
Sysdig is a monitoring service that allows you to monitor your containers within your cluster. Sysdig is known to help with troubleshooting but it also has your basic monitoring metrics for CPU, Networking, Memory, and I/O. Sysdig makes it easy to see which containers are working the hardest or essentially using the most memory and CPU. This view is in the “Overview” section, which is currently in beta. 

![Sysdig UI](./media/container-service-monitoring-sysdig/sysdig6.png) 

## Configure a Sysdig deployment with Marathon
These steps will show you how to configure and deploy Sysdig applications to your cluster with Marathon. 

Access your DC/OS UI via [http://localhost:80/](http://localhost:80/) Once in the DC/OS UI navigate to the "Universe", which is on the bottom left and then search for "Sysdig."

![Sysdig in DC/OS Universe](./media/container-service-monitoring-sysdig/sysdig1.png)

Now to complete the configuration you need a Sysdig cloud account or a free trial account. Once you're logged in to the Sysdig cloud website, click on your user name, and on the page you should see your "Access Key." 

![Sysdig API key](./media/container-service-monitoring-sysdig/sysdig2.png) 

Next enter your Access Key into the Sysdig configuration within the DC/OS Universe. 

![Sysdig configuration in the DC/OS Universe](./media/container-service-monitoring-sysdig/sysdig3.png)

Now set the instances to 10000000 so whenever a new node is added to the cluster Sysdig will automatically deploy an agent to that new node. This is an interim solution to make sure Sysdig will deploy to all new agents within the cluster. 

![Sysdig configuration in the DC/OS Universe-instances](./media/container-service-monitoring-sysdig/sysdig4.png)

Once you've installed the package navigate back to the Sysdig UI and you'll be able to explore the different usage metrics for the containers within your cluster. 

You can also install Mesos and Marathon specific dashboards via the
[new dashboard wizard](https://app.sysdigcloud.com/#/dashboards/new).
