---
title: Set up training & inference compute targets
titleSuffix: Azure Machine Learning
description: Add  compute resources (compute targets) to your workspace to use for machine learning training and inference
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 10/02/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperfq1
---
# Set up compute targets for model training and deployment

Learn how to attach Azure compute resources to your Azure Machine Learning workspace.  Then you can use these resources as training and inference [compute targets](concept-compute-target.md) in your machine learning tasks.

In this article, learn how to set up your workspace to use these compute resources:

* Your local computer
* Remote virtual machines
* Azure HDInsight
* Azure Batch
* Azure Databricks
* Azure Data Lake Analytics
* Azure Container Instance

To use compute targets managed by Azure Machine Learning, see:


* [Azure Machine Learning compute instance](how-to-create-manage-compute-instance.md)
* [Azure Machine Learning compute cluster](how-to-create-attach-compute-cluster.md)
* [Azure Kubernetes Service cluster](how-to-create-attach-kubernetes.md)

## Prerequisites

* An Azure Machine Learning workspace. For more information, see [Create an Azure Machine Learning workspace](how-to-manage-workspace.md).

* The [Azure CLI extension for Machine Learning service](reference-azure-machine-learning-cli.md), [Azure Machine Learning Python SDK](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py), or the [Azure Machine Learning Visual Studio Code extension](tutorial-setup-vscode-extension.md).

## Limitations

* **Do not create multiple, simultaneous attachments to the same compute** from your workspace. For example, attaching one Azure Kubernetes Service cluster to a workspace using two different names. Each new attachment will break the previous existing attachment(s).

    If you want to reattach a compute target, for example to change TLS or other cluster configuration setting, you must first remove the existing attachment.

## What's a compute target?

