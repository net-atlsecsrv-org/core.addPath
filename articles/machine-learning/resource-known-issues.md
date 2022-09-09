---
title: Known issues & troubleshooting
titleSuffix: Azure Machine Learning
description: Get a list of the known issues, workarounds, and troubleshooting for Azure Machine Learning.
services: machine-learning
author: j-martens
ms.author: jmartens
ms.reviewer: mldocs
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 03/31/2020

---
# Known issues and troubleshooting Azure Machine Learning

This article helps you find and correct errors or failures you may encounter when using Azure Machine Learning.

## Diagnostic logs

Sometimes it can be helpful if you can provide diagnostic information when asking for help. To see some logs: 
1. Visit [Azure Machine Learning studio](https://ml.azure.com). 
1. On the left-hand side, select **Experiment** 
1. Select an experiment.
1. Select a run.
1. On the top, select **Outputs + logs**.

> [!NOTE]
> Azure Machine Learning logs information from a variety of sources during training, such as AutoML or the Docker container that runs the training job. Many of these logs are not documented. If you encounter problems and contact Microsoft support, they may be able to use these logs during troubleshooting.


## Resource quotas

Learn about the [resource quotas](how-to-manage-quotas.md) you might encounter when working with Azure Machine Learning.

## Installation and import

* **Pip Installation: Dependencies are not guaranteed to be consistent with single line installation**: 

   This is a known limitation of pip, as it does not have a functioning dependency resolver when you install as a single line. The first  unique dependency is the only one it looks at. 

   In the following code `azure-ml-datadrift` and `azureml-train-automl` are both installed using a single line pip install. 
     ```
       pip install azure-ml-datadrift, azureml-train-automl
     ```
   For this example, let's say `azure-ml-datadrift` requires version > 1.0 and `azureml-train-automl` requires version < 1.2. If the latest version of `azure-ml-datadrift` is 1.3,  then both packages get upgraded to 1.3, regardless of the `azureml-train-automl` package requirement for an older version. 

   To ensure the appropriate versions are installed for your packages, install using multiple lines like in the following code. Order isn't an issue here, since pip explicitly downgrades as part of the next line call. And so, the appropriate version dependencies are applied.
    
     ```
        pip install azure-ml-datadrift
        pip install azureml-train-automl 
     ```
     
* **Error message: Cannot uninstall 'PyYAML'**

    Azure Machine Learning SDK for Python: PyYAML is a `distutils` installed project. Therefore, we cannot accurately determine which files belong to it if there is a partial uninstall. To continue installing the SDK while ignoring this error, use:
    
    ```Python
    pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML
    ```

* **Databricks failure when installing packages**

    Azure Machine Learning SDK installation fails on Azure Databricks when more packages are installed. Some packages, such as `psutil`, can cause conflicts. To avoid installation errors, install packages by freezing the library version. This issue is related to Databricks and not to the Azure Machine Learning SDK. You might experience this issue with other libraries, too. Example:
    
    ```python
    psutil cryptography==1.5 pyopenssl==16.0.0 ipython==2.2.0
    ```

    Alternatively, you can use init scripts if you keep facing install issues with Python libraries. This approach isn't officially supported. For more information, see [Cluster-scoped init scripts](https://docs.azuredatabricks.net/user-guide/clusters/init-scripts.html#cluster-scoped-init-scripts).

* **Databricks import error: cannot import name 'Timedelta' from 'pandas._libs.tslibs'**: If you see this error when you use automated machine learning, run the two following lines in your notebook:
    ```
    %sh rm -rf /databricks/python/lib/python3.7/site-packages/pandas-0.23.4.dist-info /databricks/python/lib/python3.7/site-packages/pandas
    %sh /databricks/python/bin/pip install pandas==0.23.4
    ```

* **Databricks import error: No module named 'pandas.core.indexes'**: If you see this error when you use automated machine learning:

    1. Run this command to install two packages in your Azure Databricks cluster:
    
       ```bash
       scikit-learn==0.19.1
       pandas==0.22.0
       ```
    
    1. Detach and then reattach the cluster to your notebook.
    
    If these steps don't solve the issue, try restarting the cluster.

* **Databricks FailToSendFeather**: If you see a `FailToSendFeather` error when reading data on Azure Databricks cluster, refer to the following solutions:
    
    * Upgrade `azureml-sdk[automl]` package to the latest version.
    * Add `azureml-dataprep` version 1.1.8 or above.
    * Add `pyarrow` version 0.11 or above.
    
## Create and manage workspaces

> [!WARNING]
> Moving your Azure Machine Learning workspace to a different subscription, or moving the owning subscription to a new tenant, is not supported. Doing so may cause errors.

* **Azure portal**: If you go directly to view your workspace from a share link from the SDK or the portal, you will not be able to view the normal **Overview** page with subscription information in the extension. You will also not be able to switch into another workspace. If you need to view another workspace, go directly to [Azure Machine Learning studio](https://ml.azure.com) and search for the workspace name.

## Set up your environment

* **Trouble creating AmlCompute**: There is a rare chance that some users who created their Azure Machine Learning workspace from the Azure portal before the GA release might not be able to create AmlCompute in that workspace. You can either raise a support request against the service or create a new workspace through the portal or the SDK to unblock yourself immediately.

## Work with data

### Overloaded AzureFile storage

If you receive an error `Unable to upload project files to working directory in AzureFile because the storage is overloaded`, apply following workarounds.

If you are using file share for other workloads, such as data transfer, the recommendation is to use blobs so that file share is free to be used for submitting runs. You may also split the workload between two different workspaces.

### Passing data as input

*  **TypeError: FileNotFound: No such file or directory**: This error occurs if the file path you provide isn't where the file is located. You need to make sure the way you refer to the file is consistent with where you mounted your dataset on your compute target. To ensure a deterministic state, we recommend using the abstract path when mounting a dataset to a compute target. For example, in the following code we mount the dataset under the root of the filesystem of the compute target, `/tmp`. 
    
    ```python
    # Note the leading / in '/tmp/dataset'
    script_params = {
        '--data-folder': dset.as_named_input('dogscats_train').as_mount('/tmp/dataset'),
    } 
    ```

    If you don't include the leading forward slash, '/',  you'll need to prefix the working directory e.g. `/mnt/batch/.../tmp/dataset` on the compute target to indicate where you want the dataset to be mounted.

### Data labeling projects

|Issue  |Resolution  |
|---------|---------|
|Only datasets created on blob datastores can be used     |  this is a known limitation of the current release.       |
|After creation, the project shows "Initializing" for a long time     | Manually refresh the page. Initialization should proceed at roughly 20 datapoints per second. The lack of autorefresh is a known issue.         |
|When reviewing images, newly labeled images are not shown     |   To load all labeled images, choose the **First** button. The **First** button will take you back to the front of the list, but loads all labeled data.      |
|Pressing Esc key while labeling for object detection creates a zero size label on the top-left corner. Submitting labels in this state fails.     |   Delete the label by clicking on the cross mark next to it.  |

## Azure Machine Learning designer

Known issues:

* **Long compute preparation time**: It may be a few minutes or even longer when you first connect to or create a compute target. 

## Train models

* **ModuleErrors (No module named)**:  If you are running into ModuleErrors while submitting experiments in Azure ML, it means that the training script is expecting a package to be installed but it isn't added. Once you provide the package name, Azure ML will install the package in the environment used for your training run. 

    If you are using [Estimators](concept-azure-machine-learning-architecture.md#estimators) to submit experiments, you can specify a package name via `pip_packages` or `conda_packages` parameter in the estimator based on from which source you want to install the package. You can also specify a yml file with all your dependencies using `conda_dependencies_file`or list all your pip requirements in a txt file using `pip_requirements_file` parameter. If you have your own Azure ML Environment object that you want to override the default image used by the estimator, you can specify that environment via the `environment` parameter of the estimator constructor.

    Azure ML also provides framework-specific estimators for Tensorflow, PyTorch, Chainer and SKLearn. Using these estimators will make sure that the core framework dependencies are installed on your behalf in the environment used for training. You have the option to specify extra dependencies as described above. 
 
    Azure ML maintained docker images and their contents can be seen in [AzureML Containers](https://github.com/Azure/AzureML-Containers).
    Framework-specific dependencies  are listed in the respective framework documentation - [Chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py#remarks), [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py#remarks), [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py#remarks), [SKLearn](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py#remarks).

    > [!Note]
    > If you think a particular package is common enough to be added in Azure ML maintained images and environments please raise a GitHub issue in [AzureML Containers](https://github.com/Azure/AzureML-Containers). 
 
* **NameError (Name not defined), AttributeError (Object has no attribute)**: This exception should come from your training scripts. You can look at the log files from Azure portal to get more information about the specific name not defined or attribute error. From the SDK, you can use `run.get_details()` to look at the error message. This will also list all the log files generated for your run. Please make sure to take a look at your training script and fix the error before resubmitting your run. 

* **Horovod has been shut down**: In most cases if you encounter "AbortedError: Horovod has been shut down" this exception means there was an underlying exception in one of the processes that caused Horovod to shut down. Each rank in the MPI job gets it own dedicated log file in Azure ML. These logs are named `70_driver_logs`. In case of distributed training, the log names are suffixed with `_rank` to make it easier to differentiate the logs. To find the exact error that caused Horovod to shut down, go through all the log files and look for `Traceback` at the end of the driver_log files. One of these files will give you the actual underlying exception. 

* **Run or experiment deletion**:  Experiments can be archived by using the [Experiment.archive](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment(class)?view=azure-ml-py#archive--) 
method, or from the Experiment tab view in Azure Machine Learning studio client via the "Archive experiment" button. This action hides the experiment from list queries and views, but does not delete it.

    Permanent deletion of individual experiments or runs is not currently supported. For more information on deleting Workspace assets, see [Export or delete your Machine Learning service workspace data](how-to-export-delete-data.md).

* **Metric Document is too large**: Azure Machine Learning has internal limits on the size of metric objects that can be logged at once from a training run. If you encounter a "Metric Document is too large" error when logging a list-valued metric, try splitting the list into smaller chunks, for example:

    ```python
    run.log_list("my metric name", my_metric[:N])
    run.log_list("my metric name", my_metric[N:])
    ```

    Internally, Azure ML concatenates the blocks with the same metric name into a contiguous list.

## Automated machine learning

* **Tensor Flow**: Automated machine learning does not currently support tensor flow version 1.13. Installing this version will cause package dependencies to stop working. We are working to fix this issue in a future release.

* **Experiment Charts**: Binary classification charts (precision-recall, ROC, gain curve etc.) shown in automated ML experiment iterations are not rendering correctly in user interface since 4/12. Chart plots are currently showing inverse results, where better performing models are shown with lower results. A resolution is under investigation.

* **Databricks cancel an automated machine learning run**: When you use automated machine learning capabilities on Azure Databricks, to cancel a run and start a new experiment run, restart your Azure Databricks cluster.

* **Databricks >10 iterations for automated machine learning**: In automated machine learning settings, if you have more than 10 iterations, set `show_output` to `False` when you submit the run.

* **Databricks widget for the Azure Machine Learning SDK and automated machine learning**: The Azure Machine Learning SDK widget isn't supported in a Databricks notebook because the notebooks can't parse HTML widgets. You can view the widget in the portal by using this Python code in your Azure Databricks notebook cell:

    ```
    displayHTML("<a href={} target='_blank'>Azure Portal: {}</a>".format(local_run.get_portal_url(), local_run.id))
    ```

## Deploy & serve models

Take these actions for the following errors:

|Error  | Resolution  |
|---------|---------|
|Image building failure when deploying web service     |  Add "pynacl==1.2.1" as a pip dependency to Conda file for image configuration       |
|`['DaskOnBatch:context_managers.DaskOnBatch', 'setup.py']' died with <Signals.SIGKILL: 9>`     |   Change the SKU for VMs used in your deployment to one that has more memory. |
|FPGA failure     |  You will not be able to deploy models on FPGAs until you have requested and been approved for FPGA quota. To request access, fill out the quota request form: https://aka.ms/aml-real-time-ai       |

### Updating Azure Machine Learning components in AKS cluster

Updates to Azure Machine Learning components installed in an Azure Kubernetes Service cluster must be manually applied. 

You can apply these updates by detaching the cluster from the Azure Machine Learning workspace, and then reattaching the cluster to the workspace. If TLS is enabled in the cluster, you will need to supply the TLS/SSL certificate and private key when reattaching the cluster. 

```python
compute_target = ComputeTarget(workspace=ws, name=clusterWorkspaceName)
compute_target.detach()
compute_target.wait_for_completion(show_output=True)

attach_config = AksCompute.attach_configuration(resource_group=resourceGroup, cluster_name=kubernetesClusterName)

## If SSL is enabled.
attach_config.enable_ssl(
    ssl_cert_pem_file="cert.pem",
    ssl_key_pem_file="key.pem",
    ssl_cname=sslCname)

attach_config.validate_configuration()

compute_target = ComputeTarget.attach(workspace=ws, name=args.clusterWorkspaceName, attach_configuration=attach_config)
compute_target.wait_for_completion(show_output=True)
```

If you no longer have the TLS/SSL certificate and private key, or you are using a certificate generated by Azure Machine Learning, you can retrieve the files prior to detaching the cluster by connecting to the cluster using `kubectl` and retrieving the secret `azuremlfessl`.

```bash
kubectl get secret/azuremlfessl -o yaml
```

>[!Note]
>Kubernetes stores the secrets in base-64 encoded format. You will need to base-64 decode the `cert.pem` and `key.pem` components of the secrets prior to providing them to `attach_config.enable_ssl`. 

### Webservices in Azure Kubernetes Service failures

Many webservice failures in Azure Kubernetes Service can be debugged by connecting to the cluster using `kubectl`. You can get the `kubeconfig.json` for an Azure Kubernetes Service Cluster by running

```azurecli-interactive
az aks get-credentials -g <rg> -n <aks cluster name>
```

## Authentication errors

If you perform a management operation on a compute target from a remote job, you will receive one of the following errors:

```json
{"code":"Unauthorized","statusCode":401,"message":"Unauthorized","details":[{"code":"InvalidOrExpiredToken","message":"The request token was either invalid or expired. Please try again with a valid token."}]}
```

```json
{"error":{"code":"AuthenticationFailed","message":"Authentication failed."}}
```

For example, you will receive an error if you try to create or attach a compute target from an ML Pipeline that is submitted for remote execution.
