---
title: Getting started with wrangling data flow in Azure Data Factory 
description: A tutorial on how to prepare data in Azure Data Factory using wrangling data flow 
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/19/2021
---

# Prepare data with data wrangling

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Data wrangling in data factory allows you to build interactive Power Query mash-ups natively in ADF and then execute those at scale inside of an ADF pipeline.

> [!NOTE]
> Power Query activity in ADF is currently available in public preview

## Create a Power Query activity

There are two ways to create a Power Query in Azure Data Factory. One way is to click the plus icon and select **Data Flow** in the factory resources pane.

> [!NOTE]
> Previously, the data wrangling feature was located in the data flow workflow. Now, you will build your data wrangling mash-up from ```New > Power query```

![Screenshot that shows Power Query in the factory resources pane.](media/data-flow/power-query-wrangling.png)

The other method is in the activities pane of the pipeline canvas. Open the **Power Query** accordion and drag the **Power Query** activity onto the canvas.

![Screenshot that highlights the data wrangling option.](media/data-flow/power-query-activity.png)

## Author a Power Query data wrangling activity

Add a **Source dataset** for your Power Query mash-up. You can either choose an existing dataset or create a new one. You can also select a sink dataset. You can choose one or more source datasets, but only one sink is allowed at this time. Choosing a sink dataset is optional, but at least one source dataset is required.

![Wrangling](media/wrangling-data-flow/tutorial4.png)

Click **Create** to open the Power Query Online mashup editor.

![Screenshot that shows the Create button that opens the Power Query Online mashup editor.](media/wrangling-data-flow/tutorial5.png)

Author your wrangling Power Query using code-free data preparation. For the list of available functions, see [transformation functions](wrangling-functions.md). ADF translates the M script into a data flow script so that you can execute your Power Query at scale using the Azure Data Factory data flow Spark environment.

![Screenshot that shows the process for authoring your data wrangling Power Query.](media/wrangling-data-flow/tutorial6.png)

## Running and monitoring a Power Query data wrangling activity

To execute a pipeline debug run of a Power Query activity, click **Debug** in the pipeline canvas. Once you publish your pipeline, **Trigger now** executes an on-demand run of the last published pipeline. Power Query pipelines can be schedule with all existing Azure Data Factory triggers.

![Screenshot that shows how to add a Power Query data wrangling activity.](media/wrangling-data-flow/tutorial3.png)

Go to the **Monitor** tab to visualize the output of a triggered Power Query activity run.

![Screenshot that shows the output of a triggered wrangling Power Query activity run.](media/wrangling-data-flow/tutorial2.png)

## Next steps

Learn how to [create a mapping data flow](tutorial-data-flow.md).
