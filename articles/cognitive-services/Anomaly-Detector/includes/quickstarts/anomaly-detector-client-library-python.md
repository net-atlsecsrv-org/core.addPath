---
title: Anomaly Detector Python client library quickstart
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/22/2020
ms.author: aahi
---

Get started with the Anomaly Detector client library for Python. Follow these steps to install the package start using the algorithms provided by the service. The Anomaly Detector service enables you to find abnormalities in your time series data by automatically using the best-fitting models on it, regardless of industry, scenario, or data volume.

Use the Anomaly Detector client library for Python to:

* Detect anomalies throughout your time series data set, as a batch request
* Detect the anomaly status of the latest data point in your time series
* Detect trend change points in your data set.

[Library reference documentation](https://go.microsoft.com/fwlink/?linkid=2090370) | [Library source code](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-anomalydetector) | [Package (PyPi)](https://pypi.org/project/azure-ai-anomalydetector/) | [Find the sample code on GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/sdk/python-sdk-sample.py)

## Prerequisites

* [Python 3.x](https://www.python.org/)
* The [Pandas data analysis library](https://pandas.pydata.org/)
* Azure subscription - [Create one for free](https://azure.microsoft.com/free/cognitive-services)
* Once you have your Azure subscription, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title="Create an Anomaly Detector resource"  target="_blank">create an Anomaly Detector resource <span class="docon docon-navigate-external x-hidden-focus"></span></a> in the Azure portal to get your key and endpoint. Wait for it to deploy and click the **Go to resource** button.
    * You will need the key and endpoint from the resource you create to connect your application to the Anomaly Detector API. You'll paste your key and endpoint into the code below later in the quickstart.
    You can use the free pricing tier (`F0`) to try the service, and upgrade later to a paid tier for production.


## Setting up

[!INCLUDE [anomaly-detector-environment-variables](../environment-variables.md)]

### Create a new python application

 Create a new Python file and import the following libraries.

[!code-python[import declarations](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=imports)]

Create variables for your key as an environment variable, the path to a time series data file, and the Azure location of your subscription. For example, `westus2`.

[!code-python[Vars for the key, path location and data path](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=initVars)]

### Install the client library

After installing Python, you can install the client library with:

```console
pip install --upgrade azure-ai-anomalydetector
```

## Object model

The Anomaly Detector client is a [AnomalyDetectorClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python) object that authenticates to Azure using your key. The client can do anomaly detection an entire dataset using [entire_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#entire-detect-body--custom-headers-none--raw-false----operation-config-), or on the latest data point using [Last_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#last-detect-body--custom-headers-none--raw-false----operation-config-). The [ChangePointDetectAsync](https://go.microsoft.com/fwlink/?linkid=2090370) function detects points that mark changes in a trend.

Time series data is sent as a series of [Points](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.point?view=azure-python) in a [Request](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.request?view=azure-python) object. The `Request` object contains properties to describe the data ([Granularity](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.granularity?view=azure-python) for example), and parameters for the anomaly detection.

The Anomaly Detector response is a [LastDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-python), [EntireDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-python), or [ChangePointDetectResponse](https://go.microsoft.com/fwlink/?linkid=2090370) object depending on the method used.

## Code examples

These code snippets show you how to do the following with the Anomaly Detector client library for Python:

* [Authenticate the client](#authenticate-the-client)
* [Load a time series data set from a file](#load-time-series-data-from-a-file)
* [Detect anomalies in the entire data set](#detect-anomalies-in-the-entire-data-set)
* [Detect the anomaly status of the latest data point](#detect-the-anomaly-status-of-the-latest-data-point)
* [Detect the change points in the data set](#detect-change-points-in-the-data-set)

## Authenticate the client

Add your Azure location variable to the endpoint, and authenticate the client with your key.

[!code-python[Client authentication](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=client)]

## Load time series data from a file

Download the example data for this quickstart from [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/example-data/request-data.csv):
1. In your browser, right-click **Raw**.
2. Click **Save link as**.
3. Save the file to your application directory, as a .csv file.

This time series data is formatted as a .csv file, and will be sent to the Anomaly Detector API.

Load your data file with the Pandas library's `read_csv()` method, and make an empty list variable to store your data series. Iterate through the file, and append the data as a [Point](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.point?view=azure-python) object. This object will contain the timestamp and numerical value from the rows of your .csv data file.

[!code-python[Load the data file](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=loadDataFile)]

Create a [Request](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.request?view=azure-python) object with your time series, and the [granularity](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.granularity?view=azure-python) (or periodicity) of its data points. For example, `Granularity.daily`.

[!code-python[Create the request object](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=request)]

## Detect anomalies in the entire data set

Call the API to detect anomalies through the entire time series data using the client's [entire_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#entire-detect-body--custom-headers-none--raw-false----operation-config-) method. Store the returned [EntireDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-python) object. Iterate through the response's `is_anomaly` list, and print the index of any `true` values. These values correspond to the index of anomalous data points, if any were found.

[!code-python[Batch anomaly detection sample](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=detectAnomaliesBatch)]

## Detect the anomaly status of the latest data point

Call the Anomaly Detector API to determine if your latest data point is an anomaly using the client's [last_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#last-detect-body--custom-headers-none--raw-false----operation-config-) method, and store the returned [LastDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-python) object. The response's `is_anomaly` value is a boolean that specifies that point's anomaly status.  

[!code-python[Batch anomaly detection sample](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=latestPointDetection)]

## Detect change points in the data set

Call the API to detect change points in the time series data using the client's [detect_change_point()](https://go.microsoft.com/fwlink/?linkid=2090370) method. Store the returned [ChangePointDetectResponse](https://go.microsoft.com/fwlink/?linkid=2090370) object. Iterate through the response's `is_change_point` list, and print the index of any `true` values. These values correspond to the indices of trend change points, if any were found.

[!code-python[detect change points](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=changePointDetection)]

## Run the application

Run the application with the `python` command and your file name.

[!INCLUDE [anomaly-detector-next-steps](../quickstart-cleanup-next-steps.md)]
