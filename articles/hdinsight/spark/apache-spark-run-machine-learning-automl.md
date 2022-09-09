---
title: Run Azure Machine Learning workloads with automated machine learning (AutoML) on Apache Spark in Azure HDInsight
description: Learn how to run Azure Machine Learning workloads with automated machine learning (AutoML) on Apache Spark in Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/14/2019
---
# Run Azure Machine Learning workloads with automated machine learning (AutoML) on Apache Spark in Azure HDInsight

Azure Machine Learning simplifies and accelerates the building, training, and deployment of machine learning models. In automated machine learning (AutoML), you start with training data that has a defined target feature and then iterate through combinations of algorithms and feature selections to automatically select the best model for your data based on the training scores. HDInsight allows customers to provision clusters with hundreds of nodes. AutoML running on Spark in an HDInsight cluster allows users to use compute capacity across these nodes to run training jobs in a scale-out fashion, and to run multiple training jobs in parallel. This allows users to run AutoML experiments while sharing the compute with their other big data workloads.
 

## Install Azure Machine Learning on an HDInsight cluster

For general tutorials of automated machine learning, see [Tutorial: Use automated machine learning to build your regression model](../../machine-learning/service/tutorial-auto-train-models.md).
All new HDInsight-Spark clusters come pre-installed with AzureML-AutoML SDK. You can get started with AutoML on HDInsight with this [sample Jupyter notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-hdi). This Jupyter Notebook demonstrates how to use an automated machine learning classifier for a simple classification problem.

> [!Note]
> Azure Machine Learning packages are installed into Python3 conda environment. The installed Jupyter notebook should be run using the PySpark3 kernel.

You can alternatively use Zeppelin notebooks to use AutoML as well.

> [!Note]
> Zeppelin has a [known issue](https://community.hortonworks.com/content/supportkb/207822/the-livypyspark3-interpreter-uses-python-2-instead.html) where PySpark3 doesn't pick the right version of Python. Please use the documented work-around.

## Authentication for workspace

Workspace creation and experiment submission require an authentication token. This token can be generated using an [Azure AD application](../../active-directory/develop/app-objects-and-service-principals.md). An [Azure AD user](https://docs.microsoft.com/python/azure/python-sdk-azure-authenticate?view=azure-python) can also be used to generate the required authentication token, if multi-factor authentication isn't enabled on the account.  

The following code snippet creates an authentication token using an **Azure AD application**.

```python
from azureml.core.authentication import ServicePrincipalAuthentication
auth_sp = ServicePrincipalAuthentication(
    tenant_id='<Azure Tenant ID>',
    service_principal_id='<Azure AD Application ID>',
    service_principal_password='<Azure AD Application Key>'
)
```
The following code snippet creates an authentication token using an **Azure AD user**.

```python
from azure.common.credentials import UserPassCredentials
credentials = UserPassCredentials('user@domain.com', 'my_smart_password')
```

## Loading dataset

Automated machine learning on Spark uses **Dataflows**, which are lazily evaluated, immutable operations on data.  A Dataflow can load a dataset from a blob with public read access, or from a blob URL with a SAS token.

```python
import azureml.dataprep as dprep

dataflow_public = dprep.read_csv(
    path='https://commonartifacts.blob.core.windows.net/automl/UCI_Adult_train.csv')

dataflow_with_token = dprep.read_csv(
    path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv?st=2018-06-15T23%3A01%3A42Z&se=2019-06-16T23%3A01%3A00Z&sp=r&sv=2017-04-17&sr=b&sig=ugQQCmeC2eBamm6ynM7wnI%2BI3TTDTM6z9RPKj4a%2FU6g%3D')
```

You can also register the datastore with the workspace using a one-time registration.

## Experiment submission

In the [automated machine learning configuration](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig), the property `spark_context` should be set for the package to run on distributed mode. The property `concurrent_iterations`, which is the maximum number of iterations executed in parallel, should be set to a number less than the executor cores for the Spark app.

## Next steps

* For more information on the motivation behind automated machine learning, see [Release models at pace using Microsoft’s automated machine learning!](https://azure.microsoft.com/blog/release-models-at-pace-using-microsoft-s-automl/)
* For more details on using Azure ML Automated ML capabilities, see [New automated machine learning capabilities in Azure Machine Learning service](https://azure.microsoft.com/blog/new-automated-machine-learning-capabilities-in-azure-machine-learning-service/)
* [AutoML project from Microsoft Research](https://www.microsoft.com/research/project/automl/)
