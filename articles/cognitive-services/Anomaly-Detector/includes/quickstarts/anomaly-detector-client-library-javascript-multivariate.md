---
title: Anomaly Detector multivariate JavaScript client library quickstart 
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/29/2021
ms.author: mbullwin
---

Get started with the Anomaly Detector multivariate client library for JavaScript. Follow these steps to install the package and start using the algorithms provided by the service. The new multivariate anomaly detection APIs enable developers by easily integrating advanced AI for detecting anomalies from groups of metrics, without the need for machine learning knowledge or labeled data. Dependencies and inter-correlations between different signals are automatically counted as key factors. This helps you to proactively protect your complex systems from failures.

Use the Anomaly Detector multivariate client library for JavaScript to:

* Detect system level anomalies from a group of time series.
* When any individual time series won't tell you much and you have to look at all signals to detect a problem.
* Predicative maintenance of expensive physical assets with tens to hundreds of different types of sensors measuring various aspects of system health.

[Library source code](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/anomalydetector/ai-anomaly-detector) | [Package (npm)](https://www.npmjs.com/package/@azure/ai-anomaly-detector) | [Sample code](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/anomalydetector/ai-anomaly-detector/samples/v3/javascript/sample_multivariate_detection.js)

## Prerequisites

* Azure subscription - [Create one for free](https://azure.microsoft.com/free/cognitive-services)
* The current version of [Node.js](https://nodejs.org/)
* Once you have your Azure subscription, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title="Create an Anomaly Detector resource"  target="_blank">create an Anomaly Detector resource </a> in the Azure portal to get your key and endpoint. Wait for it to deploy and click the **Go to resource** button.
    * You will need the key and endpoint from the resource you create to connect your application to the Anomaly Detector API. You'll paste your key and endpoint into the code below later in the quickstart.
    You can use the free pricing tier (`F0`) to try the service, and upgrade later to a paid tier for production.

## Setting up

### Create a new Node.js application

In a console window (such as cmd, PowerShell, or Bash), create a new directory for your app, and navigate to it. 

```console
mkdir myapp && cd myapp
```

Run the `npm init` command to create a node application with a `package.json` file. 

```console
npm init
```

Create a file named `index.js` and import the following libraries:
`
```javascript
'use strict'

const fs = require('fs');
const parse = require("csv-parse/lib/sync");
const { AnomalyDetectorClient } = require('@azure/ai-anomaly-detector');
const { AzureKeyCredential } = require('@azure/core-auth');
```

Create variables your resource's Azure endpoint and key. Create another variable for the example data file.

```javascript
const apiKey = "YOUR_API_KEY";
const endpoint = "YOUR_ENDPOINT";
const data_source = "YOUR_SAMPLE_ZIP_FILE_LOCATED_IN_AZURE_BLOB_STORAGE_WITH_SAS";
```

To use the Anomaly Detector multivariate APIs, you need to first train your own models. Training data is a set of multiple time series that meet the following requirements:

Each time series should be a CSV file with two (and only two) columns, "timestamp" and "value" (all in lowercase) as the header row. The "timestamp" values should conform to ISO 8601; the "value" could be integers or decimals with any number of decimal places. For example:

|timestamp | value|
|-------|-------|
|2019-04-01T00:00:00Z| 5|
|2019-04-01T00:01:00Z| 3.6|
|2019-04-01T00:02:00Z| 4|
|`...`| `...` |

Each CSV file should be named after a different variable that will be used for model training. For example, "temperature.csv" and "humidity.csv". All the CSV files should be zipped into one zip file without any subfolders. The zip file can have whatever name you want. The zip file should be uploaded to Azure Blob storage. Once you generate the blob SAS (Shared access signatures) URL for the zip file, it can be used for training. Refer to this document for how to generate SAS URLs from Azure Blob Storage.

### Install the client library

Install the `ms-rest-azure` and `azure-ai-anomalydetector` NPM packages. The csv-parse library is also used in this quickstart:

```console
npm install @azure/ai-anomaly-detector csv-parse
```

Your app's `package.json` file will be updated with the dependencies.

## Code examples

These code snippets show you how to do the following with the Anomaly Detector client library for Node.js:

* [Authenticate the client](#authenticate-the-client)
* [Train a model](#train-a-model)
* [Detect anomalies](#detect-anomalies)
* [Export model](#export-model)
* [Delete model](#delete-model)

## Authenticate the client

Instantiate a `AnomalyDetectorClient` object with your endpoint and credentials.

```javascript
const client = new AnomalyDetectorClient(endpoint, new AzureKeyCredential(apiKey));
```

## Train a model

### Construct a model result

First we need to construct a model request. Make sure that start and end time align with your data source.

```javascript
const Modelrequest = {
      source: data_source,
      startTime: new Date(2021,0,1,0,0,0),
      endTime: new Date(2021,0,2,12,0,0),
      slidingWindow:200
    };    
```

### Train a new model

You will need to pass your model request to the Anomaly Detector client `trainMultivariateModel` method.

```javascript
console.log("Training a new model...")
const train_response = await client.trainMultivariateModel(Modelrequest)
const model_id = train_response.location?.split("/").pop() ?? ""
console.log("New model ID: " + model_id)
```

To check if training of your model is complete you can track the model's status:

```javascript
let model_response = await client.getMultivariateModel(model_id)
let model_status = model_response.modelInfo?.status

while (model_status != 'READY'){
    await sleep(10000).then(() => {});
    model_response = await client.getMultivariateModel(model_id)
    model_status = model_response.modelInfo?.status
}

console.log("TRAINING FINISHED.")
```

## Detect anomalies

Use the `detectAnomaly` and `getDectectionResult` functions to determine if there are any anomalies within your datasource.

```javascript
console.log("Start detecting...")
const detect_request = {
    source: data_source,
    startTime: new Date(2021,0,2,12,0,0),
    endTime: new Date(2021,0,3,0,0,0)
};
const result_header = await client.detectAnomaly(model_id, detect_request)
const result_id = result_header.location?.split("/").pop() ?? ""
let result = await client.getDetectionResult(result_id)
let result_status = result.summary.status

while (result_status != 'READY'){
    await sleep(2000).then(() => {});
    result = await client.getDetectionResult(result_id)
    result_status = result.summary.status
}
```

## Export model

To export your trained model use the `exportModel` function.

```javascript
const export_result = await client.exportModel(model_id)
const model_path = "model.zip"
const destination = fs.createWriteStream(model_path)
export_result.readableStreamBody?.pipe(destination)
console.log("New model has been exported to "+model_path+".")
```

## Delete model

To delete an existing model that is available to the current resource use the `deleteMultivariateModel` function.

```javascript
client.deleteMultivariateModel(model_id)
console.log("New model has been deleted.")
```

## Run the application

Before running the application it can be helpful to check your code against the [full sample code](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/anomalydetector/ai-anomaly-detector/samples/v3/javascript/sample_multivariate_detection.js)

Run the application with the `node` command on your quickstart file.

```console
node index.js
```

## Next steps

* [Anomaly Detector multivariate best practices](../../concepts/best-practices-multivariate.md)
