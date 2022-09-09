---
title: 'Install & use Azure Machine Learning CLI'
description: Learn how to install and use the Azure Machine Learning CLI extension to create and manage resources such as your workspace, datastores, datasets, pipelines, models, and deployments.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference

ms.reviewer: jmartens
ms.author: jordane
author: jpe316
ms.date: 06/22/2020
ms.custom: seodec18
---

# Install & use the CLI extension for Azure Machine Learning


The Azure Machine Learning CLI is an extension to the [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest&preserve-view=true), a cross-platform command-line interface for the Azure platform. This extension provides commands for working with Azure Machine Learning. It allows you to automate your machine learning activities. The following list provides some example actions that you can do with the CLI extension:

+ Run experiments to create machine learning models

+ Register machine learning models for customer usage

+ Package, deploy, and track the lifecycle of your machine learning models

The CLI is not a replacement for the Azure Machine Learning SDK. It is a complementary tool that is optimized to handle highly parameterized tasks which suit themselves well to automation.

## Prerequisites

* To use the CLI, you must have an Azure subscription. If you don't have an Azure subscription, create a free account before you begin. Try the [free or paid version of Azure Machine Learning](https://aka.ms/AMLFree) today.

* To use the CLI commands in this document from your **local environment**, you need the [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true).

    If you use the [Azure Cloud Shell](https://azure.microsoft.com//features/cloud-shell/), the CLI is accessed through the browser and lives in the cloud.

## Full reference docs

Find the [full reference docs for the azure-cli-ml extension of Azure CLI](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/?view=azure-cli-latest&preserve-view=true).

## Connect the CLI to your Azure subscription

> [!IMPORTANT]
> If you are using the Azure Cloud Shell, you can skip this section. The cloud shell automatically authenticates you using the account you log into your Azure subscription.

There are several ways that you can authenticate to your Azure subscription from the CLI. The most basic is to interactively authenticate using a browser. To authenticate interactively, open a command line or terminal and use the following command:

```azurecli-interactive
az login
```

If the CLI can open your default browser, it will do so and load a sign-in page. Otherwise, you need to open a browser and follow the instructions on the command line. The instructions involve browsing to [https://aka.ms/devicelogin](https://aka.ms/devicelogin) and entering an authorization code.

[!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)]

For other methods of authenticating, see [Sign in with Azure CLI](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest&preserve-view=true).

## Install the extension

To install the Machine Learning CLI extension, use the following command:

```azurecli-interactive
az extension add -n azure-cli-ml
```

> [!TIP]
> Example files you can use with the commands below can be found [here](https://aka.ms/azml-deploy-cloud).

When prompted, select `y` to install the extension.

To verify that the extension has been installed, use the following command to display a list of ML-specific subcommands:

```azurecli-interactive
az ml -h
```

## Update the extension

To update the Machine Learning CLI extension, use the following command:

```azurecli-interactive
az extension update -n azure-cli-ml
```


## Remove the extension

To remove the CLI extension, use the following command:

```azurecli-interactive
az extension remove -n azure-cli-ml
```

## Resource management

The following commands demonstrate how to use the CLI to manage resources used by Azure Machine Learning.

+ If you do not already have one, create a resource group:

    ```azurecli-interactive
    az group create -n myresourcegroup -l westus2
    ```

+ Create an Azure Machine Learning workspace:

    ```azurecli-interactive
    az ml workspace create -w myworkspace -g myresourcegroup
    ```

    For more information, see [az ml workspace create](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-workspace-create).

+ Attach a workspace configuration to a folder to enable CLI contextual awareness.

    ```azurecli-interactive
    az ml folder attach -w myworkspace -g myresourcegroup
    ```

    This command creates a `.azureml` subdirectory that contains example runconfig and conda environment files. It also contains a `config.json` file that is used to communicate with your Azure Machine Learning workspace.

    For more information, see [az ml folder attach](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/folder?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-folder-attach).

+ Attach an Azure blob container as a Datastore.

    ```azurecli-interactive
    az ml datastore attach-blob  -n datastorename -a accountname -c containername
    ```

    For more information, see [az ml datastore attach-blob](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/datastore?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-datastore-attach-blob).

+ Upload files to a Datastore.

    ```azurecli-interactive
    az ml datastore upload  -n datastorename -p sourcepath
    ```

    For more information, see [az ml datastore upload](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/datastore?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-datastore-upload).

+ Attach an AKS cluster as a Compute Target.

    ```azurecli-interactive
    az ml computetarget attach aks -n myaks -i myaksresourceid -g myresourcegroup -w myworkspace
    ```

    For more information, see [az ml computetarget attach aks](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/attach?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-computetarget-attach-aks)

### Compute clusters

+ Create a new managed compute cluster.

    ```azurecli-interactive
    az ml computetarget create amlcompute -n cpu --min-nodes 1 --max-nodes 1 -s STANDARD_D3_V2
    ```



+ Create a new managed compute cluster with managed identity

  + User-assigned managed identity

    ```azurecli
    az ml computetarget create amlcompute --name cpu-cluster --vm-size Standard_NC6 --max-nodes 5 --assign-identity '/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'
    ```

  + System-assigned managed identity

    ```azurecli
    az ml computetarget create amlcompute --name cpu-cluster --vm-size Standard_NC6 --max-nodes 5 --assign-identity '[system]'
    ```
+ Add a managed identity to an existing cluster:

    + User-assigned managed identity
        ```azurecli
        az ml computetarget amlcompute identity assign --name cpu-cluster '/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'
        ```
    + System-assigned managed identity

        ```azurecli
        az ml computetarget amlcompute identity assign --name cpu-cluster '[system]'
        ```

For more information, see [az ml computetarget create amlcompute](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-computetarget-create-amlcompute).

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-note.md)]

