---
title: Overview for Microsoft Azure Maps
description: Learn about services and capabilities in Microsoft Azure Maps and how to use them in your applications.
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: overview
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc, references_regions
#Customer intent: As an Azure enterprise customer, I want to know what capabilities Azure Maps has, so that I can take advantage of mapping in my applications. 
---

# What is Azure Maps?

Azure Maps is a collection of geospatial services and SDKs that use fresh mapping data to provide geographic context to web and mobile applications. Azure Maps provides:

* REST APIs to render vector and raster maps in multiple styles and satellite imagery.
* Creator services (Preview) to create and render maps based on private indoor map data.
* Search services to locate addresses, places, and points of interest around the world.
* Various routing options; such as point-to-point, multipoint, multipoint optimization, isochrone, electric vehicle, commercial vehicle, traffic influenced, and matrix routing.
* Traffic flow view and incidents view, for applications that require real-time traffic information.
* Mobility services (Preview) to request public transit information, plan routes by blending different travel modes and real-time arrivals.
* Time zone and Geolocation (Preview) services.
* Elevation services (Preview) with Digital Elevation Model
* Geofencing service and mapping data storage, with location information hosted in Azure.
* Location intelligence through geospatial analytics.

Additionally, Azure Maps services are available through the Web SDK and the Android SDK. These tools help developers quickly develop and scale solutions that integrate location information into Azure solutions.

