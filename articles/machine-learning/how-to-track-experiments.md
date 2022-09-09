---
title: Log ML experiments & metrics
titleSuffix: Azure Machine Learning
description: Monitor your Azure ML experiments and monitor run metrics to enhance the model creation process. Add logging to your training script using run.log, Run.start_logging, or ScriptRunConfig.
services: machine-learning
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.service: machine-learning
ms.subservice: core
ms.date: 07/30/2020
ms.topic: conceptual
ms.custom: how-to
---

# Enable logging in Azure ML training runs
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

The Azure Machine Learning Python SDK lets you log real-time information using both the default Python logging package and SDK-specific functionality. You can log locally and send logs to your workspace in the portal.

Logs can help you diagnose errors and warnings, or track performance metrics like parameters and model performance. In this article, you learn how to enable logging in the following scenarios:

> [!div class="checklist"]
> * Interactive training sessions
> * Submitting training jobs using ScriptRunConfig
> * Python native `logging` settings
> * Logging from additional sources


> [!TIP]
> This article shows you how to monitor the model training process. If you're interested in monitoring resource usage and events from Azure Machine learning, such as quotas, completed training runs, or completed model deployments, see [Monitoring Azure Machine Learning](monitor-azure-machine-learning.md).

## Data types

You can log multiple data types including scalar values, lists, tables, images, directories, and more. For more information, and Python code examples for different data types, see the [Run class reference page](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py).

## Interactive logging session

Interactive logging sessions are typically used in notebook environments. The method [Experiment.start_logging()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment(class)?view=azure-ml-py#start-logging--args----kwargs-) starts an interactive logging session. Any metrics logged during the session are added to the run record in the experiment. The method [run.complete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#complete--set-status-true-) ends the sessions and marks the run as completed.

The following code snippet uses an interactive logging session to log training parameters and performance metrics with the [run.log()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#log-name--value--description----) method. It also uploads the trained model to a specified output location.

[!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb?name=create_experiment)]

For a complete sample notebook that uses interactive logging, see [Train a model in a notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb).

## ScriptRunConfig logs

In this section, you learn how to add logging code inside of ScriptConfig runs. You can use the [**ScriptRunConfig**](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) class to encapsulate scripts and environments for repeatable runs. You can also use this option to show a visual Jupyter Notebooks widget for monitoring.

This example performs a parameter sweep over alpha values and captures the results using the [run.log()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#log-name--value--description----) method.

1. Create a training script that includes the logging logic, `train.py`.

   [!code-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train.py)]


1. Submit the ```train.py``` script to run in a user-managed environment. The entire script folder is submitted for training.

   [!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb?name=src)]
   [!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb?name=run)]

    The `show_output` parameter turns on verbose logging, which lets you see details from the training process as well as information about any remote resources or compute targets. Use the following code to turn on verbose logging when you submit the experiment.

```python
run = exp.submit(src, show_output=True)
```

You can also use the same parameter in the `wait_for_completion` function on the resulting run.

```python
run.wait_for_completion(show_output=True)
```

For a complete sample notebook that uses ScriptRunConfigs logs, see [Train a model locally](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-local/train-on-local.ipynb).

## Native Python logging

Some logs in the SDK may contain an error that instructs you to set the logging level to DEBUG. To set the logging level, add the following code to your script.

```python
import logging
logging.basicConfig(level=logging.DEBUG)
```

## Additional logging sources

Azure Machine Learning can also log information from other sources during training, such as automated machine learning runs, or Docker containers that run the jobs. These logs aren't documented, but if you encounter problems and contact Microsoft support, they may be able to use these logs during troubleshooting.

For information on logging metrics in Azure Machine Learning designer (preview), see [How to log metrics in the designer (preview)](how-to-track-designer-experiments.md)

## Example notebooks
The following notebooks demonstrate concepts in this article:
* [how-to-use-azureml/training/train-within-notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook)
* [how-to-use-azureml/training/train-on-local](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-local)
* [how-to-use-azureml/track-and-monitor-experiments/logging-api](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/logging-api)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## Next steps

See these articles to learn more on how to use Azure Machine Learning:

* Learn how to [log metrics in Azure Machine Learning designer (preview)](how-to-track-designer-experiments.md).

* See an example of how to register the best model and deploy it in the tutorial, [Train an image classification model with Azure Machine Learning](tutorial-train-models-with-aml.md).
