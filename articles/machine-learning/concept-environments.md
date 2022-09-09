---
title: About Azure Machine Learning environments
titleSuffix: Azure Machine Learning
description: Learn about machine learning environments, which enable reproducible, auditable, & portable machine learning dependency definitions for various compute targets.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: BlackMist
ms.date: 11/16/2020
---

# What are Azure Machine Learning environments?

Azure Machine Learning environments are an encapsulation of the environment where your machine learning training happens. They specify the Python packages, environment variables, and software settings around your training and scoring scripts. They also specify run times (Python, Spark, or Docker). The environments are managed and versioned entities within your Machine Learning workspace that enable reproducible, auditable, and portable machine learning workflows across a variety of compute targets.

You can use an `Environment` object on your local compute to:
* Develop your training script.
* Reuse the same environment on Azure Machine Learning Compute for model training at scale.
* Deploy your model with that same environment.
* Revisit the environment in which an existing model was trained.

The following diagram illustrates how you can use a single `Environment` object in both your run configuration (for training) and your inference and deployment configuration (for web service deployments).

![Diagram of an environment in machine learning workflow](./media/concept-environments/ml-environment.png)

The environment, compute target and training script together form the run configuration: the full specification of a training run.

## Types of environments

Environments can broadly be divided into three categories: *curated*, *user-managed*, and *system-managed*.

Curated environments are provided by Azure Machine Learning and are available in your workspace by default. Intended to be used as is, they contain collections of Python packages and settings to help you get started with various machine learning frameworks. These pre-created environments also allow for faster deployment time. For a full list, see the [curated environments article](resource-curated-environments.md).

In user-managed environments, you're responsible for setting up your environment and installing every package that your training script needs on the compute target. Conda doesn't check your environment or install anything for you. If you're defining your own environment, you must list `azureml-defaults` with version `>= 1.0.45` as a pip dependency. This package contains the functionality that's needed to host the model as a web service.

You use system-managed environments when you want [Conda](https://conda.io/docs/) to manage the Python environment and the script dependencies for you. A new conda environment is built based on the conda dependencies object. The Azure Machine Learning service assumes this type of environment by default, because of its usefulness on remote compute targets that aren't manually configurable.

## Create and manage environments

You can create environments by:

* Defining new `Environment` objects, either by using a curated environment or by defining your own dependencies.
* Using existing `Environment` objects from your workspace. This approach allows for consistency and reproducibility with your dependencies.
* Importing from an existing Anaconda environment definition.
* Using the Azure Machine Learning CLI
* [Using the VS Code extension](how-to-manage-resources-vscode.md#create-environment)

For specific code samples, see the "Create an environment" section of [How to use environments](how-to-use-environments.md#create-an-environment). Environments are also easily managed through your workspace. They include the following functionality:

* Environments are automatically registered to your workspace when you submit an experiment. They can also be manually registered.
* You can fetch environments from your workspace to use for training or deployment, or to make edits to the environment definition.
* With versioning, you can see changes to your environments over time, which ensures reproducibility.
* You can build Docker images automatically from your environments.

For code samples, see the "Manage environments" section of [How to use environments](how-to-use-environments.md#manage-environments).

## Environment building, caching, and reuse

The Azure Machine Learning service builds environment definitions into Docker images and conda environments. It also caches the environments so they can be reused in subsequent training runs and service endpoint deployments. Running a training script remotely requires the creation of a Docker image whereas, a local run can use a Conda environment directly. 

### Submitting a run using an environment

When you first submit a remote run using an environment, the Azure Machine Learning service invokes an [ACR Build Task](../container-registry/container-registry-tasks-overview.md) on the Azure Container Registry (ACR) associated with the Workspace. The built Docker image is then cached on the Workspace ACR. Curated environments are backed by Docker images that are cached in Global ACR. At the start of the run execution, the image is retrieved by the compute target from the relevant ACR.

For local runs, a Docker or Conda environment is created based on the environment definition. The scripts are then executed on the target compute - a local runtime environment or local Docker engine.

### Building environments as Docker images

If the environment definition doesn't already exist in the workspace ACR, a new image will be built. The image build consists of two steps:

 1. Downloading a base image, and executing any Docker steps
 2. Building a conda environment according to conda dependencies specified in the environment definition.

The second step is omitted if you specify [user-managed dependencies](/python/api/azureml-core/azureml.core.environment.pythonsection?preserve-view=true&view=azure-ml-py). In this case you're responsible for installing any Python packages, by including them in your base image, or specifying custom Docker steps within the first step. You're also responsible for specifying the correct location for the Python executable. It is also possible to use a [custom Docker base image](how-to-deploy-custom-docker-image.md).

### Image caching and reuse

If you use the same environment definition for another run, the Azure Machine Learning service reuses the cached image from the Workspace ACR. 

To view the details of a cached image, use [Environment.get_image_details](/python/api/azureml-core/azureml.core.environment.environment?preserve-view=true&view=azure-ml-py#&preserve-view=trueget-image-details-workspace-) method.

To determine whether to reuse a cached image or build a new one, the service computes [a hash value](https://en.wikipedia.org/wiki/Hash_table) from the environment definition and compares it to the hashes of existing environments. The hash is based on:
 
 * Base image property value
 * Custom docker steps property value
 * List of Python packages in Conda definition
 * List of packages in Spark definition 

The hash doesn't depend on environment name or version -  if you rename your environment or create a new environment with the exact properties and packages of an existing one, then the hash value remains the same. However, environment definition changes, such as adding or removing a Python package or changing the package version, cause the hash value to change. Changing the order of dependencies or channels in an environment will result in a new environment and thus require a new image build. It is important to note that any change to a curated environment will invalidate the hash and result in a new "non-curated" environment.

The computed hash value is compared to those in the Workspace and Global ACR (or on the compute target for local runs). If there is a match then the cached image is pulled, otherwise an image build is triggered. The duration to pull a cached image includes the download time whereas the duration to pull a newly built image includes both the build time and the download time. 

The following diagram shows three environment definitions. Two of them have different names and versions, but identical base image and Python packages. But they have the same hash and thus correspond to the same cached image. The third environment has different Python packages and versions, and therefore corresponds to a different cached image.

![Diagram of environment caching as Docker images](./media/concept-environments/environment-caching.png)

>[!IMPORTANT]
> If you create an environment with an unpinned package dependency, for example ```numpy```, that environment will keep using the package version installed _at the time of environment creation_. Also, any future environment with matching definition will keep using the old version. 

To update the package, specify a version number to force image rebuild, for example ```numpy==1.18.1```. New dependencies, including nested ones, will be installed that might break a previously working scenario. 

> [!WARNING]
>  The [Environment.build](/python/api/azureml-core/azureml.core.environment.environment?preserve-view=true&view=azure-ml-py#&preserve-view=truebuild-workspace--image-build-compute-none-) method will rebuild the cached image, with possible side-effect of updating unpinned packages and breaking reproducibility for all environment definitions corresponding to that cached image.

## Next steps

* Learn how to [create and use environments](how-to-use-environments.md) in Azure Machine Learning.
* See the Python SDK reference documentation for the [environment class](/python/api/azureml-core/azureml.core.environment%28class%29?preserve-view=true&view=azure-ml-py).
* See the R SDK reference documentation for [environments](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-environments).