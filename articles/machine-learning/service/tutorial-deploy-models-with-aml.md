---
title: "Image classification tutorial: Deploy models"
titleSuffix: Azure Machine Learning service
description: This tutorial shows how to use Azure Machine Learning service to deploy an image classification model with scikit-learn in a Python Jupyter notebook. This tutorial is the second of a two-part series.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial

author: sdgilley
ms.author: sgilley
ms.date: 05/14/2019
ms.custom: seodec18
# As a professional data scientist, I can deploy the model previously trained in tutorial1.
---

# Tutorial: Deploy an image classification model in Azure Container Instances

This tutorial is **part two of a two-part tutorial series**. In the [previous tutorial](tutorial-train-models-with-aml.md), you trained machine learning models and then registered a model in your workspace on the cloud.  

Now you're ready to deploy the model as a web service in [Azure Container Instances](https://docs.microsoft.com/azure/container-instances/). A web service is an image, in this case a Docker image. It encapsulates the scoring logic and the model itself. 

In this part of the tutorial, you use Azure Machine Learning service for the following tasks:

> [!div class="checklist"]
> * Set up your testing environment.
> * Retrieve the model from your workspace.
> * Test the model locally.
> * Deploy the model to Container Instances.
> * Test the deployed model.

Container Instances is a great solution for testing and understanding the workflow. For scalable production deployments, consider using Azure Kubernetes Service. For more information, see [how to deploy and where](how-to-deploy-and-where.md).

>[!NOTE]
> Code in this article was tested with Azure Machine Learning SDK version 1.0.41.

## Prerequisites

To run the notebook, first complete the model training in [Tutorial (part 1): Train an image classification model](tutorial-train-models-with-aml.md).   Then open the  **tutorials/img-classification-part2-deploy.ipynb** notebook using the same notebook server.

