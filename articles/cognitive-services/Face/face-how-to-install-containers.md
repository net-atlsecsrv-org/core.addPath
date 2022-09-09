---
title: Install and run containers
titlesuffix: Face - Azure Cognitive Services
description: Download, install, and run containers for Face in this walkthrough tutorial.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: article
ms.date: 06/10/2019
ms.author: dapine
---

# Install and run Face containers

Azure Cognitive Services Face provides a standardized Linux container for Docker that detects human faces in images. It also identifies attributes, which include face landmarks such as noses and eyes, gender, age, and other machine-predicted facial features. In addition to detection, Face can check if two faces in the same image or different images are the same by using a confidence score. Face also can compare faces against a database to see if a similar-looking or identical face already exists. It also can organize similar faces into groups by using shared visual traits.

If you don't have an Azure subscription, create a [free account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) before you begin.

## Prerequisites

You must meet the following prerequisites before you use the Face API containers.

|Required|Purpose|
|--|--|
|Docker Engine| The Docker Engine must be installed on a [host computer](#the-host-computer). Docker provides packages that configure the Docker environment on [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/), and [Linux](https://docs.docker.com/engine/installation/#supported-platforms). For a primer on Docker and container basics, see the [Docker overview](https://docs.docker.com/engine/docker-overview/).<br><br> Docker must be configured to allow the containers to connect with and send billing data to Azure. <br><br> On Windows, Docker also must be configured to support Linux containers.<br><br>|
|Familiarity with Docker | You need a basic understanding of Docker concepts, such as registries, repositories, containers, and container images. You also need knowledge of basic `docker` commands.| 
|Azure `Cognitive Services` resource |To use the container, you must have:<br><br>An Azure Cognitive Services resource and the associated billing key and the billing endpoint URI. Both values are available on the **Overview** and **Keys** pages for the resource. They're required to start the container. Add the `face/v1.0` routing to the endpoint URI, as shown in the following BILLING_ENDPOINT_URI example: <br><br>**{BILLING_KEY}**: resource key<br><br>**{BILLING_ENDPOINT_URI}**: endpoint URI example is `https://westus.api.cognitive.microsoft.com/face/v1.0`|

## Request access to the private container registry

[!INCLUDE [Request access to private container registry](../../../includes/cognitive-services-containers-request-access.md)]

### The host computer

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]


### Container requirements and recommendations

The following table describes the minimum and recommended CPU cores and memory to allocate for each Face API container.

| Container | Minimum | Recommended | Transactions per second<br>(Minimum, maximum)|
|-----------|---------|-------------|--|
|Face | 1 core, 2-GB memory | 1 core, 4-GB memory |10, 20|

* Each core must be at least 2.6 GHz or faster.
* Transactions per second (TPS).

Core and memory correspond to the `--cpus` and `--memory` settings, which are used as part of the `docker run` command.

## Get the container image with docker pull

Container images for the Face API are available. 

| Container | Repository |
|-----------|------------|
| Face | `containerpreview.azurecr.io/microsoft/cognitive-services-face:latest` |

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### Docker pull for the Face container

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-face:latest
```

## Use the container

After the container is on the [host computer](#the-host-computer), use the following process to work with the container.

1. [Run the container](#run-the-container-with-docker-run) with the required billing settings. More [examples](./face-resource-container-config.md#example-docker-run-commands) of the `docker run` command are available. 
1. [Query the container's prediction endpoint](#query-the-containers-prediction-endpoint). 

## Run the container with docker run

Use the [docker run](https://docs.docker.com/engine/reference/commandline/run/) command to run any of the three containers. The command uses the following parameters.

| Placeholder | Value |
|-------------|-------|
|{BILLING_KEY} | This key is used to start the container and is available on the Azure `Cognitive Services` **Keys** page. |
|{BILLING_ENDPOINT_URI} | The billing endpoint URI value is available on the Azure `Cognitive Services` **Overview** page. An example is `https://westus.api.cognitive.microsoft.com/face/v1.0`.|

Add the `face/v1.0` routing to the endpoint URI, as shown in the preceding BILLING_ENDPOINT_URI example. 

Replace these parameters with your own values in the following `docker run` command example:

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-face \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

This command:

* Runs a face container from the container image.
* Allocates one CPU core and 4 GB of memory.
* Exposes TCP port 5000 and allocates a pseudo TTY for the container.
* Automatically removes the container after it exits. The container image is still available on the host computer. 

More [examples](./face-resource-container-config.md#example-docker-run-commands) of the `docker run` command are available. 

> [!IMPORTANT]
> The `Eula`, `Billing`, and `ApiKey` options must be specified to run the container or the container won't start. For more information, see [Billing](#billing).

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]


## Query the container's prediction endpoint

The container provides REST-based query prediction endpoint APIs. 

Use the host, `https://localhost:5000`, for container APIs.


<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## Stop the container

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## Troubleshooting

If you run the container with an output [mount](./face-resource-container-config.md#mount-settings) and logging is enabled, the container generates log files that are helpful to troubleshoot issues that happen while you start or run the container. 


## Billing

The Face API containers send billing information to Azure by using a Face API resource on your Azure account. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

For more information about these options, see [Configure containers](./face-resource-container-config.md).

<!--blogs/samples/video coures -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## Summary

In this article, you learned concepts and workflow for how to download, install, and run Face API containers. In summary:

* The Face API provides three Linux containers for Docker that provide key phrase extraction, language detection, and sentiment analysis.
* Container images are downloaded from the Azure Container Registry.
* Container images run in Docker.
* You can use either the REST API or the SDK to call operations in Face API containers by specifying the host URI of the container.
* You must specify billing information when you instantiate a container.

> [!IMPORTANT]
> Cognitive Services containers aren't licensed to run without being connected to Azure for metering. Customers must enable the containers to communicate billing information with the metering service at all times. Cognitive Services containers don't send customer data, such as the image or text that's being analyzed, to Microsoft.

## Next steps

* For configuration settings, see [Configure containers](face-resource-container-config.md).
* To learn more about how to detect and identify faces, see [Face overview](Overview.md).
* For information about the methods supported by the container, see the [Face API](//westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).
* To use more Cognitive Services containers, see [Cognitive Services containers](../cognitive-services-container-support.md).
