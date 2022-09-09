---
title: 'Build & train models'
titleSuffix: Azure Machine Learning
description: Learn the different methods you can use to train model with Azure Machine Learning. Estimators provide an easy way to work with popular frameworks like Scikit-learn, TensorFlow, Keras, PyTorch, and Chainer. Machine Learning pipelines make it easy to schedule unattended runs, use heterogeneous compute environments, and reuse parts of your workflow. And run configurations provide granular control over the compute targets that the training process runs on.
services: machine-learning
ms.service: machine-learning
author: Blackmist
ms.author: larryfr
ms.subservice: core
ms.topic: conceptual
ms.date: 05/13/2020
ms.custom: tracking-python
---

# Train models with Azure Machine Learning

Azure Machine Learning provides several ways to train your models, from code first solutions using the SDK to low code solutions such as automated machine learning and the visual designer. Use the following list to determine which training method is right for you:

+ [Azure Machine Learning SDK for Python](#python-sdk): The Python SDK provides several ways to train models, each with different capabilities.

    | Training method | Description |
    | ----- | ----- |
    | [Run configuration](#run-configuration) | A **generic way to train models** is to use a training script and run configuration. The run configuration provides the information needed to configure the training environment used to train your model. You can take a run configuration, your training script, and a compute target (the training environment) and run a training job. |
    | [Automated machine learning](#automated-machine-learning) | Automated machine learning allows you to **train models without extensive data science or programming knowledge**. For people with a data science and programming background, it provides a way to save time and resources by automating algorithm selection and hyperparameter tuning. You don't have to worry about defining a run configuration when using automated machine learning. |
    | [Estimators](#estimators) | Estimator classes **make it easy to train models based on popular machine learning frameworks**. There are estimator classes for **Scikit-learn**, **PyTorch**, **TensorFlow**, **Chainer**, and **Ray RLlib**. There is also a generic estimator that can be used with frameworks that do not already have a dedicated estimator class. You don't have to worry about defining a run configuration when using estimators. |
    | [Machine learning pipeline](#machine-learning-pipeline) | Pipelines are not a different training method, but a **way of defining a workflow using modular, reusable steps**, that can include training as part of the workflow. Machine learning pipelines support using automated machine learning, estimators, and run configuration to train models. Since pipelines are not focused specifically on training, the reasons for using a pipeline are more varied than the other training methods. Generally, you might use a pipeline when:<br>* You want to **schedule unattended processes** such as long running training jobs or data preparation.<br>* Use **multiple steps** that are coordinated across heterogeneous compute resources and storage locations.<br>* Use the pipeline as a **reusable template** for specific scenarios, such as retraining or batch scoring.<br>* **Track and version data sources, inputs, and outputs** for your workflow.<br>* Your workflow is **implemented by different teams that work on specific steps independently**. Steps can then be joined together in a pipeline to implement the workflow. |

+ [Azure Machine Learning SDK for R](#r-sdk): The SDK for R uses the reticulate package to bind to Azure Machine Learning's Python SDK. This allows you access to core objects and methods implemented in the Python SDK from any R environment.

+ **Designer**: Azure Machine Learning designer (preview) provides an easy entry-point into machine learning for building proof of concepts, or for users with little coding experience. It allows you to train models using a drag and drop web-based UI. You can use Python code as part of the design, or train models without writing any code.

+ **CLI**: The machine learning CLI provides commands for common tasks with Azure Machine Learning, and is often used for **scripting and automating tasks**. For example, once you've created a training script or pipeline, you might use the CLI to start a training run on a schedule or when the data files used for training are updated. For training models, it provides commands that submit training jobs. It can submit jobs using run configurations or pipelines.

Each of these training methods can use different types of compute resources for training. Collectively, these resources are referred to as [__compute targets__](concept-azure-machine-learning-architecture.md#compute-targets). A compute target can be a local machine or a cloud resource, such as an Azure Machine Learning Compute, Azure HDInsight, or a remote virtual machine.

## Python SDK

The Azure Machine Learning SDK for Python allows you to build and run machine learning workflows with Azure Machine Learning. You can interact with the service from an interactive Python session, Jupyter Notebooks, Visual Studio Code, or other IDE.

* [What is the Azure Machine Learning SDK for Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
* [Install/update the SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)
* [Configure a development environment for Azure Machine Learning](how-to-configure-environment.md)

### Run configuration

A generic training job with Azure Machine Learning can be defined using the [RunConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py). The run configuration is then used, along with your training script(s) to train a model on a compute target.

You may start with a run configuration for your local computer, and then switch to one for a cloud-based compute target as needed. When changing the compute target, you only change the run configuration you use. A run also logs information about the training job, such as the inputs, outputs, and logs.

* [What is a run configuration?](concept-azure-machine-learning-architecture.md#run-configurations)
* [Tutorial: Train your first ML model](tutorial-1st-experiment-sdk-train.md)
* [Examples: Jupyter Notebook examples of training models](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training)
* [How to: Set up and use compute targets for model training](how-to-set-up-training-targets.md)

### Automated Machine Learning

Define the iterations, hyperparameter settings, featurization, and other settings. During training, Azure Machine Learning tries different algorithms and parameters in parallel. Training stops once it hits the exit criteria you defined. You don't have to worry about defining a run configuration when using estimators.

> [!TIP]
> In addition to the Python SDK, you can also use Automated ML through [Azure Machine Learning studio](https://ml.azure.com).

* [What is automated machine learning?](concept-automated-ml.md)
* [Tutorial: Create your first classification model with automated machine learning](tutorial-first-experiment-automated-ml.md)
* [Tutorial: Use automated machine learning to predict taxi fares](tutorial-auto-train-models.md)
* [Examples: Jupyter Notebook examples for automated machine learning](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning)
* [How to: Configure automated ML experiments in Python](how-to-configure-auto-train.md)
* [How to: Autotrain a time-series forecast model](how-to-auto-train-forecast.md)
* [How to: Create, explore, and deploy automated machine learning experiments with Azure Machine Learning studio](how-to-use-automated-ml-for-ml-models.md)

### Estimators

Estimators make it easy to train models using popular ML frameworks. If you're using **Scikit-learn**, **PyTorch**, **TensorFlow**, **Chainer**, or **Ray RLlib** you should consider using an estimator for training. There is also a generic estimator that can be used with frameworks that do not already have a dedicated estimator class. You don't have to worry about defining a run configuration when using estimators.

* [What are estimators?](concept-azure-machine-learning-architecture.md#estimators)
* [Tutorial: Train image classification models with MNIST data and scikit-learn using Azure Machine Learning](tutorial-train-models-with-aml.md)
* [Examples: Jupyter Notebook examples of using estimators](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning)
* [How to: Create estimators in training](how-to-train-ml-models.md)

### Machine learning pipeline

Machine learning pipelines can use the previously mentioned training methods (run configuration, estimators, and automated machine learning). Pipelines are more about creating a workflow, so they encompass more than just the training of models. In a pipeline, you can train a model using automated machine learning, estimators, or run configurations.

* [What are ML pipelines in Azure Machine Learning?](concept-ml-pipelines.md)
* [Create and run machine learning pipelines with Azure Machine Learning SDK](how-to-create-your-first-pipeline.md)
* [Tutorial: Use Azure Machine Learning Pipelines for batch scoring](tutorial-pipeline-batch-scoring-classification.md)
* [Examples: Jupyter Notebook examples for machine learning pipelines](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines)
* [Examples: Pipeline with automated machine learning](https://aka.ms/pl-automl)
* [Examples: Pipeline with estimators](https://aka.ms/pl-estimator)

### Understand what happens when you submit a training job

The Azure training lifecycle consists of:

1. Zipping the files in your project folder, ignoring those specified in _.amlignore_ or _.gitignore_
1. Scaling up your compute cluster 
1. Building or downloading the dockerfile to the compute node 
    1. The system calculates a hash of: 
        - The base image 
        - Custom docker steps (see [Deploy a model using a custom Docker base image](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-custom-docker-image))
        - The conda definition YAML (see [Create & use software environments in Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/how-to-use-environments))
    1. The system uses this hash as the key in a lookup of the workspace Azure Container Registry (ACR)
    1. If it is not found, it looks for a match in the global ACR
    1. If it is not found, the system builds a new image (which will be cached and registered with the workspace ACR)
1. Downloading your zipped project file to temporary storage on the compute node
1. Unzipping the project file
1. The compute node executing `python <entry script> <arguments>`
1. Saving logs, model files, and other files written to `./outputs` to the storage account associated with the workspace
1. Scaling down compute, including removing temporary storage 

If you choose to train on your local machine ("configure as local run"), you do not need to use Docker. You may use Docker locally if you choose (see the section [Configure ML pipeline](https://docs.microsoft.com/azure/machine-learning/how-to-debug-pipelines#configure-ml-pipeline ) for an example).

## R SDK

The R SDK enables you to use the R language with Azure Machine Learning. The SDK uses the reticulate package to bind to Azure Machine Learning's Python SDK. This gives you access to core objects and methods implemented in the Python SDK from any R environment.

For more information, see the following articles:

* [Tutorial: Create a logistic regression model](tutorial-1st-r-experiment.md)
* [Azure Machine Learning SDK for R reference](https://azure.github.io/azureml-sdk-for-r/index.html)

## Azure Machine Learning designer

The designer lets you train models using a drag and drop interface in your web browser.

+ [What is the designer?](concept-designer.md)
+ [Tutorial: Predict automobile price](tutorial-designer-automobile-price-train-score.md)
+ [Regression: Predict price](how-to-designer-sample-regression-automobile-price-basic.md)
+ [Classification: Predict income](how-to-designer-sample-classification-predict-income.md)
+ [Classification: Predict churn, appetency, and up-selling](how-to-designer-sample-classification-churn.md)
+ [Classification with custom R script: Predict flight delays](how-to-designer-sample-classification-flight-delay.md)
+ [Text Classification: Wikipedia SP 500 Dataset](how-to-designer-sample-text-classification.md)

## Many models solution accelerator

The [Many Models Solution Accelerator](https://aka.ms/many-models) (preview) builds on Azure Machine Learning and enables you to train, operate, and manage hundreds or even thousands of machine learning models.

For example, building a model __for each instance or individual__ in the following scenarios can lead to improved results:

* Predicting sales for each individual store
* Predictive maintenance for hundreds of oil wells
* Tailoring an experience for individual users.

For more information, see the [Many Models Solution Accelerator](https://aka.ms/many-models) on GitHub.

## CLI

The machine learning CLI is an extension for the Azure CLI. It provides cross-platform CLI commands for working with Azure Machine Learning. Typically, you use the CLI to automate tasks, such as training a machine learning model.

* [Use the CLI extension for Azure Machine Learning](reference-azure-machine-learning-cli.md)
* [MLOps on Azure](https://github.com/microsoft/MLOps)

## VS Code

You can use the VS Code extension to run and manage your training jobs. See the [VS Code resource management how-to guide](how-to-manage-resources-vscode.md#experiments) to learn more.

## Next steps

Learn how to [Set up training environments](how-to-set-up-training-targets.md).