This tutorial is also available on [GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials) if you wish to use it on your own [local environment](how-to-configure-environment.md#local).  Make sure you have installed `matplotlib` and `scikit-learn` in your environment. 

## <a name="start"></a>Set up the environment

Start by setting up a testing environment.

### Import packages

Import the Python packages needed for this tutorial:

```python
%matplotlib inline
import numpy as np
import matplotlib
import matplotlib.pyplot as plt
 
import azureml
from azureml.core import Workspace, Run

# display the core SDK version number
print("Azure ML SDK Version: ", azureml.core.VERSION)
```

### Retrieve the model

You registered a model in your workspace in the previous tutorial. Now load this workspace and download the model to your local directory:


```python
from azureml.core import Workspace
from azureml.core.model import Model
import os
ws = Workspace.from_config()
model = Model(ws, 'sklearn_mnist')

model.download(target_dir=os.getcwd(), exist_ok=True)

# verify the downloaded model file
file_path = os.path.join(os.getcwd(), "sklearn_mnist_model.pkl")

os.stat(file_path)
```

## Test the model locally

Before you deploy, make sure your model is working locally:
* Load test data.
* Predict test data.
* Examine the confusion matrix.

### Load test data

Load the test data from the **./data/** directory created during the training tutorial:

```python
from utils import load_data
import os

data_folder = os.path.join(os.getcwd(), 'data')
# note we also shrink the intensity values (X) from 0-255 to 0-1. This helps the neural network converge faster
X_test = load_data(os.path.join(data_folder, 'test-images.gz'), False) / 255.0
y_test = load_data(os.path.join(
    data_folder, 'test-labels.gz'), True).reshape(-1)
```

### Predict test data

To get predictions, feed the test dataset to the model:

```python
import pickle
from sklearn.externals import joblib

clf = joblib.load(os.path.join(os.getcwd(), 'sklearn_mnist_model.pkl'))
y_hat = clf.predict(X_test)
```

###  Examine the confusion matrix

Generate a confusion matrix to see how many samples from the test set are classified correctly. Notice the misclassified value for the incorrect predictions: 

```python
from sklearn.metrics import confusion_matrix

conf_mx = confusion_matrix(y_test, y_hat)
print(conf_mx)
print('Overall accuracy:', np.average(y_hat == y_test))
```

The output shows the confusion matrix:

    [[ 960    0    1    2    1    5    6    3    1    1]
     [   0 1112    3    1    0    1    5    1   12    0]
     [   9    8  920   20   10    4   10   11   37    3]
     [   4    0   17  921    2   21    4   12   20    9]
     [   1    2    5    3  915    0   10    2    6   38]
     [  10    2    0   41   10  770   17    7   28    7]
     [   9    3    7    2    6   20  907    1    3    0]
     [   2    7   22    5    8    1    1  950    5   27]
     [  10   15    5   21   15   27    7   11  851   12]
     [   7    8    2   13   32   13    0   24   12  898]]
    Overall accuracy: 0.9204
   

Use `matplotlib` to display the confusion matrix as a graph. In this graph, the x-axis shows the actual values, and the y-axis shows the predicted values. The color in each grid shows the error rate. The lighter the color, the higher the error rate is. For example, many 5's are misclassified as 3's. So you see a bright grid at (5,3):

```python
# normalize the diagonal cells so that they don't overpower the rest of the cells when visualized
row_sums = conf_mx.sum(axis=1, keepdims=True)
norm_conf_mx = conf_mx / row_sums
np.fill_diagonal(norm_conf_mx, 0)

fig = plt.figure(figsize=(8, 5))
ax = fig.add_subplot(111)
cax = ax.matshow(norm_conf_mx, cmap=plt.cm.bone)
ticks = np.arange(0, 10, 1)
ax.set_xticks(ticks)
ax.set_yticks(ticks)
ax.set_xticklabels(ticks)
ax.set_yticklabels(ticks)
fig.colorbar(cax)
plt.ylabel('true labels', fontsize=14)
plt.xlabel('predicted values', fontsize=14)
plt.savefig('conf.png')
plt.show()
```

![Chart showing confusion matrix](./media/tutorial-deploy-models-with-aml/confusion.png)

## Deploy as a web service

After you tested the model and you're satisfied with the results, deploy the model as a web service hosted in Container Instances. 

To build the correct environment for Container Instances, provide the following components:
* A scoring script to show how to use the model.
* An environment file to show what packages need to be installed.
* A configuration file to build the container instance.
* The model you trained previously.

<a name="make-script"></a>

### Create scoring script

Create the scoring script, called **score.py**. The web service call uses this script to show how to use the model.

Include these two required functions in the scoring script:
* The `init()` function, which typically loads the model into a global object. This function is run only once when the Docker container is started. 

* The `run(input_data)` function uses the model to predict a value based on the input data. Inputs and outputs to the run typically use JSON for serialization and de-serialization, but other formats are supported.

```python
%%writefile score.py
import json
import numpy as np
import os
import pickle
from sklearn.externals import joblib
from sklearn.linear_model import LogisticRegression

from azureml.core.model import Model

def init():
    global model
    # retrieve the path to the model file using the model name
    model_path = Model.get_model_path('sklearn_mnist')
    model = joblib.load(model_path)

def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    # make prediction
    y_hat = model.predict(data)
    # you can return any data type as long as it is JSON-serializable
    return y_hat.tolist()
```

<a name="make-myenv"></a>

### Create environment file

Next create an environment file, called **myenv.yml**, that specifies all of the script's package dependencies. This file is used to make sure that all of those dependencies are installed in the Docker image. This model needs `scikit-learn` and `azureml-sdk`:

```python
from azureml.core.conda_dependencies import CondaDependencies

myenv = CondaDependencies()
myenv.add_conda_package("scikit-learn")

with open("myenv.yml", "w") as f:
    f.write(myenv.serialize_to_string())
```
Review the content of the `myenv.yml` file:

```python
with open("myenv.yml", "r") as f:
    print(f.read())
```

### Create a configuration file

Create a deployment configuration file. Specify the number of CPUs and gigabytes of RAM needed for your Container Instances container. Although it depends on your model, the default of one core and 1 gigabyte of RAM is sufficient for many models. If you need more later, you have to re-create the image and redeploy the service.

```python
from azureml.core.webservice import AciWebservice

aciconfig = AciWebservice.deploy_configuration(cpu_cores=1,
                                               memory_gb=1,
                                               tags={"data": "MNIST",
                                                     "method": "sklearn"},
                                               description='Predict MNIST with sklearn')
```

### Deploy in Container Instances
The estimated time to finish deployment is **about seven to eight minutes**.

Configure the image and deploy. The following code goes through these steps:

1. Build an image by using these files:
   * The scoring file, `score.py`.
   * The environment file, `myenv.yml`.
   * The model file.
1. Register the image under the workspace. 
1. Send the image to the Container Instances container.
1. Start up a container in Container Instances by using the image.
1. Get the web service HTTP endpoint.


```python
%%time
from azureml.core.webservice import Webservice
from azureml.core.image import ContainerImage

# configure the image
image_config = ContainerImage.image_configuration(execution_script="score.py", 
                                                  runtime="python", 
                                                  conda_file="myenv.yml")

service = Webservice.deploy_from_model(workspace=ws,
                                       name='sklearn-mnist-svc',
                                       deployment_config=aciconfig,
                                       models=[model],
                                       image_config=image_config)

service.wait_for_deployment(show_output=True)
```

Get the scoring web service's HTTP endpoint, which accepts REST client calls. You can share this endpoint with anyone who wants to test the web service or integrate it into an application: 

```python
print(service.scoring_uri)
```


## Test the deployed service

Earlier, you scored all the test data with the local version of the model. Now you can test the deployed model with a random sample of 30 images from the test data.  

The following code goes through these steps:
1. Send the data as a JSON array to the web service hosted in Container Instances. 

1. Use the SDK's `run` API to invoke the service. You can also make raw calls by using any HTTP tool such as **curl**.

1. Print the returned predictions and plot them along with the input images. Red font and inverse image, white on black, is used to highlight the misclassified samples. 

Because the model accuracy is high, you might have to run the following code a few times before you can see a misclassified sample:

```python
import json

# find 30 random samples from test set
n = 30
sample_indices = np.random.permutation(X_test.shape[0])[0:n]

test_samples = json.dumps({"data": X_test[sample_indices].tolist()})
test_samples = bytes(test_samples, encoding='utf8')

# predict using the deployed model
result = service.run(input_data=test_samples)

# compare actual value vs. the predicted values:
i = 0
plt.figure(figsize=(20, 1))

for s in sample_indices:
    plt.subplot(1, n, i + 1)
    plt.axhline('')
    plt.axvline('')

    # use different color for misclassified sample
    font_color = 'red' if y_test[s] != result[i] else 'black'
    clr_map = plt.cm.gray if y_test[s] != result[i] else plt.cm.Greys

    plt.text(x=10, y=-10, s=result[i], fontsize=18, color=font_color)
    plt.imshow(X_test[s].reshape(28, 28), cmap=clr_map)

    i = i + 1
plt.show()
```

This result is from one random sample of test images:

![Graphic showing results](./media/tutorial-deploy-models-with-aml/results.png)

You can also send a raw HTTP request to test the web service:

```python
import requests

# send a random row from the test set to score
random_index = np.random.randint(0, len(X_test)-1)
input_data = "{\"data\": [" + str(list(X_test[random_index])) + "]}"

headers = {'Content-Type': 'application/json'}

# for AKS deployment you'd need to the service key in the header as well
# api_key = service.get_key()
# headers = {'Content-Type':'application/json',  'Authorization':('Bearer '+ api_key)}

resp = requests.post(service.scoring_uri, input_data, headers=headers)

print("POST to url", service.scoring_uri)
#print("input data:", input_data)
print("label:", y_test[random_index])
print("prediction:", resp.text)
```

## Clean up resources

To keep the resource group and workspace for other tutorials and exploration, you can delete only the Container Instances deployment by using this API call:

```python
service.delete()
```

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]


## Next steps

+ Learn about all of the [deployment options for Azure Machine Learning service](how-to-deploy-and-where.md).
+ Learn how to [create clients for the web service](how-to-consume-web-service.md).
+  [Make predictions on large quantities of data](how-to-run-batch-predictions.md) asynchronously.
+ Monitor your Azure Machine Learning models with [Application Insights](how-to-enable-app-insights.md).
+ Try out the [automatic algorithm selection](tutorial-auto-train-models.md) tutorial. 
