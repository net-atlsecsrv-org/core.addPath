---
title: "Quickstart: Get started with Azure Machine Learning"
titleSuffix: Azure Machine Learning
description: Get started with Azure Machine Learning.  Set up a workspace and add compute resources to create your development environment.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: quickstart
author: sdgilley
ms.author: sgilley
ms.date: 04/26/2021
adobe-target: true
# Customer intent: As a data scientist, I want to create a workspace so that I can start to use Azure Machine Learning.
---

# Quickstart: Create resources you'll need to get started with Azure Machine Learning

In this quickstart, you'll create a workspace, then add compute resources to the workspace. You'll then have everything you need to get started with Azure Machine Learning.  

The workspace is the top-level resource for your machine learning activities, providing a centralized place to view and manage the artifacts you create when you use Azure Machine Learning. The compute resources provide a pre-configured cloud-based environment you can use to train, deploy, automate, manage, and track machine learning models.


## Prerequisites

- An Azure account with an active subscription. [Create an account for free](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## Create the workspace

If you  already have a workspace, skip this section and continue to [Explore the workspace](#studio).

If you don't yet have a workspace, create one now:

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal.md)]

> [!div class="nextstepaction"]
> [I created a workspace](?success=create-workspace#instance) [I ran into an issue](https://www.research.net/r/7C8Z3DN?issue=create-workspace)


## <a name="instance"></a> Create compute instance

You could install Azure Machine Learning on your own computer.  But in this quickstart, you'll create an online compute resource that has a development environment already installed and ready to go.  You'll use this online machine, a *compute instance*, for your development environment to write and run code in Python scripts and Jupyter notebooks.

Create a *compute instance* to use this development environment for the rest of the tutorials and quickstarts.

1. If you didn't select **Go to workspace** in the previous section, sign in to [Azure Machine Learning studio](https://ml.azure.com) now, and select your workspace.
1. On the left side, under **Manage**, select **Compute**.
1. Select **+New** to create a new compute instance.
1. Keep all the defaults on the first page, select **Next**.
1. Supply a name and select **Create**.
 
In about two minutes, you'll see the **State** of the compute instance change from *Creating* to *Running*.  It's now ready to go.  

> [!div class="nextstepaction"]
> [I created a compute instance](?success=create-instance#cluster) [I ran into an issue](https://www.research.net/r/7C8Z3DN?issue=create-instance)

## <a name="cluster"></a> Create compute clusters

Next you'll create a compute cluster.  Clusters allow you to distribute a training or batch inference process across a cluster of CPU or GPU compute nodes in the cloud.

Create a compute cluster that will autoscale between zero and four nodes:

1. Still in the **Compute** section, in the top tab, select **Compute clusters**.
1. Select **+New** to create a new compute cluster.
1. Keep all the defaults on the first page, select **Next**.
1. Name the cluster **cpu-cluster**.  If this name already exists, add your initials to the name to make it unique.
1. Leave the **Minimum number of nodes** at 0.
1. Change the **Maximum number of nodes** to 4.
1. Change the **Idle seconds before scale down** to 2400.
1. Leave the rest of the defaults, and select **Create**.

In less than a minute, the **State** of the cluster will change from *Creating* to *Succeeded*.  The list shows the provisioned compute cluster, along with the number of idle nodes, busy nodes, and unprovisioned nodes.  Since you haven't used the cluster yet, all the nodes are currently unprovisioned. 

> [!NOTE]
> When the cluster is created, it will have 0 nodes provisioned. The cluster *does not* incur costs until you submit a job. This cluster will scale down when it has been idle for 2,400 seconds (40 minutes).  This will give you time to use it in a few tutorials if you wish without waiting for it to scale back up.

> [!div class="nextstepaction"]
> [I created a compute cluster](?success=create-compute-cluster#clean-up) [I ran into an issue](https://www.research.net/r/7C8Z3DN?issue=create-compute-cluster)

## <a name="studio"></a> Quick tour of the studio

The studio is your web portal for Azure Machine Learning. This portal combines no-code and code-first experiences for an inclusive data science platform.

Review the parts of the studio on the left-hand navigation bar:

* The **Author** section of the studio contains multiple ways to get started in creating machine learning models.  You can:

    * **Notebooks** section allows you to create Jupyter Notebooks, copy sample notebooks, and run notebooks and Python scripts.
    * **Automated ML** steps you though created a machine learning model without writing code.
    * **Designer** gives you a drag-and-drop way to build models using prebuilt modules.

* The **Assets** section of the studio helps you keep track of the assets you create as you run your jobs.  If you have a new workspace, there's nothing in any of these sections yet.

* You already used the **Manage** section of the studio to create your compute resources.  This section also lets you create and manage  data and external services you link to your workspace.  

## <a name="clean-up"></a>Clean up resources

If you plan to continue now to the next tutorial, skip to [Next steps](#next-steps).

### Stop compute instance

If you're not going to use it now, stop the compute instance:

1. In the studio, on the left, select **Compute**.
1. In the top tabs, select **Compute instances**
1. Select the compute instance in the list.
1. On the top toolbar, select **Stop**.

### Delete all resources

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## Next steps

You now have an Azure Machine Learning workspace that contains:

- A compute instance to use for your development environment.
- A compute cluster to use for submitting training runs.

Use these resources to learn more about Azure Machine Learning and train a model with Python scripts.

> [!div class="nextstepaction"]
> [Learn more with Python scripts](tutorial-1st-experiment-hello-world.md)
>
