---
title: Train with azureml-datasets
titleSuffix: Azure Machine Learning
description:  Learn how to make your data available to your local or remote compute for ML model training with Azure Machine Learning datasets.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 07/31/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, data4ml

# Customer intent: As an experienced Python developer, I need to make my data available to my local or remote compute target to train my machine learning models.

---

# Train with datasets in Azure Machine Learning


In this article, you learn how to work with [Azure Machine Learning datasets](/python/api/azureml-core/azureml.core.dataset%28class%29?preserve-view=true&view=azure-ml-py) in your training experiments.  You can use datasets in your local or remote compute target without worrying about connection strings or data paths.

Azure Machine Learning datasets provide a seamless integration with Azure Machine Learning training functionality like [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig?preserve-view=true&view=azure-ml-py), [HyperDrive](/python/api/azureml-train-core/azureml.train.hyperdrive?preserve-view=true&view=azure-ml-py) and [Azure Machine Learning pipelines](how-to-create-your-first-pipeline.md).

## Prerequisites

To create and train with datasets, you need:

* An Azure subscription. If you don't have an Azure subscription, create a free account before you begin. Try the [free or paid version of Azure Machine Learning](https://aka.ms/AMLFree) today.

* An [Azure Machine Learning workspace](how-to-manage-workspace.md).

* The [Azure Machine Learning SDK for Python installed](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py) (>= 1.13.0), which includes the azureml-datasets package.

> [!Note]
> Some Dataset classes have dependencies on the [azureml-dataprep](/python/api/azureml-dataprep/?preserve-view=true&view=azure-ml-py) package. For Linux users, these classes are supported only on the following distributions:  Red Hat Enterprise Linux, Ubuntu, Fedora, and CentOS.

## Use datasets directly in training scripts

If you have structured data not yet registered as a dataset, create a TabularDataset and use it directly in your training script for your local or remote experiment.

