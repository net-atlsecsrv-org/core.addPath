---
title: Trigger Azure Machine Learning pipelines
titleSuffix: Azure Machine Learning
description: Triggered pipelines allow you to automate routine, time-consuming tasks such as data processing, training, and monitoring.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: laobri
author: lobrien
ms.date: 12/16/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python

# Customer intent: As a Python coding data scientist, I want to improve my operational efficiency by scheduling my training pipeline of my model using the latest data. 
---

# Trigger machine learning pipelines with Azure Machine Learning SDK for Python

In this article, you'll learn how to programmatically schedule a pipeline to run on Azure. You can choose to create a schedule based on elapsed time or on file-system changes. Time-based schedules can be used to take care of routine tasks, such as monitoring for data drift. Change-based schedules can be used to react to irregular or unpredictable changes, such as new data being uploaded or old data being edited. After learning how to create schedules, you'll learn how to retrieve and deactivate them. Finally, you'll learn how to use an Azure Logic App to allow more complex triggering logic or behavior.

## Prerequisites

* An Azure subscription. If you don’t have an Azure subscription, create a [free account](https://aka.ms/AMLFree).

* A Python environment in which the Azure Machine Learning SDK for Python is installed. For more information, see [Create and manage reusable environments for training and deployment with Azure Machine Learning.](how-to-use-environments.md)

* A Machine Learning workspace with a published pipeline. You can use the one built in [Create and run machine learning pipelines with Azure Machine Learning SDK](how-to-create-your-first-pipeline.md).

## Initialize the workspace & get data

To schedule a pipeline, you'll need a reference to your workspace, the identifier of your published pipeline, and the name of the experiment in which you wish to create the schedule. You can get these values with the following code:

```Python
import azureml.core
from azureml.core import Workspace
from azureml.pipeline.core import Pipeline, PublishedPipeline
from azureml.core.experiment import Experiment

ws = Workspace.from_config()

experiments = Experiment.list(ws)
for experiment in experiments:
    print(experiment.name)

published_pipelines = PublishedPipeline.list(ws)
for published_pipeline in  published_pipelines:
    print(f"{published_pipeline.name},'{published_pipeline.id}'")

experiment_name = "MyExperiment" 
pipeline_id = "aaaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee" 
```

## Create a schedule

To run a pipeline on a recurring basis, you'll create a schedule. A `Schedule` associates a pipeline, an experiment, and a trigger. The trigger can either be a`ScheduleRecurrence` that describes the wait between runs or a Datastore path that specifies a directory to watch for changes. In either case, you'll need the pipeline identifier and the name of the experiment in which to create the schedule.

At the top of your python file, import the `Schedule` and `ScheduleRecurrence` classes:

```python

from azureml.pipeline.core.schedule import ScheduleRecurrence, Schedule
```

### Create a time-based schedule

The `ScheduleRecurrence` constructor has a required `frequency` argument that must be one of the following strings: "Minute", "Hour", "Day", "Week", or "Month". It also requires an integer `interval` argument specifying how many of the `frequency` units should elapse between schedule starts. Optional arguments allow you to be more specific about starting times, as detailed in the [ScheduleRecurrence SDK docs](/python/api/azureml-pipeline-core/azureml.pipeline.core.schedule.schedulerecurrence?preserve-view=true&view=azure-ml-py).

Create a `Schedule` that begins a run every 15 minutes:

```python
recurrence = ScheduleRecurrence(frequency="Minute", interval=15)
recurring_schedule = Schedule.create(ws, name="MyRecurringSchedule", 
                            description="Based on time",
                            pipeline_id=pipeline_id, 
                            experiment_name=experiment_name, 
                            recurrence=recurrence)
```

### Create a change-based schedule

Pipelines that are triggered by file changes may be more efficient than time-based schedules. For instance, you may want to perform a preprocessing step when a file is changed, or when a new file is added to a data directory. You can monitor any changes to a datastore or changes within a specific directory within the datastore. If you monitor a specific directory, changes within subdirectories of that directory will _not_ trigger a run.

To create a file-reactive `Schedule`, you must set the `datastore` parameter in the call to [Schedule.create](/python/api/azureml-pipeline-core/azureml.pipeline.core.schedule.schedule?preserve-view=true&view=azure-ml-py#&preserve-view=truecreate-workspace--name--pipeline-id--experiment-name--recurrence-none--description-none--pipeline-parameters-none--wait-for-provisioning-false--wait-timeout-3600--datastore-none--polling-interval-5--data-path-parameter-name-none--continue-on-step-failure-none--path-on-datastore-none---workflow-provider-none---service-endpoint-none-). To monitor a folder, set the `path_on_datastore` argument.

The `polling_interval` argument allows you to specify, in minutes, the frequency at which the datastore is checked for changes.

If the pipeline was constructed with a [DataPath](/python/api/azureml-core/azureml.data.datapath.datapath?preserve-view=true&view=azure-ml-py) [PipelineParameter](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelineparameter?preserve-view=true&view=azure-ml-py), you can set that variable to the name of the changed file by setting the `data_path_parameter_name` argument.

```python
datastore = Datastore(workspace=ws, name="workspaceblobstore")

reactive_schedule = Schedule.create(ws, name="MyReactiveSchedule", description="Based on input file change.",
                            pipeline_id=pipeline_id, experiment_name=experiment_name, datastore=datastore, data_path_parameter_name="input_data")
```

### Optional arguments when creating a schedule

In addition to the arguments discussed previously, you may set the `status` argument to `"Disabled"` to create an inactive schedule. Finally, the `continue_on_step_failure` allows you to pass a Boolean that will override the pipeline's default failure behavior.

## View your scheduled pipelines

In your Web browser, navigate to Azure Machine Learning. From the **Endpoints** section of the navigation panel, choose **Pipeline endpoints**. This takes you to a list of the pipelines published in the Workspace.

![Pipelines page of AML](./media/how-to-trigger-published-pipeline/scheduled-pipelines.png)

In this page you can see summary information about all the pipelines in the Workspace: names, descriptions, status, and so forth. Drill in by clicking in your pipeline. On the resulting page, there are more details about your pipeline and you may drill down into individual runs.

## Deactivate the pipeline

If you have a `Pipeline` that is published, but not scheduled, you can disable it with:

```python
pipeline = PublishedPipeline.get(ws, id=pipeline_id)
pipeline.disable()
```

If the pipeline is scheduled, you must cancel the schedule first. Retrieve the schedule's identifier from the portal or by running:

```python
ss = Schedule.list(ws)
for s in ss:
    print(s)
```

Once you have the `schedule_id` you wish to disable, run:

```python
def stop_by_schedule_id(ws, schedule_id):
    s = next(s for s in Schedule.list(ws) if s.id == schedule_id)
    s.disable()
    return s

stop_by_schedule_id(ws, schedule_id)
```

If you then run `Schedule.list(ws)` again, you should get an empty list.

## Use Azure Logic Apps for complex triggers 

More complex trigger rules or behavior can be created using an [Azure Logic App](../logic-apps/logic-apps-overview.md).

To use an Azure Logic App to trigger a Machine Learning pipeline, you'll need the REST endpoint for a published Machine Learning pipeline. [Create and publish your pipeline](how-to-create-your-first-pipeline.md). Then find the REST endpoint of your `PublishedPipeline` by using the pipeline ID:

```python
# You can find the pipeline ID in Azure Machine Learning studio

published_pipeline = PublishedPipeline.get(ws, id="<pipeline-id-here>")
published_pipeline.endpoint 
```

## Create a Logic App

Now create an [Azure Logic App](../logic-apps/logic-apps-overview.md) instance. If you wish, [use an integration service environment (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) and [set up a customer-managed key](../logic-apps/customer-managed-keys-integration-service-environment.md) for use by your Logic App.

Once your Logic App has been provisioned, use these steps to configure a trigger for your pipeline:

1. [Create a system-assigned managed identity](../logic-apps/create-managed-service-identity.md) to give the app access to your Azure Machine Learning Workspace.

1. Navigate to the Logic App Designer view and select the Blank Logic App template. 
    > [!div class="mx-imgBorder"]
    > ![Blank template](media/how-to-trigger-published-pipeline/blank-template.png)

1. In the Designer, search for **blob**. Select the **When a blob is added or modified (properties only)** trigger and add this trigger to your Logic App.
    > [!div class="mx-imgBorder"]
    > ![Add trigger](media/how-to-trigger-published-pipeline/add-trigger.png)

1. Fill in the connection info for the Blob storage account you wish to monitor for blob additions or modifications. Select the Container to monitor. 
 
    Choose the **Interval** and **Frequency** to poll for updates that work for you.  

    > [!NOTE]
    > This trigger will monitor the selected Container but will not monitor subfolders.

1. Add an HTTP action that will run when a new or modified blob is detected. Select **+ New Step**, then search for and select the HTTP action.

  > [!div class="mx-imgBorder"]
  > ![Search for HTTP action](media/how-to-trigger-published-pipeline/search-http.png)

  Use the following settings to configure your action:

  | Setting | Value | 
  |---|---|
  | HTTP action | POST |
  | URI |the endpoint to the published pipeline that you found as a [Prerequisite](#prerequisites) |
  | Authentication mode | Managed Identity |

1. Set up your schedule to set the value of any [DataPath PipelineParameters](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-showcasing-datapath-and-pipelineparameter.ipynb) you may have:

    ```json
    "DataPathAssignments": { 
         "input_datapath": { 
                            "DataStoreName": "<datastore-name>", 
                            "RelativePath": "@triggerBody()?['Name']" 
    } 
    }, 
    "ExperimentName": "MyRestPipeline", 
    "ParameterAssignments": { 
    "input_string": "sample_string3" 
    },
    ```

    Use the `DataStoreName` you added to your workspace as a [Prerequisite](#prerequisites).
     
    > [!div class="mx-imgBorder"]
    > ![HTTP settings](media/how-to-trigger-published-pipeline/http-settings.png)

1. Select **Save** and your schedule is now ready.

> [!IMPORTANT]
> If you are using Azure role-based access control (Azure RBAC) to manage access to your pipeline, [set the permissions for your pipeline scenario (training or scoring)](how-to-assign-roles.md#common-scenarios).

## Next steps

In this article, you used the Azure Machine Learning SDK for Python to schedule a pipeline in two different ways. One schedule recurs based on elapsed clock time. The other schedule runs if a file is modified on a specified `Datastore` or within a directory on that store. You saw how to use the portal to examine the pipeline and individual runs. You learned how to disable a schedule so that the pipeline stops running. Finally, you created an Azure Logic App to trigger a pipeline. 

For more information, see:

> [!div class="nextstepaction"]
> [Use Azure Machine Learning Pipelines for batch scoring](tutorial-pipeline-batch-scoring-classification.md)

* Learn more about [pipelines](concept-ml-pipelines.md)
* Learn more about [exploring Azure Machine Learning with Jupyter](samples-notebooks.md)