With Azure Machine Learning, you can train your model on a variety of resources or environments, collectively referred to as [__compute targets__](concept-azure-machine-learning-architecture.md#compute-targets). A compute target can be a local machine or a cloud resource, such as an Azure Machine Learning Compute, Azure HDInsight, or a remote virtual machine.  You also use compute targets for model deployment as described in ["Where and how to deploy your models"](how-to-deploy-and-where.md).


## <a id="local"></a>Local computer

When you use your local computer for **training**, there is no need to create a compute target.  Just [submit the training run](how-to-set-up-training-targets.md) from your local machine.

When you use your local computer for **inference**, you must have Docker installed. To perform the deployment, use [LocalWebservice.deploy_configuration()](/python/api/azureml-core/azureml.core.webservice.local.localwebservice?preserve-view=true&view=azure-ml-py#deploy-configuration-port-none-) to define the port that the web service will use. Then use the normal deployment process as described in [Deploy models with Azure Machine Learning](how-to-deploy-and-where.md).

## <a id="vm"></a>Remote virtual machines

Azure Machine Learning also supports bringing your own compute resource and attaching it to your workspace. One such resource type is an arbitrary remote VM, as long as it's accessible from Azure Machine Learning. The resource can be an Azure VM, a remote server in your organization, or on-premises. Specifically, given the IP address and credentials (user name and password, or SSH key), you can use any accessible VM for remote runs.

You can use a system-built conda environment, an already existing Python environment, or a Docker container. To execute on a Docker container, you must have a Docker Engine running on the VM. This functionality is especially useful when you want a more flexible, cloud-based dev/experimentation environment than your local machine.

Use the Azure Data Science Virtual Machine (DSVM) as the Azure VM of choice for this scenario. This VM is a pre-configured data science and AI development environment in Azure. The VM offers a curated choice of tools and frameworks for full-lifecycle machine learning development. For more information on how to use the DSVM with Azure Machine Learning, see [Configure a development environment](./how-to-configure-environment.md#dsvm).

1. **Create**: Create a DSVM before using it to train your model. To create this resource, see [Provision the Data Science Virtual Machine for Linux (Ubuntu)](./data-science-virtual-machine/dsvm-ubuntu-intro.md).

    > [!WARNING]
    > Azure Machine Learning only supports virtual machines that run **Ubuntu**. When you create a VM or choose an existing VM, you must select a VM that uses Ubuntu.
    > 
    > Azure Machine Learning also requires the virtual machine to have a __public IP address__.

1. **Attach**: To attach an existing virtual machine as a compute target, you must provide the resource ID, user name, and password for the virtual machine. The resource ID of the VM can be constructed using the subscription ID, resource group name, and VM name using the following string format: `/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Compute/virtualMachines/<vm_name>`

 
   ```python
   from azureml.core.compute import RemoteCompute, ComputeTarget

   # Create the compute config 
   compute_target_name = "attach-dsvm"
   
   attach_config = RemoteCompute.attach_configuration(resource_id='<resource_id>',
                                                   ssh_port=22,
                                                   username='<username>',
                                                   password="<password>")

   # Attach the compute
   compute = ComputeTarget.attach(ws, compute_target_name, attach_config)

   compute.wait_for_completion(show_output=True)
   ```

   Or you can attach the DSVM to your workspace [using Azure Machine Learning studio](how-to-create-attach-compute-studio.md#attached-compute).

    > [!WARNING]
    > Do not create multiple, simultaneous attachments to the same DSVM from your workspace. Each new attachment will break the previous existing attachment(s).

1. **Configure**: Create a run configuration for the DSVM compute target. Docker and conda are used to create and configure the training environment on the DSVM.

   ```python
   from azureml.core import ScriptRunConfig
   from azureml.core.environment import Environment
   from azureml.core.conda_dependencies import CondaDependencies
   
   # Create environment
   myenv = Environment(name="myenv")
   
   # Specify the conda dependencies
   myenv.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])
   
   # If no base image is explicitly specified the default CPU image "azureml.core.runconfig.DEFAULT_CPU_IMAGE" will be used
   # To use GPU in DSVM, you should specify the default GPU base Docker image or another GPU-enabled image:
   # myenv.docker.enabled = True
   # myenv.docker.base_image = azureml.core.runconfig.DEFAULT_GPU_IMAGE
   
   # Configure the run configuration with the Linux DSVM as the compute target and the environment defined above
   src = ScriptRunConfig(source_directory=".", script="train.py", compute_target=compute, environment=myenv) 
   ```

## <a id="hdinsight"></a>Azure HDInsight 

Azure HDInsight is a popular platform for big-data analytics. The platform provides Apache Spark, which can be used to train your model.

1. **Create**:  Create the HDInsight cluster before you use it to train your model. To create a Spark on HDInsight cluster, see [Create a Spark Cluster in HDInsight](../hdinsight/spark/apache-spark-jupyter-spark-sql.md). 

    > [!WARNING]
    > Azure Machine Learning requires the HDInsight cluster to have a __public IP address__.

    When you create the cluster, you must specify an SSH user name and password. Take note of these values, as you need them to use HDInsight as a compute target.
    
    After the cluster is created, connect to it with the hostname \<clustername>-ssh.azurehdinsight.net, where \<clustername> is the name that you provided for the cluster. 

1. **Attach**: To attach an HDInsight cluster as a compute target, you must provide the resource ID, user name, and password for the HDInsight cluster. The resource ID of the HDInsight cluster can be constructed using the subscription ID, resource group name, and HDInsight cluster name using the following string format: `/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.HDInsight/clusters/<cluster_name>`

    ```python
   from azureml.core.compute import ComputeTarget, HDInsightCompute
   from azureml.exceptions import ComputeTargetException

   try:
    # if you want to connect using SSH key instead of username/password you can provide parameters private_key_file and private_key_passphrase

    attach_config = HDInsightCompute.attach_configuration(resource_id='<resource_id>',
                                                          ssh_port=22, 
                                                          username='<ssh-username>', 
                                                          password='<ssh-pwd>')
    hdi_compute = ComputeTarget.attach(workspace=ws, 
                                       name='myhdi', 
                                       attach_configuration=attach_config)

   except ComputeTargetException as e:
    print("Caught = {}".format(e.message))

   hdi_compute.wait_for_completion(show_output=True)
   ```

   Or you can attach the HDInsight cluster to your workspace [using Azure Machine Learning studio](how-to-create-attach-compute-studio.md#attached-compute).

    > [!WARNING]
    > Do not create multiple, simultaneous attachments to the same HDInsight from your workspace. Each new attachment will break the previous existing attachment(s).

1. **Configure**: Create a run configuration for the HDI compute target. 

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/hdi.py?name=run_hdi)]


Now that you've attached the compute and configured your run, the next step is to [submit the training run](how-to-set-up-training-targets.md).

## <a id="azbatch"></a>Azure Batch 

Azure Batch is used to run large-scale parallel and high-performance computing (HPC) applications efficiently in the cloud. AzureBatchStep can be used in an Azure Machine Learning Pipeline to submit jobs to an Azure Batch pool of machines.

To attach Azure Batch as a compute target, you must use the Azure Machine Learning SDK and provide the following information:

-    **Azure Batch compute name**: A friendly name to be used for the compute within the workspace
-    **Azure Batch account name**: The name of the Azure Batch account
-    **Resource Group**: The resource group that contains the Azure Batch account.

The following code demonstrates how to attach Azure Batch as a compute target:

```python
from azureml.core.compute import ComputeTarget, BatchCompute
from azureml.exceptions import ComputeTargetException

# Name to associate with new compute in workspace
batch_compute_name = 'mybatchcompute'

# Batch account details needed to attach as compute to workspace
batch_account_name = "<batch_account_name>"  # Name of the Batch account
# Name of the resource group which contains this account
batch_resource_group = "<batch_resource_group>"

try:
    # check if the compute is already attached
    batch_compute = BatchCompute(ws, batch_compute_name)
except ComputeTargetException:
    print('Attaching Batch compute...')
    provisioning_config = BatchCompute.attach_configuration(
        resource_group=batch_resource_group, account_name=batch_account_name)
    batch_compute = ComputeTarget.attach(
        ws, batch_compute_name, provisioning_config)
    batch_compute.wait_for_completion()
    print("Provisioning state:{}".format(batch_compute.provisioning_state))
    print("Provisioning errors:{}".format(batch_compute.provisioning_errors))

print("Using Batch compute:{}".format(batch_compute.cluster_resource_id))
```

> [!WARNING]
> Do not create multiple, simultaneous attachments to the same Azure Batch from your workspace. Each new attachment will break the previous existing attachment(s).

### <a id="databricks"></a>Azure Databricks

Azure Databricks is an Apache Spark-based environment in the Azure cloud. It can be used as a compute target with an Azure Machine Learning pipeline.

Create an Azure Databricks workspace before using it. To create a workspace resource, see the [Run a Spark job on Azure Databricks](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal) document.

To attach Azure Databricks as a compute target, provide the following information:

* __Databricks compute name__: The name you want to assign to this compute resource.
* __Databricks workspace name__: The name of the Azure Databricks workspace.
* __Databricks access token__: The access token used to authenticate to Azure Databricks. To generate an access token, see the [Authentication](https://docs.azuredatabricks.net/dev-tools/api/latest/authentication.html) document.

The following code demonstrates how to attach Azure Databricks as a compute target with the Azure Machine Learning SDK (__The Databricks workspace need to be present in the same subscription as your AML workspace__):

```python
import os
from azureml.core.compute import ComputeTarget, DatabricksCompute
from azureml.exceptions import ComputeTargetException

databricks_compute_name = os.environ.get(
    "AML_DATABRICKS_COMPUTE_NAME", "<databricks_compute_name>")
databricks_workspace_name = os.environ.get(
    "AML_DATABRICKS_WORKSPACE", "<databricks_workspace_name>")
databricks_resource_group = os.environ.get(
    "AML_DATABRICKS_RESOURCE_GROUP", "<databricks_resource_group>")
databricks_access_token = os.environ.get(
    "AML_DATABRICKS_ACCESS_TOKEN", "<databricks_access_token>")

try:
    databricks_compute = ComputeTarget(
        workspace=ws, name=databricks_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('databricks_compute_name {}'.format(databricks_compute_name))
    print('databricks_workspace_name {}'.format(databricks_workspace_name))
    print('databricks_access_token {}'.format(databricks_access_token))

    # Create attach config
    attach_config = DatabricksCompute.attach_configuration(resource_group=databricks_resource_group,
                                                           workspace_name=databricks_workspace_name,
                                                           access_token=databricks_access_token)
    databricks_compute = ComputeTarget.attach(
        ws,
        databricks_compute_name,
        attach_config
    )

    databricks_compute.wait_for_completion(True)
```

For a more detailed example, see an [example notebook](https://aka.ms/pl-databricks) on GitHub.

> [!WARNING]
> Do not create multiple, simultaneous attachments to the same Azure Databricks from your workspace. Each new attachment will break the previous existing attachment(s).

### <a id="adla"></a>Azure Data Lake Analytics

Azure Data Lake Analytics is a big data analytics platform in the Azure cloud. It can be used as a compute target with an Azure Machine Learning pipeline.

Create an Azure Data Lake Analytics account before using it. To create this resource, see the [Get started with Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md) document.

To attach Data Lake Analytics as a compute target, you must use the Azure Machine Learning SDK and provide the following information:

* __Compute name__: The name you want to assign to this compute resource.
* __Resource Group__: The resource group that contains the Data Lake Analytics account.
* __Account name__: The Data Lake Analytics account name.

The following code demonstrates how to attach Data Lake Analytics as a compute target:

```python
import os
from azureml.core.compute import ComputeTarget, AdlaCompute
from azureml.exceptions import ComputeTargetException


adla_compute_name = os.environ.get(
    "AML_ADLA_COMPUTE_NAME", "<adla_compute_name>")
adla_resource_group = os.environ.get(
    "AML_ADLA_RESOURCE_GROUP", "<adla_resource_group>")
adla_account_name = os.environ.get(
    "AML_ADLA_ACCOUNT_NAME", "<adla_account_name>")

try:
    adla_compute = ComputeTarget(workspace=ws, name=adla_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('adla_compute_name {}'.format(adla_compute_name))
    print('adla_resource_id {}'.format(adla_resource_group))
    print('adla_account_name {}'.format(adla_account_name))
    # create attach config
    attach_config = AdlaCompute.attach_configuration(resource_group=adla_resource_group,
                                                     account_name=adla_account_name)
    # Attach ADLA
    adla_compute = ComputeTarget.attach(
        ws,
        adla_compute_name,
        attach_config
    )

    adla_compute.wait_for_completion(True)
```

For a more detailed example, see an [example notebook](https://aka.ms/pl-adla) on GitHub.

> [!WARNING]
> Do not create multiple, simultaneous attachments to the same ADLA from your workspace. Each new attachment will break the previous existing attachment(s).

> [!TIP]
> Azure Machine Learning pipelines can only work with data stored in the default data store of the Data Lake Analytics account. If the data you need to work with is in a non-default store, you can use a [`DataTransferStep`](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?preserve-view=true&view=azure-ml-py) to copy the data before training.

## <a id="aci"></a>Azure Container Instance

Azure Container Instances (ACI) are created dynamically when you deploy a model. You cannot create or attach ACI to your workspace in any other way. For more information, see [Deploy a model to Azure Container Instances](how-to-deploy-azure-container-instance.md).

## Azure Kubernetes Service

Azure Kubernetes Service (AKS) allows for a variety of configuration options when used with Azure Machine Learning. For more information, see [How to create and attach Azure Kubernetes Service](how-to-create-attach-kubernetes.md).


## Notebook examples

See these notebooks for examples of training with various compute targets:
* [how-to-use-azureml/training](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [tutorials/img-classification-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/image-classification-mnist-data/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## Next steps

* Use the compute resource to [configure and submit a training run](how-to-set-up-training-targets.md).
* [Tutorial: Train a model](tutorial-train-models-with-aml.md) uses a managed compute target to  train a model.
* Learn how to [efficiently tune hyperparameters](how-to-tune-hyperparameters.md) to build better models.
* Once you have a trained model, learn [how and where to deploy models](how-to-deploy-and-where.md).
* [Use Azure Machine Learning with Azure Virtual Networks](./how-to-network-security-overview.md)