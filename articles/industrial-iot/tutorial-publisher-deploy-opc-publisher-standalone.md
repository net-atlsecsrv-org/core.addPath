---
title: Deploy the Microsoft OPC Publisher
description: In this tutorial you learn how to deploy the OPC Publisher in standalone mode.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: tutorial
ms.date: 3/22/2021
---

# Tutorial: Deploy the OPC Publisher

OPC Publisher is a fully supported Microsoft product, developed in the open, that bridges the gap between industrial assets and the Microsoft Azure cloud. It does so by connecting to OPC UA-enabled assets or industrial connectivity software and publishes telemetry data to [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) in various formats, including IEC62541 OPC UA PubSub standard format (from version 2.6 onwards).

It runs on [Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/) as a Module or on plain Docker as a container. Since it leverages the [.NET cross-platform runtime](/dotnet/core/introduction), it also runs natively on Linux and Windows 10.

In this tutorial, you learn how to:

> [!div class="checklist"]
> * Deploy the OPC Publisher
> * Run the latest released version of OPC Publisher as a container
> * Specify Container Create Options in the Azure portal

If you don’t have an Azure subscription, create a free trial account

## Prerequisites

- An IoT Hub must be created
- An IoT Edge device must be created

## Deploy the OPC Publisher from the Azure Marketplace

1. Pick the Azure subscription to use. If no Azure subscription is available, one must be created.
2. Pick the IoT Hub the OPC Publisher is supposed to send data to. If no IoT Hub is available, one must be created.
3. Pick the IoT Edge device the OPC Publisher is supposed to run on (or enter a name for a new IoT Edge device to be created).
4. Click Create. The "Set modules on Device" page for the selected IoT Edge device opens.
5. Click on "OPCPublisher" to open the OPC Publisher's "Update IoT Edge Module" page and then select "Container Create Options".
6. Specify additional container create options based on your usage of OPC Publisher, see next section below.


### Accessing the Microsoft Container Registry Docker containers for OPC Publisher manually

The latest released version of OPC Publisher can be run manually via:

```
docker run mcr.microsoft.com/iotedge/opc-publisher:latest <name>
```

Where "name" is the name for the container.

## Specifying Container Create Options in the Azure portal
When deploying OPC Publisher through the Azure portal, container create options can be specified in the Update IoT Edge Module page of OPC Publisher. These create options must be in JSON format. The OPC Publisher command line arguments can be specified via the Cmd key, e.g.:
```
"Cmd": [
    "--pf=./pn.json",
    "--aa"
],
```

A typical set of IoT Edge Module Container Create Options for OPC Publisher is:
```
{
    "Hostname": "opcpublisher",
    "Cmd": [
        "--pf=./pn.json",
        "--aa"
    ],
    "HostConfig": {
        "Binds": [
            "/iiotedge:/appdata"
        ]
    }
}
```

With these options specified, OPC Publisher will read the configuration file `./pn.json`. The OPC Publisher's working directory is set to
`/appdata` at startup and thus OPC Publisher will read the file `/appdata/pn.json` inside its Docker container. 
OPC Publisher's log file will be written to `/appdata` and the `CertificateStores` directory (used for OPC UA certificates) will also be created in this directory. To make these files available in the IoT Edge host file system, the container configuration requires a bind mount volume. The `/iiotedge:/appdata` bind will map the directory `/appdata` to the host directory `/iiotedge` (which will be created by the IoT Edge runtime if it doesn't exist).
**Without this bind mount volume, all OPC Publisher configuration files will be lost when the container is restarted.**

A connection to an OPC UA server using its hostname without a DNS server configured on the network can be achieved by adding an `ExtraHosts` entry to the `HostConfig` section:

```
"HostConfig": {
    "ExtraHosts": [
        "opctestsvr:192.168.178.26"
    ]
}
```

## Next steps 
Now that you have deployed the OPC Publisher Edge module, the next step is to configure it:

> [!div class="nextstepaction"]
> [Configure the OPC Publisher](tutorial-publisher-configure-opc-publisher.md)