In this example, you create an unregistered [TabularDataset](/python/api/azureml-core/azureml.data.tabulardataset?preserve-view=true&view=azure-ml-py) and specify it as a script argument in the ScriptRunConfig object for training. If you want to reuse this TabularDataset with other experiments in your workspace, see [how to register datasets to your workspace](how-to-create-register-datasets.md#register-datasets).

### Create a TabularDataset

The following code creates an unregistered TabularDataset from a web url.  

```Python
from azureml.core.dataset import Dataset

web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path)
```

TabularDataset objects provide the ability to load the data in your TabularDataset into a pandas or Spark DataFrame so that you can work with familiar data preparation and training libraries without having to leave your notebook.

### Access dataset in training script

The following code configures a script argument `--input-data` that you will specify when you configure your training run (see next section). When the tabular dataset is passed in as the argument value, Azure ML will resolve that to ID of the dataset, which you can then use to access the dataset in your training script (without having to hardcode the name or ID of the dataset in your script). It then uses the [`to_pandas_dataframe()`](/python/api/azureml-core/azureml.data.tabulardataset#to-pandas-dataframe-on-error--null---out-of-range-datetime--null--) method to load that dataset into a pandas dataframe for further data exploration and preparation prior to training.

> [!Note]
> If your original data source contains NaN, empty strings or blank values, when you use `to_pandas_dataframe()`, then those values are replaced as a *Null* value.

If you need to load the prepared data into a new dataset from an in-memory pandas dataframe, write the data to a local file, like a parquet, and create a new dataset from that file. You can also create datasets from local files or paths in datastores. Learn more about [how to create datasets](how-to-create-register-datasets.md).

```Python
%%writefile $script_folder/train_titanic.py

import argparse
from azureml.core import Dataset, Run

parser = argparse.ArgumentParser()
parser.add_argument("--input-data", type=str)
args = parser.parse_args()

run = Run.get_context()
ws = run.experiment.workspace

# get the input dataset by ID
dataset = Dataset.get_by_id(ws, id=args.input_data)

# load the TabularDataset to pandas DataFrame
df = dataset.to_pandas_dataframe()
```

### Configure the training run
A [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrun?preserve-view=true&view=azure-ml-py) object is used to configure and submit the training run.

This code creates a ScriptRunConfig object, `src`, that specifies

* A script directory for your scripts. All the files in this directory are uploaded into the cluster nodes for execution.
* The training script, *train_titanic.py*.
* The input dataset for training, `titanic_ds`, as a script argument. Azure ML will resolve this to corresponding ID of the dataset when it is passed to your script.
* The compute target for the run.
* The environment for the run.

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory=script_folder,
                      script='train_titanic.py',
                      # pass dataset as an input with friendly name 'titanic'
                      arguments=['--input-dataset', titanic_ds],
                      compute_target=compute_target,
                      environment=myenv)
                             
# Submit the run configuration for your training run
run = experiment.submit(src)
run.wait_for_completion(show_output=True)                             
```

## Mount files to remote compute targets

If you have unstructured data, create a [FileDataset](/python/api/azureml-core/azureml.data.filedataset?preserve-view=true&view=azure-ml-py) and either mount or download your data files to make them available to your remote compute target for training. Learn about when to use [mount vs. download](#mount-vs-download) for your remote training experiments. 

The following example creates a FileDataset and mounts the dataset to the compute target by passing it as an argument to the training script. 

> [!Note]
> If you are using a custom Docker base image, you will need to install fuse via `apt-get install -y fuse` as a dependency for dataset mount to work. Learn how to [build a custom build image](how-to-deploy-custom-docker-image.md#build-a-custom-base-image).

### Create a FileDataset

The following example creates an unregistered FileDataset from web urls. Learn more about [how to create datasets](how-to-create-register-datasets.md) from other sources.

```Python
from azureml.core.dataset import Dataset

web_paths = [
            'http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz'
            ]
mnist_ds = Dataset.File.from_files(path = web_paths)
```

### Configure the training run
We recommend passing the dataset as an argument when mounting via the `arguments` parameter of the `ScriptRunConfig` constructor. By doing so, you will get the data path (mounting point) in your training script via arguments. This way, you will be able use the same training script for local debugging and remote training on any cloud platform.

The following example creates a ScriptRunConfig that passes in the FileDataset via `arguments`. After you submit the run, data files referred by the dataset `mnist_ds` will be mounted to the compute target.

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory=script_folder,
                      script='train_mnist.py',
                      # the dataset will be mounted on the remote compute and the mounted path passed as an argument to the script
                      arguments=['--data-folder', mnist_ds.as_mount(), '--regularization', 0.5],
                      compute_target=compute_target,
                      environment=myenv)

# Submit the run configuration for your training run
run = experiment.submit(src)
run.wait_for_completion(show_output=True)
```

### Retrieve the data in your training script

The following code shows how to retrieve the data in your script.

```Python
%%writefile $script_folder/train_mnist.py

import argparse
import os
import numpy as np
import glob

from utils import load_data

# retrieve the 2 arguments configured through `arguments` in the ScriptRunConfig
parser = argparse.ArgumentParser()
parser.add_argument('--data-folder', type=str, dest='data_folder', help='data folder mounting point')
parser.add_argument('--regularization', type=float, dest='reg', default=0.01, help='regularization rate')
args = parser.parse_args()

data_folder = args.data_folder
print('Data folder:', data_folder)

# get the file paths on the compute
X_train_path = glob.glob(os.path.join(data_folder, '**/train-images-idx3-ubyte.gz'), recursive=True)[0]
X_test_path = glob.glob(os.path.join(data_folder, '**/t10k-images-idx3-ubyte.gz'), recursive=True)[0]
y_train_path = glob.glob(os.path.join(data_folder, '**/train-labels-idx1-ubyte.gz'), recursive=True)[0]
y_test = glob.glob(os.path.join(data_folder, '**/t10k-labels-idx1-ubyte.gz'), recursive=True)[0]

# load train and test set into numpy arrays
X_train = load_data(X_train_path, False) / 255.0
X_test = load_data(X_test_path, False) / 255.0
y_train = load_data(y_train_path, True).reshape(-1)
y_test = load_data(y_test, True).reshape(-1)
```

## Mount vs download

Mounting or downloading files of any format are supported for datasets created from Azure Blob storage, Azure Files, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database, and Azure Database for PostgreSQL. 

When you **mount** a dataset, you attach the files referenced by the dataset to a directory (mount point) and make it available on the compute target. Mounting is supported for Linux-based computes, including Azure Machine Learning Compute, virtual machines, and HDInsight. 

When you **download** a dataset, all the files referenced by the dataset will be downloaded to the compute target. Downloading is supported for all compute types. 

If your script processes all files referenced by the dataset, and your compute disk can fit your full dataset, downloading is recommended to avoid the overhead of streaming data from storage services. If your data size exceeds the compute disk size,  downloading is not possible. For this scenario, we recommend mounting since only the data files used by your script are loaded at the time of processing.

The following code mounts `dataset` to the temp directory at `mounted_path`

```python
import tempfile
mounted_path = tempfile.mkdtemp()

# mount dataset onto the mounted_path of a Linux-based compute
mount_context = dataset.mount(mounted_path)

mount_context.start()

import os
print(os.listdir(mounted_path))
print (mounted_path)
```

## Directly access datasets in your script

Registered datasets are accessible both locally and remotely on compute clusters like the Azure Machine Learning compute. To access your registered dataset across experiments, use the following code to access your workspace and registered dataset by name. By default, the [`get_by_name()`](/python/api/azureml-core/azureml.core.dataset.dataset?preserve-view=true&view=azure-ml-py#&preserve-view=trueget-by-name-workspace--name--version--latest--) method on the `Dataset` class returns the latest version of the dataset that's registered with the workspace.

```Python
%%writefile $script_folder/train.py

from azureml.core import Dataset, Run

run = Run.get_context()
workspace = run.experiment.workspace

dataset_name = 'titanic_ds'

# Get a dataset by name
titanic_ds = Dataset.get_by_name(workspace=workspace, name=dataset_name)

# Load a TabularDataset into pandas DataFrame
df = titanic_ds.to_pandas_dataframe()
```

## Accessing source code during training

Azure Blob storage has higher throughput speeds than an Azure file share and will scale to large numbers of jobs started in parallel. For this reason, we recommend configuring your runs to use Blob storage for transferring source code files.

The following code example specifies in the run configuration which blob datastore to use for source code transfers.

```python 
# workspaceblobstore is the default blob storage
src.run_config.source_directory_data_store = "workspaceblobstore" 
```

## Notebook examples

The [dataset notebooks](https://aka.ms/dataset-tutorial) demonstrate and expand upon concepts in this article.

## Next steps

* [Auto train machine learning models](how-to-auto-train-remote.md) with TabularDatasets.

* [Train image classification models](https://aka.ms/filedataset-samplenotebook) with FileDatasets.

* [Train with datasets using pipelines](how-to-create-your-first-pipeline.md).