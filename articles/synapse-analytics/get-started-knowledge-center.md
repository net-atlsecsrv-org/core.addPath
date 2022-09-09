---
title: 'Tutorial: Get started explore the Synapse Knowledge Center' 
description: In this tutorial, you'll learn how to use the Synapse Knowledge Center.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: tutorial
ms.date: 11/16/2020 
---

# Explore the Synapse Knowledge center

In this tutorial, you'll learn how to use the Synapse Studio Knowledge Center.

## Getting to the Knowledge Center

There are two ways of finding the Knowledge Center in Synapse Studio:

  1. In the Home hub, near the top-right of the page click on **Learn**.
  2. In the menu bar at the top, click **?** and then  **Knowledge Center**.

Pick either method and open the **Knowledge Center**.

## Overview

The **Knowledge center** allows you to do three things:
* **Use samples immediately**. If you want a quick example of how Synapse works, choose this option.
* **Browse gallery**. This option lets you link sample data sets and add sample code in the form of SQL scripts, notebooks, and pipelines.
* **Tour Synapse studio**. This option takes you on a brief tour of the basic parts of Synapse Studio. This is useful if you have never used Synapse Studio before.

## Exploring blob storage with serverless SQL pool

1. Go to the **Knowledge center**, click **Use samples immediately**
1. Select **Query data with SQL** 
1. Click **Use samples immediately**
1. It will create a new SQL script.
1. Scroll to the first query (lines 28 to 32) and select the query text
1. Click Run. It will run the text you selected.

## Loading more NYC Taxi Data
1. Go to the **Knowledge Center**, click **Browse gallery** 
1. Select the **SQL scripts** tab at the top
1. Select **Load the New York Taxicab dataset**
1. Under **Inputs**, choose **Select an existing pool** and select **SQLDB1**
1. Click **Open Script**
1. A new SQL script will appear.
1. Click **Run**
1. This will create several tables for all of the NYC Taxi data and load them using the T-SQL COPY command.

## Next steps

* [Get started with Azure Synapse Analytics](get-started.md)
* [Create a workspace](quickstart-create-workspace.md)
* [Use serverless SQL pool](quickstart-sql-on-demand.md)
