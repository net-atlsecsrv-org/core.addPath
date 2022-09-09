---
title: Tutorial - Connect a generic Node.js client app to Azure IoT Central | Microsoft Docs
description: This tutorial shows you how, as a device developer, to connect a device running a Node.js client app to your Azure IoT Central application. You modify the automatically generated device template by adding views that let an operator interact with a connected device.
author: dominicbetts
ms.author: dobett
ms.date: 11/03/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: [mqtt, device-developer, devx-track-js]

# As a device developer, I want to try out using Node.js device code that uses the Azure IoT Node.js SDK. I want to understand how to send telemetry from a device, synchronize properties with the device, and control the device using commands.
---

# Tutorial: Create and connect a client application to your Azure IoT Central application (Node.js)

[!INCLUDE [iot-central-selector-tutorial-connect](../../../includes/iot-central-selector-tutorial-connect.md)]

*This article applies to solution builders and device developers.*

This tutorial shows you how, as a device developer, to connect a Node.js client application to your Azure IoT Central application. The Node.js application simulates the behavior of a thermostat device. When the application connects to IoT Central, it sends the model ID of the thermostat device model. IoT Central uses the model ID to retrieve the device model and create a device template for you. You add customizations and views to the device template to enable an operator to interact with a device.

In this tutorial, you learn how to:

> [!div class="checklist"]
> * Create and run the Node.js device code and see it connect to your IoT Central application.
> * View the simulated telemetry sent from the device.
> * Add custom views to a device template.
> * Publish the device template.
> * Use a view to manage device properties.
> * Call a command to control the device.

## Prerequisites

To complete the steps in this article, you need the following:

