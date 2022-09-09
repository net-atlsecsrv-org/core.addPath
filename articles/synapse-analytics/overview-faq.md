---
title: FAQ - Azure Synapse Analytics
description: FAQ for Azure Synapse Analytics
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: overview
ms.date: 10/25/2020
ms.author: saveenr
ms.reviewer: jrasnick
---

# Azure Synapse Analytics frequently asked questions

In this guide, you'll find the most frequently asked questions for Azure Synapse Analytics.

## General

### Q: How can I use RBAC roles to secure my workspace?

A: Azure Synapse introduces a number of roles and scopes to assign them on that will simplify securing your workspace.

Synapse RBAC roles:
* Synapse Administrator
* Synapse SQL Administrator
* Synapse Spark Administrator
* Synapse Contributor (preview)
* Synapse Artifact Publisher (preview)
* Synapse Artifact User (preview)
* Synapse Compute Operator (preview)
* Synapse Credential User (preview)

To secure your Synapse workspace, assign the RBAC Roles to these RBAC scopes:
* Workspaces
* Spark pools
* Integration runtimes
* Linked services
* Credentials

Additionally, with dedicated SQL pools you have all the same security features that you know and love.

### Q: How do I control cont dedicated SQL pools, serverless SQL pools, and serverless Spark pools?

A: As a starting point, Azure Synapse works with the built-in cost analysis and cost alerts available at the Azure subscription level.

- Dedicated SQL pools - you have direct visibility into the cost and control over the cost, because you create and specify the sizes of dedicated SQL pools. You can further control your which users can create or scale dedicated SQL pools with Azure RBAC roles.

- Serverless SQL pools - you have monitoring and cost management controls that let you cap spending at a daily, weekly, and monthly level. [See Cost management for serverless SQL pool](./sql/data-processed.md) for more information. 

- Serverless Spark pools - you can restrict who can create Spark pools with Synapse RBAC roles.  

### Q: Will Synapse workspace support folder organization of objects and granularity at GA?

A: Synapse workspaces supports user-defined folders.

### Q: Can I link more than one Power BI workspace to a single Azure Synapse Workspace?
	
A: Currently, you can only link a single Power BI workspace to an Azure Synapse Workspace. 

### Q: Is Synapse Link to Cosmos DB GA?

A: Synapse Link for Apache Spark is GA. Synapse Link for serverless SQL pool is in Public Preview.

### Q: Does Azure Synapse workspace Support CI/CD? 

A: Yes! All Pipeline artifacts, notebooks, SQL scripts, and Spark job definitions will reside in Git. All pool definitions will be stored in Git as ARM Templates. Dedicated SQL pool objects (schemas, tables, views, etc.) will be managed with database projects with CI/CD support.

## Pipelines

### Q: How do I ensure I know what credential is being used to run a pipeline? 

A: Each activity in a Synapse Pipeline is executed using the credential specified inside the linked service.

### Q: Are SSIS IRs supported in Synapse Integrate?

A: Not at this time. 

### Q: How do I migrate existing pipelines from Azure Data Factory to an Azure Synapse workspace?

A: At this time, you must manually recreate your Azure Data Factory pipelines and related artifacts by exporting the JSON from the original pipeline and importing it into your Synapse workspace.

## Apache Spark

### Q: What is the difference between Apache Spark for Synapse and Apache Spark?

A: Apache Spark for Synapse IS Apache Spark with added support for integrations with other services (AAD, AzureML, etc.) and additional libraries (mssparktuils, Hummingbird) and pre-tuned performance configurations.

Any workload that is currently running on Apache Spark will run on Apache Spark for Azure Synapse without change. 

### Q: What versions of Spark are available?

A: Azure Synapse Apache Spark fully supports Spark 2.4. For a full list of core components and currently supported version see [Apache Spark version support](./spark/apache-spark-version-support.md).

### Q: Is there an equivalent of DButils in Azure Synapse Spark?

A: Yes, Azure Synapse Apache Spark provides the **mssparkutils** library. For full documentation of the utility see [Introduction to Microsoft Spark utilities](./spark/microsoft-spark-utilities.md).

### Q: How do I set session parameters in Apache Spark?

A: To set session parameters, use %%configure magic available. A session restart is required for the parameters to take effect. 

### Q: How do I set cluster level parameters in a serverless Spark pool?

A: To set cluster level parameters, you can provide a spark.conf file for the Spark pool. This pool will then honor the parameters past in the config file. 

### Q: Can I run a multi-user Spark Cluster in Azure Synapse Analytics?
 
A: Azure Synapse provides purpose-built engines for specific use cases. Apache Spark for Synapse is designed as a job service and not a cluster model. 
There are two scenarios where people ask for a multi-user cluster model.

**Scenario #1: Many users accessing a cluster for serving data for BI purposes.**

The easiest way of accomplishing this task is to cook the data with Spark and then take advantage of the serving capabilities of Synapse SQL to that they can connect Power BI to those datasets.

**Scenario #2: Having multiple developers on a single cluster to save money.**
 
To satisfy this scenario, you should give each developer a serverless Spark pool that is set to use a small number of Spark resources. Since serverless Spark pools don’t cost anything, until they are actively used minimizes the cost when there are multiple developers. The pools share metadata (Spark tables) so they can easily work with each other.

### Q: How do I include, manage, and install libraries?

A:  You can install external packages via a requirements.txt file while creating the Spark pool, from the synapse workspace, or from the Azure portal. See [Manage libraries for Apache Spark in Azure Synapse Analytics](./spark/apache-spark-azure-portal-add-libraries.md).

## Dedicated SQL Pools

### Q: What are the functional differences between dedicated SQL pools and serverless pools?

A: You can find a full list of differences in [T-SQL feature differences in Synapse SQL](./sql/overview-features.md).

### Q: Now that Azure Synapse is GA, how do I move my dedicated SQL pools that were previously standalone into Azure Synapse? 

A: There is no “move” or “migration.” 
You can choose to enable new workspace features on your existing pools. If you do, there are no breaking changes, instead you’ll be able to use new features such as Synapse Studio, Spark, and serverless SQL pools.

### Q: What is the default deployment of dedicated SQL pools now? 

A: By Default, all new dedicated SQL pools will be deployed to a workspace; however, if you need to you can still create a dedicated SQL pool (formerly SQL DW) in a standalone form factor. 


### Q: What are the functional differences between dedicated SQL pools and serverless SQL pools?

A: You can find a full list of differences in [T-SQL feature differences in Synapse SQL](./sql/overview-features.md).

## Next steps

* [Get started with Azure Synapse Analytics](get-started.md)
* [Create a workspace](quickstart-create-workspace.md)
* [Use serverless SQL pool](quickstart-sql-on-demand.md)
