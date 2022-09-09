---
title: Use location data in an Azure IoT Central solution
description: Learn how to use location data sent from a device connected to your IoT Central application. Plot location data on a map or create geofencing rules.
author: dominicbetts
ms.author: dobett
ms.date: 01/08/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central

---

# Use location data in an Azure IoT Central solution

This article shows you how to use location data in an IoT Central application. A device connected to IoT Central can send location data as telemetry stream or use a device property to report location data.

You can use the location data to:

* Plot the reported location on a map.
* Plot the telemetry location history om a map.
* Create geofencing rules to notify an operator when a device enters or leaves a specific area.

## Add location capabilities to a device template

The following screenshot shows a device template with examples of a device property and telemetry type that use location data. The definitions use the **location** semantic type and the **geolocation** schema type:

:::image type="content" source="media/howto-use-location-data/location-device-template.png" alt-text="Screenshot showing location property definition in device template":::

For reference, the [Digital Twins Definition Language (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) definitions for these capabilities look like the following snippet:

```json
{
  "@type": [
    "Property",
    "Location"
  ],
  "displayName": {
    "en": "DeviceLocation"
  },
  "name": "DeviceLocation",
  "schema": "geopoint",
  "writable": false
},
{
  "@type": [
    "Telemetry",
    "Location"
  ],
  "displayName": {
    "en": "Tracking"
  },
  "name": "Tracking",
  "schema": "geopoint"
}
```

> [!NOTE]
> The **geopoint** schema type is not part of the [DTDL specification](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md). IoT Central currently supports the **geopoint** schema type and the **location** semantic type for backwards compatibility.

## Send location data from a device

When a device sends data for the **DeviceLocation** property shown in the previous section, the payload looks like the following JSON snippet:

```json
{
  "DeviceLocation": {
    "lat": 47.64263,
    "lon": -122.13035,
    "alt": 0
  }
}
```

When a device sends data for the **Tracking** telemetry shown in the previous section, the payload looks like the following JSON snippet:

```json
{
  "Tracking": {
    "lat": 47.64263,
    "lon": -122.13035,
    "alt": 0
  }
}
```

## Display device location

You can display location data in multiple places in your IoT Central application. For example, on views associated with an individual device or on dashboards.

When you create a view for a device, you can choose to plot the location on a map, or show the individual values:

:::image type="content" source="media/howto-use-location-data/location-views.png" alt-text="Screenshot showing example view with location data":::

You can add map tiles to a dashboard to plot the location of one or more devices. When you add a map tile to show location telemetry, you can plot the location over a time period. The following screenshot shows the location reported by a simulated device over the last 30 minutes:

:::image type="content" source="media/howto-use-location-data/location-dashboard.png" alt-text="Screenshot showing example dashboard with location data":::

## Create a geofencing rule

You can use location telemetry to create a geofencing rule that generates an alert when a device moves into or out of a rectangular area. The following screenshot shows a rule that uses four conditions to define a rectangular area using latitude and longitude values. The rule generates an email when the device moves into the rectangular area:

:::image type="content" source="media/howto-use-location-data/geofence-rule.png" alt-text="Screenshot that shows a geofencing rule definition":::

## Next steps

Now that you've learned how to use properties in your Azure IoT Central application, see:

* [Payloads](concepts-telemetry-properties-commands.md)
* [Create and connect a client application to your Azure IoT Central application](tutorial-connect-device.md)