<a id="computeinstance"></a>

### Compute instance
Manage compute instances.  In all the examples below, the name of the compute instance is **cpu**

+ Create a new computeinstance.

    ```azurecli-interactive
    az ml computetarget create computeinstance  -n cpu -s "STANDARD_D3_V2" -v
    ```
    
    For more information, see [az ml computetarget create computeinstance](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-computetarget-create-computeinstance).

+ Stop a computeinstance.
    
    ```azurecli-interactive
    az ml computetarget stop computeinstance -n cpu -v
    ```
    
    For more information, see [az ml computetarget stop computeinstance](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/computeinstance?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-computetarget-computeinstance-stop).
    
+ Start a computeinstance.
    
    ```azurecli-interactive
    az ml computetarget start computeinstance -n cpu -v
    ```
    
    For more information, see [az ml computetarget start computeinstance](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/computeinstance?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-computetarget-computeinstance-start).
    
+ Restart a computeinstance.
    
    ```azurecli-interactive
    az ml computetarget restart computeinstance -n cpu -v
    ```
    
    For more information, see [az ml computetarget restart computeinstance](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/computeinstance?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-computetarget-computeinstance-restart).
    
+ Delete a computeinstance.
    
    ```azurecli-interactive
    az ml computetarget delete -n cpu -v
    ```
    
    For more information, see [az ml computetarget delete computeinstance](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-computetarget-delete).


## <a id="experiments"></a>Run experiments