You can sign up for a free [Azure Maps account](https://azure.microsoft.com/services/azure-maps/) and start developing.

The following video explains Azure Maps in depth:

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-Maps/player?format=ny" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## Map controls

### Web SDK

The Azure Maps Web SDK lets you customize interactive maps with your own content and imagery. You can use this interactive map for both your web or mobile applications. The map control makes use of WebGL, so you can render large data sets with high performance. You can develop with the SDK by using JavaScript or TypeScript.

:::image type="content" source="./media/about-azure-maps/intro_web_map_control.png" alt-text="Example map of population change created by using Azure Maps Web SDK":::

### Android SDK

Use the Azure Maps Android SDK to create mobile mapping applications.

:::image type="content" source="./media/about-azure-maps/android_sdk.png" border="false" alt-text="Map examples on a mobile device":::

## Services in Azure Maps

Azure Maps consists of the following services that can provide geographic context to your Azure applications.

### Data service (Preview)

Data is imperative for maps. Use the Data service to upload and store geospatial data for use with spatial operations or image composition.  Bringing customer data closer to the Azure Maps service will reduce latency, increase productivity, and create new scenarios in your applications. For details on this service, see the [Data service documentation](/rest/api/maps/data).

### Geolocation service (Preview)

Use the Geolocation service to preview the retrieved two-letter country/region code for an IP address. This service can help you enhance user experience by providing customized application content based on geographic location.

For more details, read the [Geolocation service documentation](/rest/api/maps/geolocation).

### Mobility services (Preview) 

The Azure Maps Mobility services improve the development time for applications with public transit features, such as transit routing and search for nearby public transit stops. Users can retrieve detailed information about transit stops, lines, and schedules. The Mobility service also allows users to retrieve stop and line geometries, alerts for stops, lines, and service areas, and real-time public transit arrivals and service alerts. Additionally, the Mobility services provide routing capabilities with multimodal trip planning options. Multimodal trip planning incorporates walking, bicycling, and public transit options, all into one trip. Users can also access detailed multimodal step-by-step itineraries.

To learn more about the service, see the [Mobility services documentation](/rest/api/maps/mobility).

### Render service

The [Render service V2 (Preview)](/rest/api/maps/renderv2) introduces a new version of the [Get Map Tile V2 API](/rest/api/maps/renderv2/getmaptilepreview). The Get Map Tile V2 API now allows customers to request Azure Maps road tiles, weather tiles, or the map tiles created using Azure Maps Creator. It's recommended that you use the new Get Map Tile V2 API.  

:::image type="content" source="./media/about-azure-maps/intro_map.png" border="false" alt-text="Example of a map from the Render service V2":::

For more details, read the [Render service V2 documentation](/rest/api/maps/renderv2).

To learn more about the Render service V1 that is in GA (General Availability), see the [Render service V1 documentation](/rest/api/maps/render).  

### Route service

The route services can be used to calculate the estimated arrival times (ETAs) for each requested route. Route APIs consider factors, such as real-time traffic information and historic traffic data, like the typical road speeds on the requested day of the week and time of day. The APIs return the shortest or fastest routes available to multiple destinations at a time in sequence or in optimized order, based on time or distance. The service allows developers to calculate directions across several travel modes, such as car, truck, bicycle, or walking, and electric vehicle. The service also considers inputs, such as departure time, weight restrictions, or hazardous material transport.

:::image type="content" source="./media/about-azure-maps/intro_route.png" border="false" alt-text="Example of a map from the Route service":::

The Route service offers advanced set features, such as:

* Batch processing of multiple route requests.
* Matrices of travel time and distance between a set of origins and destinations.
* Finding routes or distances that users can travel based on time or fuel requirements.

For details on the routing capabilities, read the [Route service documentation](/rest/api/maps/route).

### Search service

The Search service helps developers search for addresses, places, business listings by name or category, and other geographic information. Also, services can [reverse geocode](https://en.wikipedia.org/wiki/Reverse_geocoding) addresses and cross streets based on latitudes and longitudes.

:::image type="content" source="./media/about-azure-maps/intro_search.png" border="false" alt-text="Example of a search on a map":::

The Search service also provides advanced features such as:

* Search along a route.
* Search inside a wider area.
* Batch a group of search requests.
* Search electric vehicle charging stations and Point of Interest (POI) data by brand name.

For more details on search capabilities, read the [Search service documentation](/rest/api/maps/search).

### Spatial service

The Spatial service quickly analyzes location information to help inform customers of ongoing events happening in time and space. It enables near real-time analysis and predictive modeling of events.

The service enables customers to enhance their location intelligence with a library of common geospatial mathematical calculations. Common calculations include closest point, great circle distance, and buffers. To learn more about the service and the various features, read the [Spatial service documentation](/rest/api/maps/spatial).

### Timezone service

The Time zone service enables you to query current, historical, and future time zone information. You can use either latitude and longitude pairs or an [IANA ID](https://www.iana.org/) as an input. The Time zone service also allows for:

* Converting Microsoft Windows time-zone IDs to IANA time zones.
* Fetching a time-zone offset to UTC.
* Getting the current time in a chosen time zone.

A typical JSON response for a query to the Time zone service looks like the following sample:

```JSON
{
  "Version": "2020a",
  "ReferenceUtcTimestamp": "2020-07-31T19:15:14.4570053Z",
  "TimeZones": [
    {
      "Id": "America/Los_Angeles",
      "Names": {
        "ISO6391LanguageCode": "en",
        "Generic": "Pacific Time",
        "Standard": "Pacific Standard Time",
        "Daylight": "Pacific Daylight Time"
      },
      "ReferenceTime": {
        "Tag": "PDT",
        "StandardOffset": "-08:00:00",
        "DaylightSavings": "01:00:00",
        "WallTime": "2020-07-31T12:15:14.4570053-07:00",
        "PosixTzValidYear": 2020,
        "PosixTz": "PST+8PDT,M3.2.0,M11.1.0"
      }
    }
  ]
}
```

For details on this service, read the [Time zone service documentation](/rest/api/maps/timezone).

### Traffic service

The Traffic service is a suite of web services that developers can use for web or mobile applications that require traffic information. The service provides two data types:

* Traffic flow: Real-time observed speeds and travel times for all key roads in the network.
* Traffic incidents: An up-to-date view of traffic jams and incidents around the road network.

![Example of a map with traffic information](media/about-azure-maps/intro_traffic.png)

For more information, see the [Traffic service documentation](/rest/api/maps/traffic).

### Weather services (Preview) 

Weather services offer APIs that developers can use to retrieve weather information for a particular location. The information contains details such as observation date and time, brief description of the weather conditions, weather icon, precipitation indicator flags, temperature, and wind speed information. Additional details such as RealFeel™ Temperature and UV index are also returned.

Developers can use the [Get Weather along route API](/rest/api/maps/weather/getweatheralongroutepreview) to retrieve weather information along a particular route. Also, the service supports the generation of weather notifications for waypoints that are affected by weather hazards, such as flooding or heavy rain.

The [Get Map Tile V2 API](/rest/api/maps/renderv2/getmaptilepreview) allows you to request past, current, and future radar and satellite tiles.

![Example of map with real-time weather radar tiles](media/about-azure-maps/intro_weather.png)

### Maps Creator service (Preview) 

Maps Creator service is a suite of web services that developers can use to create applications with map features based on indoor map data.

Maps Creator provides three core services:

* [Dataset service](/rest/api/maps/dataset). Use the Dataset service to create a dataset from a converted Drawing package data. For information on Drawing package requirements, see Drawing package requirements.

* [Conversion service](/rest/api/maps/dataset). Use the Conversion service to convert a DWG design file into Drawing package data for indoor maps.

* [Tileset service](/rest/api/maps/tileset). Use the Tileset service to create a vector-based representation of a dataset. Applications can use a tileset to present a visual tile-based view of the dataset.

* [Feature State service](/rest/api/maps/featurestate). Use the Feature State service to support dynamic map styling. Dynamic map styling allows applications to reflect real-time events on spaces provided by IoT systems.

* [WFS service](/rest/api/maps/featurestate). Use the WFS service to query your indoor map data. The WFS service follows the [Open Geospatial Consortium API](http://docs.opengeospatial.org/is/17-069r3/17-069r3.html) standards for querying a single dataset.

### Elevation service (Preview)

The Azure Maps Elevation service is a web service that developers can use to retrieve elevation data from anywhere on the Earth’s surface.

The Elevation service allows you to retrieve elevation data in two formats:

* **GeoTIFF raster format**. Use the [Render V2 - Get Map Tile API](/rest/api/maps/renderv2) to retrieve elevation data in tile format.

* **GeoJSON format**. Use the [Elevation APIs](/rest/api/maps/elevation) to request sampled elevation data along paths, within a defined bounding box, or at specific coordinates. 

:::image type="content" source="./media/about-azure-maps/elevation.png" alt-text="Example of map with elevation data":::


## Programming model

Azure Maps is built for mobility and can help you develop cross-platform applications. It uses a programming model that's language agnostic and supports JSON output through [REST APIs](/rest/api/maps/).

Also, Azure Maps offers a convenient [JavaScript map control](/javascript/api/azure-maps-control) with a simple programming model. The development is quick and easy for both web and mobile applications.





## Power BI visual

The Azure Maps visual for Power BI provides a rich set of data visualizations for spatial data on top of a map. It's estimated that over 80% of business data has a location context. The Azure Maps visual offers a no-code solution for gaining insights into how this location context relates to and influences your business data.

:::image type="content" source="./media/about-azure-maps/intro-power-bi.png" border="false" alt-text="Power BI desktop with the Azure Maps visual displaying business data":::

For more information, see the Getting started with the [Azure Maps Power BI visual](power-bi-visual-getting-started.md) documentation.

## Usage

To access Azure Maps services, go to the [Azure portal](https://portal.azure.com) and create an Azure Maps account.

Azure Maps uses a key-based authentication scheme. When you create your account, two keys are generated. To authenticate for Azure Maps services, you can use either key.

Note - Azure Maps shares customer-provided address/location queries ("Queries") with third-party TomTom for mapping functionality purposes. Queries aren't linked to any customer or end user when shared with TomTom and can't be used to identify individuals. The Mobility and Weather services, which include integration with Moovit, and AccuWeather are currently in [PREVIEW](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Microsoft is currently in the process of adding TomTom, Moovit, and AccuWeather to the Online Services Subcontractor List.

## Supported regions

Azure Maps services are currently available except in the following countries/regions:

* China
* South Korea

Verify that the location of your current IP address is in a supported country/region.

## Next steps

Try a sample app that showcases Azure Maps:

[Quickstart: Create a web app](quick-demo-map-app.md)

Stay up to date on Azure Maps:

[Azure Maps blog](https://azure.microsoft.com/blog/topics/azure-maps/)