* An Azure IoT Central application created using the **Custom application** template. For more information, see the [create an application quickstart](quick-deploy-iot-central.md). The application must have been created on or after 14 July 2020.
* A development machine with [Node.js](https://nodejs.org/) version 6 or later installed. You can run `node --version` in the command line to check your version. The instructions in this tutorial assume you're running the **node** command at the Windows command prompt. However, you can use Node.js on many other operating systems.
* A local copy of the [Microsoft Azure IoT SDK for Node.js](https://github.com/Azure/azure-iot-sdk-node) GitHub repository that contains the sample code. Use this link to download a copy of the repository: [Download ZIP](https://github.com/Azure/azure-iot-sdk-node/archive/master.zip). Then unzip the file to a suitable location on your local machine.

## Review the code

In the copy of the Microsoft Azure IoT SDK for Node.js you downloaded previously, open the *azure-iot-sdk-node/device/samples/pnp/simple_thermostat.js* file in a text editor.

When you run the sample to connect to IoT Central, it uses the Device Provisioning Service (DPS) to register the device and generate a connection string. The sample retrieves the DPS connection information it needs from the command-line environment.

The `main` method:

* Creates a `client` object and sets the `dtmi:com:example:Thermostat;1` model ID before it opens the connection.
* Creates a command handler.
* Starts a loop to send temperature telemetry every 10 seconds.
* Sends the `maxTempSinceLastReboot` property to IoT Central. IoT Central ignores the `serialNumber` property because it's not part of the device model.
* Creates a writable properties handler.

```javascript
async function main() {

  // ...

  // fromConnectionString must specify a transport, coming from any transport package.
  const client = Client.fromConnectionString(deviceConnectionString, Protocol);

  let resultTwin;
  try {
    // Add the modelId here
    await client.setOptions(modelIdObject);
    await client.open();

    client.onDeviceMethod(commandMaxMinReport, commandHandler);

    // Send Telemetry every 10 secs
    let index = 0;
    intervalToken = setInterval(() => {
      sendTelemetry(client, index).catch((err) => console.log('error', err.toString()));
      index += 1;
    }, telemetrySendInterval);

    // attach a standard input exit listener
    attachExitHandler(client);

    // Deal with twin
    try {
      resultTwin = await client.getTwin();
      const patchRoot = createReportPropPatch({ serialNumber: deviceSerialNum });
      const patchThermostat = createReportPropPatch({
        maxTempSinceLastReboot: deviceTemperatureSensor.getMaxTemperatureValue()
      });

      // the below things can only happen once the twin is there
      updateComponentReportedProperties(resultTwin, patchRoot);
      updateComponentReportedProperties(resultTwin, patchThermostat);

      // Setup the handler for desired properties
      desiredPropertyPatchHandler(resultTwin);

    } catch (err) {
      console.error('could not retrieve twin or report twin properties\n' + err.toString());
    }
  } catch (err) {
    console.error('could not connect Plug and Play client or could not attach interval function for telemetry\n' + err.toString());
  }
}
```

The `provisionDevice` function shows how the device uses DPS to register and connect to IoT Central. The payload includes the model ID:

```javascript
async function provisionDevice(payload) {
  var provSecurityClient = new SymmetricKeySecurityClient(registrationId, symmetricKey);
  var provisioningClient = ProvisioningDeviceClient.create(provisioningHost, idScope, new ProvProtocol(), provSecurityClient);

  if (!!(payload)) {
    provisioningClient.setProvisioningPayload(payload);
  }

  try {
    let result = await provisioningClient.register();
    deviceConnectionString = 'HostName=' + result.assignedHub + ';DeviceId=' + result.deviceId + ';SharedAccessKey=' + symmetricKey;
  } catch (err) {
    console.error("error registering device: " + err.toString());
  }
}
```

The `sendTelemetry` function shows how the device sends the temperature telemetry to IoT Central. The `getCurrentTemperatureObject` method returns an object that looks like `{ temperature: 45.6 }`:

```javascript
async function sendTelemetry(deviceClient, index) {
  console.log('Sending telemetry message %d...', index);
  const msg = new Message(
    JSON.stringify(
      deviceTemperatureSensor.updateSensor().getCurrentTemperatureObject()
    )
  );
  msg.contentType = 'application/json';
  msg.contentEncoding = 'utf-8';
  await deviceClient.sendEvent(msg);
}
```

The `main` method uses the following two methods to send the `maxTempSinceLastReboot` property to IoT Central. The `main` method calls `createReportPropPatch` with an object that looks like `{maxTempSinceLastReboot: 80.9}`:

```javascript
const createReportPropPatch = (propertiesToReport) => {
  let patch;
  patch = { };
  patch = propertiesToReport;
  return patch;
};

const updateComponentReportedProperties = (deviceTwin, patch) => {
  deviceTwin.properties.reported.update(patch, function (err) {
    if (err) throw err;
    console.log('Properties have been reported for component');
  });
};
```

The `main` method uses the following two methods to handle updates to the _target temperature_ writable property from IoT Central. Notice how `propertyUpdateHandle` builds the response with the version and status code:

```javascript
const desiredPropertyPatchHandler = (deviceTwin) => {
  deviceTwin.on('properties.desired', (delta) => {
    const versionProperty = delta.$version;

    Object.entries(delta).forEach(([propertyName, propertyValue]) => {
      if (propertyName !== '$version') {
        propertyUpdateHandler(deviceTwin, propertyName, null, propertyValue, versionProperty);
      }
    });
  });
};

const propertyUpdateHandler = (deviceTwin, propertyName, reportedValue, desiredValue, version) => {
  console.log('Received an update for property: ' + propertyName + ' with value: ' + JSON.stringify(desiredValue));
  const patch = createReportPropPatch(
    { [propertyName]:
      {
        'value': desiredValue,
        'ac': 200,
        'ad': 'Successfully executed patch for ' + propertyName,
        'av': version
      }
    });
  updateComponentReportedProperties(deviceTwin, patch);
  console.log('updated the property');
};
```

The `main` method uses the following two methods to handle calls to the `getMaxMinReport` command. The `getMaxMinReportObject` method generates the report as a JSON object:

```javascript
const commandHandler = async (request, response) => {
  switch (request.methodName) {
  case commandMaxMinReport: {
    console.log('MaxMinReport ' + request.payload);
    await sendCommandResponse(request, response, 200, deviceTemperatureSensor.getMaxMinReportObject());
    break;
  }
  default:
    await sendCommandResponse(request, response, 404, 'unknown method');
    break;
  }
};

const sendCommandResponse = async (request, response, status, payload) => {
  try {
    await response.send(status, payload);
    console.log('Response to method \'' + request.methodName +
              '\' sent successfully.' );
  } catch (err) {
    console.error('An error ocurred when sending a method response:\n' +
              err.toString());
  }
};
```

## Get connection information

[!INCLUDE [iot-central-connection-configuration](../../../includes/iot-central-connection-configuration.md)]

## Run the code

To run the sample application, open a command-line environment and navigate to the folder *azure-iot-sdk-node/device/samples/pnp* folder that contains the *simple_thermostat.js* sample file.

[!INCLUDE [iot-central-connection-environment](../../../includes/iot-central-connection-environment.md)]

Install the required packages:

```cmd/sh
npm install
```

Run the sample:

```cmd/sh
node simple_thermostat.js
```

The following output shows the device registering and connecting to IoT Central. The sample then sends the `maxTempSinceLastReboot` property before it starts sending telemetry:

```cmd/sh
registration succeeded
assigned hub=iotc-.......azure-devices.net
deviceId=sample-device-01
payload=undefined
Connecting using connection string HostName=iotc-........azure-devices.net;DeviceId=sample-device-01;SharedAccessKey=Ci....=
Enabling the commands on the client
Please enter q or Q to exit sample.
The following properties will be updated for root interface:
{ maxTempSinceLastReboot: 55.20309427428496 }
Properties have been reported for component
Sending telemetry message 0...
Sending telemetry message 1...
Sending telemetry message 2...
Sending telemetry message 3...
```

[!INCLUDE [iot-central-monitor-thermostat](../../../includes/iot-central-monitor-thermostat.md)]

You can see how the device responds to commands and property updates:

```cmd/sh
MaxMinReport 2020-10-15T12:00:00.000Z
Response to method 'getMaxMinReport' sent successfully.

...

Received an update for property: targetTemperature with value: {"value":86.3}
The following properties will be updated for root interface:
{
  targetTemperature: {
    value: { value: 86.3 },
    ac: 200,
    ad: 'Successfully executed patch for targetTemperature',
    av: 2
  }
}
```

## View raw data

[!INCLUDE [iot-central-monitor-thermostat-raw-data](../../../includes/iot-central-monitor-thermostat-raw-data.md)]

## Next steps

If you'd prefer to continue through the set of IoT Central tutorials and learn more about building an IoT Central solution, see:

> [!div class="nextstepaction"]
> [Create a gateway device template](./tutorial-define-gateway-device-type.md)

As a device developer, now that you've learned the basics of how to create a device using Node.js, some suggested next steps are to:

* Read [What are device templates?](./concepts-device-templates.md) to learn more about the role of device templates when you're implementing your device code.
* Read [Get connected to Azure IoT Central](./concepts-get-connected.md) to learn more about how to register devices with IoT Central and how IoT Central secures device connections.
* Read [Telemetry, property, and command payloads](concepts-telemetry-properties-commands.md) to learn more about the data the device exchanges with IoT Central.