* Start a run of your experiment. When using this command, specify the name of the runconfig file (the text before \*.runconfig if you are looking at your file system) against the -c parameter.

    ```azurecli-interactive
    az ml run submit-script -c sklearn -e testexperiment train.py
    ```

    > [!TIP]
    > The `az ml folder attach` command creates a `.azureml` subdirectory, which contains two example runconfig files. 
    >
    > If you have a Python script that creates a run configuration object programmatically, you can use [RunConfig.save()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py&preserve-view=true#&preserve-view=truesave-path-none--name-none--separate-environment-yaml-false-) to save it as a runconfig file.
    >
    > The full runconfig schema can be found in this [JSON file](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json). The schema is self-documenting through the `description` key of each object. Additionally, there are enums for possible values, and a template snippet at the end.

    For more information, see [az ml run submit-script](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-run-submit-script).

* View a list of experiments:

    ```azurecli-interactive
    az ml experiment list
    ```

    For more information, see [az ml experiment list](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/experiment?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-experiment-list).

### HyperDrive run

You can use HyperDrive with Azure CLI to perform parameter tuning runs. First, create a HyperDrive configuration file in the following format. See [Tune hyperparameters for your model](how-to-tune-hyperparameters.md) article for details on hyperparameter tuning parameters.

```yml
# hdconfig.yml
sampling: 
    type: random # Supported options: Random, Grid, Bayesian
    parameter_space: # specify a name|expression|values tuple for each parameter.
    - name: --penalty # The name of a script parameter to generate values for.
      expression: choice # supported options: choice, randint, uniform, quniform, loguniform, qloguniform, normal, qnormal, lognormal, qlognormal
      values: [0.5, 1, 1.5] # The list of values, the number of values is dependent on the expression specified.
policy: 
    type: BanditPolicy # Supported options: BanditPolicy, MedianStoppingPolicy, TruncationSelectionPolicy, NoTerminationPolicy
    evaluation_interval: 1 # Policy properties are policy specific. See the above link for policy specific parameter details.
    slack_factor: 0.2
primary_metric_name: Accuracy # The metric used when evaluating the policy
primary_metric_goal: Maximize # Maximize|Minimize
max_total_runs: 8 # The maximum number of runs to generate
max_concurrent_runs: 2 # The number of runs that can run concurrently.
max_duration_minutes: 100 # The maximum length of time to run the experiment before cancelling.
```

Add this file alongside the run configuration files. Then submit a HyperDrive run using:
```azurecli
az ml run submit-hyperdrive -e <experiment> -c <runconfig> --hyperdrive-configuration-name <hdconfig> my_train.py
```

Note the *arguments* section in runconfig and *parameter space* in HyperDrive config. They contain the command-line arguments to be passed to training script. The value in runconfig stays the same for each iteration, while the range in HyperDrive config is iterated over. Do not specify the same argument in both files.

## Dataset management

The following commands demonstrate how to work with datasets in Azure Machine Learning:

+ Register a dataset:

    ```azurecli-interactive
    az ml dataset register -f mydataset.json
    ```

    For information on the format of the JSON file used to define the dataset, use `az ml dataset register --show-template`.

    For more information, see [az ml dataset register](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-dataset-register).

+ List all datasets in a workspace:

    ```azurecli-interactive
    az ml dataset list
    ```

    For more information, see [az ml dataset list](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-dataset-list).

+ Get details of a dataset:

    ```azurecli-interactive
    az ml dataset show -n dataset-name
    ```

    For more information, see [az ml dataset show](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-dataset-show).

+ Unregister a dataset:

    ```azurecli-interactive
    az ml dataset unregister -n dataset-name
    ```

    For more information, see [az ml dataset unregister](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-dataset-archive).

## Environment management

The following commands demonstrate how to create, register, and list Azure Machine Learning [environments](how-to-configure-environment.md) for your workspace:

+ Create scaffolding files for an environment:

    ```azurecli-interactive
    az ml environment scaffold -n myenv -d myenvdirectory
    ```

    For more information, see [az ml environment scaffold](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-environment-scaffold).

+ Register an environment:

    ```azurecli-interactive
    az ml environment register -d myenvdirectory
    ```

    For more information, see [az ml environment register](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-environment-register).

+ List registered environments:

    ```azurecli-interactive
    az ml environment list
    ```

    For more information, see [az ml environment list](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-environment-list).

+ Download a registered environment:

    ```azurecli-interactive
    az ml environment download -n myenv -d downloaddirectory
    ```

    For more information, see [az ml environment download](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-environment-download).

### Environment configuration schema

If you used the `az ml environment scaffold` command, it generates a template `azureml_environment.json` file that can be modified and used to create custom environment configurations with the CLI. The top level object loosely maps to the [`Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment%28class%29?view=azure-ml-py&preserve-view=true) class in the Python SDK. 

```json
{
    "name": "testenv",
    "version": null,
    "environmentVariables": {
        "EXAMPLE_ENV_VAR": "EXAMPLE_VALUE"
    },
    "python": {
        "userManagedDependencies": false,
        "interpreterPath": "python",
        "condaDependenciesFile": null,
        "baseCondaEnvironment": null
    },
    "docker": {
        "enabled": false,
        "baseImage": "mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04",
        "baseDockerfile": null,
        "sharedVolumes": true,
        "shmSize": "2g",
        "arguments": [],
        "baseImageRegistry": {
            "address": null,
            "username": null,
            "password": null
        }
    },
    "spark": {
        "repositories": [],
        "packages": [],
        "precachePackages": true
    },
    "databricks": {
        "mavenLibraries": [],
        "pypiLibraries": [],
        "rcranLibraries": [],
        "jarLibraries": [],
        "eggLibraries": []
    },
    "inferencingStackVersion": null
}
```

The following table details each top-level field in the JSON file, it's type, and a description. If an object type is linked to a class from the Python SDK, there is a loose 1:1 match between each JSON field and the public variable name in the Python class. In some cases the field may map to a constructor argument rather than a class variable. For example, the `environmentVariables` field maps to the `environment_variables` variable in the [`Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment%28class%29?view=azure-ml-py&preserve-view=true) class.

| JSON field | Type | Description |
|---|---|---|
| `name` | `string` | Name of the environment. Do not start name with **Microsoft** or **AzureML**. |
| `version` | `string` | Version of the environment. |
| `environmentVariables` | `{string: string}` | A hash-map of environment variable names and values. |
| `python` | [`PythonSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.pythonsection?view=azure-ml-py&preserve-view=true)hat defines the Python environment and interpreter to use on target compute resource. |
| `docker` | [`DockerSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.dockersection?view=azure-ml-py&preserve-view=true) | Defines settings to customize the Docker image built to the environment's specifications. |
| `spark` | [`SparkSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.sparksection?view=azure-ml-py&preserve-view=true) | The section configures Spark settings. It is only used when framework is set to PySpark. |
| `databricks` | [`DatabricksSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.databricks.databrickssection?view=azure-ml-py&preserve-view=true) | Configures Databricks library dependencies. |
| `inferencingStackVersion` | `string` | Specifies the inferencing stack version added to the image. To avoid adding an inferencing stack, leave this field `null`. Valid value: "latest". |

## ML pipeline management

The following commands demonstrate how to work with machine learning pipelines:

+ Create a machine learning pipeline:

    ```azurecli-interactive
    az ml pipeline create -n mypipeline -y mypipeline.yml
    ```

    For more information, see [az ml pipeline create](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/pipeline?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-pipeline-create).

    For more information on the pipeline YAML file, see [Define machine learning pipelines in YAML](reference-pipeline-yaml.md).

+ Run a pipeline:

    ```azurecli-interactive
    az ml run submit-pipeline -n myexperiment -y mypipeline.yml
    ```

    For more information, see [az ml run submit-pipeline](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-run-submit-pipeline).

    For more information on the pipeline YAML file, see [Define machine learning pipelines in YAML](reference-pipeline-yaml.md).

+ Schedule a pipeline:

    ```azurecli-interactive
    az ml pipeline create-schedule -n myschedule -e myexpereiment -i mypipelineid -y myschedule.yml
    ```

    For more information, see [az ml pipeline create-schedule](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/pipeline?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-pipeline-create-schedule).

    For more information on the pipeline schedule YAML file, see [Define machine learning pipelines in YAML](reference-pipeline-yaml.md#schedules).

## Model registration, profiling, deployment

The following commands demonstrate how to register a trained model, and then deploy it as a production service:

+ Register a model with Azure Machine Learning:

    ```azurecli-interactive
    az ml model register -n mymodel -p sklearn_regression_model.pkl
    ```

    For more information, see [az ml model register](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-model-register).

+ **OPTIONAL** Profile your model to get optimal CPU and memory values for deployment.
    ```azurecli-interactive
    az ml model profile -n myprofile -m mymodel:1 --ic inferenceconfig.json -d "{\"data\": [[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}" -t myprofileresult.json
    ```

    For more information, see [az ml model profile](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-model-profile).

+ Deploy your model to AKS
    ```azurecli-interactive
    az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json --ct akscomputetarget
    ```
    
    For more information on the inference configuration file schema, see [Inference configuration schema](#inferenceconfig).
    
    For more information on the deployment configuration file schema, see [Deployment configuration schema](#deploymentconfig).

    For more information, see [az ml model deploy](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-model-deploy).

<a id="inferenceconfig"></a>

## Inference configuration schema

[!INCLUDE [inferenceconfig](../../includes/machine-learning-service-inference-config.md)]

<a id="deploymentconfig"></a>

## Deployment configuration schema

### Local deployment configuration schema

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-local-deploy-config.md)]

### Azure Container Instance deployment configuration schema 

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aci-deploy-config.md)]

### Azure Kubernetes Service deployment configuration schema

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aks-deploy-config.md)]

## Next steps

* [Command reference for the Machine Learning CLI extension](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml?view=azure-cli-latest&preserve-view=true).

* [Train and deploy machine learning models using Azure Pipelines](/azure/devops/pipelines/targets/azure-machine-learning)
