---
title: Train a model by using a custom Docker image
titleSuffix: Azure Machine Learning
description: Learn how to train models with custom Docker images in Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sagopal
author: saachigopal
ms.date: 10/20/2020
ms.topic: conceptual
ms.custom: how-to
---

# Train a model by using a custom Docker image

In this article, learn how to use a custom Docker image when you're training models with Azure Machine Learning. You'll use the example scripts in this article to classify pet images by creating a convolutional neural network. 

Azure Machine Learning provides a default Docker base image. You can also use Azure Machine Learning environments to specify a different base image, such as one of the maintained [Azure Machine Learning base images](https://github.com/Azure/AzureML-Containers) or your own [custom image](how-to-deploy-custom-docker-image.md#create-a-custom-base-image). Custom base images allow you to closely manage your dependencies and maintain tighter control over component versions when running training jobs.

## Prerequisites

Run the code on either of these environments:

* Azure Machine Learning compute instance (no downloads or installation necessary):
  * Complete the [Set up environment and workspace](tutorial-1st-experiment-sdk-setup.md) tutorial to create a dedicated notebook server preloaded with the SDK and the sample repository.
  * In the Azure Machine Learning [examples repository](https://github.com/Azure/azureml-examples), find a completed notebook by going to the **notebooks** > **fastai** > **train-pets-resnet34.ipynb** directory. 
* Your own Jupyter Notebook server:
  * Create a [workspace configuration file](how-to-configure-environment.md#workspace).
  * Install the [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true). 
  * Create an [Azure container registry](/azure/container-registry) or other Docker registry that's available on the internet.

## Set up a training experiment

In this section, you set up your training experiment by initializing a workspace, defining your environment, and configuring a compute target.

### Initialize a workspace

The [Azure Machine Learning workspace](concept-workspace.md) is the top-level resource for the service. It gives you a centralized place to work with all the artifacts that you create. In the Python SDK, you can access the workspace artifacts by creating a [`Workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py&preserve-view=true) object.

Create a `Workspace` object from the config.json file that you created as a [prerequisite](#prerequisites).

```Python
from azureml.core import Workspace

ws = Workspace.from_config()
```

### Define your environment

Create an `Environment` object and enable Docker.

```python
from azureml.core import Environment

fastai_env = Environment("fastai2")
fastai_env.docker.enabled = True
```

The specified base image in the following code supports the fast.ai library, which allows for distributed deep-learning capabilities. For more information, see the [fast.ai Docker Hub repository](https://hub.docker.com/u/fastdotai). 

When you're using your custom Docker image, you might already have your Python environment properly set up. In that case, set the `user_managed_dependencies` flag to `True` to use your custom image's built-in Python environment. By default, Azure Machine Learning builds a Conda environment with dependencies that you specified. The service runs the script in that environment instead of using any Python libraries that you installed on the base image.

```python
fastai_env.docker.base_image = "fastdotai/fastai2:latest"
fastai_env.python.user_managed_dependencies = True
```

#### Use a private container registry (optional)

To use an image from a private container registry that isn't in your workspace, use `docker.base_image_registry` to specify the address of the repository and a username and password:

```python
# Set the container registry information.
fastai_env.docker.base_image_registry.address = "myregistry.azurecr.io"
fastai_env.docker.base_image_registry.username = "username"
fastai_env.docker.base_image_registry.password = "password"
```

#### Use a custom Dockerfile (optional)

It's also possible to use a custom Dockerfile. Use this approach if you need to install non-Python packages as dependencies. Remember to set the base image to `None`.

```python 
# Specify Docker steps as a string. 
dockerfile = r"""
FROM mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04
RUN echo "Hello from custom container!"
"""

# Set the base image to None, because the image is defined by Dockerfile.
fastai_env.docker.base_image = None
fastai_env.docker.base_dockerfile = dockerfile

# Alternatively, load the string from a file.
fastai_env.docker.base_image = None
fastai_env.docker.base_dockerfile = "./Dockerfile"
```

For more information about creating and managing Azure Machine Learning environments, see [Create and use software environments](how-to-use-environments.md). 

### Create or attach a compute target

You need to create a [compute target](concept-azure-machine-learning-architecture.md#compute-targets) for training your model. In this tutorial, you create `AmlCompute` as your training compute resource.

Creation of `AmlCompute` takes a few minutes. If the `AmlCompute` resource is already in your workspace, this code skips the creation process.

As with other Azure services, there are limits on certain resources (for example, `AmlCompute`) associated with the Azure Machine Learning service. For more information, see [Default limits and how to request a higher quota](how-to-manage-quotas.md).

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# Choose a name for your cluster.
cluster_name = "gpu-cluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target.')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_NC6',
                                                           max_nodes=4)

    # Create the cluster.
    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True)

# Use get_status() to get a detailed status for the current AmlCompute.
print(compute_target.get_status().serialize())
```

## Configure your training job

For this tutorial, use the training script *train.py* on [GitHub](https://github.com/Azure/azureml-examples/blob/main/code/models/fastai/pets-resnet34/train.py). In practice, you can take any custom training script and run it, as is, with Azure Machine Learning.

Create a `ScriptRunConfig` resource to configure your job for running on the desired [compute target](how-to-set-up-training-targets.md).

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory='fastai-example',
                      script='train.py',
                      compute_target=compute_target,
                      environment=fastai_env)
```

## Submit your training job

When you submit a training run by using a `ScriptRunConfig` object, the `submit` method returns an object of type `ScriptRun`. The returned `ScriptRun` object gives you programmatic access to information about the training run. 

```python
from azureml.core import Experiment

run = Experiment(ws,'fastai-custom-image').submit(src)
run.wait_for_completion(show_output=True)
```

> [!WARNING]
> Azure Machine Learning runs training scripts by copying the entire source directory. If you have sensitive data that you don't want to upload, use an [.ignore file](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots) or don't include it in the source directory. Instead, access your data by using a [datastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data?view=azure-ml-py&preserve-view=true).

## Next steps
In this article, you trained a model by using a custom Docker image. See these other articles to learn more about Azure Machine Learning:
* [Track run metrics](how-to-track-experiments.md) during training.
* [Deploy a model](how-to-deploy-custom-docker-image.md) by using a custom Docker image.
