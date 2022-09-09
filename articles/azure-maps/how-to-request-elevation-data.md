---
title: Request elevation data using the Azure Maps Elevation service (Preview) 
description: Learn how to request elevation data using the Azure Maps Elevation service (Preview).
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
---

# Request elevation data using the Azure Maps Elevation service (Preview)

> [!IMPORTANT]
> The Azure Maps Elevation service is currently in public preview.
> This preview version is provided without a service level agreement, and it's not recommended for production workloads. Certain features might not be supported or might have constrained capabilities. 
> For more information, see [Supplemental Terms of Use for Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

The Azure Maps [Elevation service](https://docs.microsoft.com/rest/api/maps/elevation) provides APIs to query elevation data anywhere on the Earth's surface. You can request sampled elevation data along paths, within a defined bounding box, or at specific coordinates. Also, you can use the [Render V2 - Get Map Tile API](https://docs.microsoft.com/rest/api/maps/renderv2) to retrieve elevation data in tile format. The tiles are delivered in GeoTIFF raster format. This article shows you how to use Azure Maps Elevation service and the Get Map Tile API to request elevation data. The elevation data can be requested in both GeoJSON and GeoTiff formats.

## Prerequisites

1. [Make an Azure Maps account in the S1 pricing tier](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Obtain a primary subscription key](quick-demo-map-app.md#get-the-primary-key-for-your-account), also known as the primary key or the subscription key.

For more information on authentication in Azure Maps, [Manage Authentication in Azure Maps](how-to-manage-authentication.md).

This article uses the [Postman](https://www.postman.com/) application, but you may choose a different API development environment.

## Request elevation data in raster tiled format

To request elevation data in raster tile format, use the [Render V2 - Get Map Tile API](https://docs.microsoft.com/rest/api/maps/renderv2). If the tile can be found, the API returns the tile as a GeoTIFF. Otherwise, the API returns 0. All raster DEM tiles are using the geoid (sea level) Earth mode. In this example, we'll request elevation data for Mt. Everest.

>[!TIP]
>To retrieve a tile at a specific area on the world map, you'll need to find the correct tile at the appropriate zoom level. Note also that, WorldDEM covers the entire global landmass but does not cover oceans.  For more information, see [Zoom levels and tile grid](zoom-levels-and-tile-grid.md).

1. Open the Postman app. Near the top of the Postman app, select **New**. In the **Create New** window, select **Collection**.  Name the collection and select the **Create** button.

2. To create the request, select **New** again. In the **Create New** window, select **Request**. Enter a **Request name** for the request. Select the collection you created in the previous step, and then select **Save**.

3. Select the **GET** HTTP method in the builder tab and enter the following URL to request the raster tile. For this request, and other requests mentioned in this article, replace `{Azure-Maps-Primary-Subscription-key}` with your primary subscription key.

    ```http
    https://atlas.microsoft.com/map/tile?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=2.0&tilesetId=microsoft.dem&zoom=13&x=6074&y=3432
    ```

4. Click the **Send** button. You should receive the raster tile containing the elevation data in GeoTIFF format. Each pixel within the raster tile raw data is of type `float`. The value of each pixel represents the elevation height in meters.

## Request elevation data in GeoJSON format

Use the Elevation service (Preview) APIs to request elevation data in GeoJSON format. This section will show you each one of the three APIs:

* [Get Data for Points](/rest/api/maps/elevation/getdataforpoints)
* [Post Data for Points](/rest/api/maps/elevation/postdataforpoints)
* [Get Data for Polyline](https://docs.microsoft.com/rest/api/maps/elevation/getdataforpolyline)
* [Post Data for Polyline](https://docs.microsoft.com/rest/api/maps/elevation/postdataforpolyline)
* [Get Data for Bounding Box](https://docs.microsoft.com/rest/api/maps/elevation/getdataforboundingbox)

>[!IMPORTANT]
> When no data can be returned, all APIs return `0`.

### Request elevation data for points

In this example, we'll use the [Get Data for Points API](/rest/api/maps/elevation/getdataforpoints) to request elevation data at Mt. Everest and Chamlang mountains. Then, we'll use the [Post Data for Points API](/rest/api/maps/elevation/postdataforpoints) to request elevation data using the same two points. Latitudes and longitudes in the URL are expected to be in WGS84 (World Geodetic System) decimal degree.

 >[!IMPORTANT]
 >Due to the URL character length limit of 2048, it's not possible to pass more than 100 coordinates as a pipeline delimited string in a URL GET request. If you intend to pass more than 100 coordinates as a pipeline delimited string, use the POST Data For Points.

1. To create the request, select **New** again. In the **Create New** window, select **Request**. Enter a **Request name** for the request. Select the collection you created in the previous step, and then select **Save**.

2. Select the **GET** HTTP method in the builder tab and enter the following URL. For this request, and other requests mentioned in this article, replace `{Azure-Maps-Primary-Subscription-key}` with your primary subscription key.

    ```http
    https://atlas.microsoft.com/elevation/point/json?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0&points=-73.998672,40.714728|150.644,-34.397
    ```

3. Click the **Send** button.  You'll receive the following JSON response:

    ```json
    {
    "data": [
        {
            "coordinate": {
                "latitude": 40.714728,
                "longitude": -73.998672
            },
            "elevationInMeter": 12.142355447638208
        },
        {
            "coordinate": {
                "latitude": -34.397,
                "longitude": 150.644
            },
            "elevationInMeter": 384.47041445517846
        }
    ]
    }
    ```

4. Now, we'll call the [Post Data for Points API](/rest/api/maps/elevation/postdataforpoints) to get elevation data for the same two points. Select the **POST** HTTP method in the builder tab and enter the following URL. For this request, and other requests mentioned in this article, replace `{Azure-Maps-Primary-Subscription-key}` with your primary subscription key.

    ```http
    https://atlas.microsoft.com/elevation/point/json?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0
    ```

5. In the **Headers** of the **POST** request, set `Content-Type` to `application/json`. In the **Body**, provide the coordinate point information below. When you're done, click **Send**.

     ```json
    [
        {
            "lon": -73.998672,
            "lat": 40.714728
        },
        {
            "lon": 150.644,
            "lat": -34.397
        }
    ]
    ```

### Request elevation data samples along a Polyline

In this example, we'll use the [Get Data for Polyline](https://docs.microsoft.com/rest/api/maps/elevation/getdataforpolyline) to request five equally spaced samples of elevation data along a straight line between coordinates at Mt. Everest and Chamlang mountains. Both coordinates must be defined in Long/Lat format. If you don't specify a value for the `samples` parameter, the number of samples defaults to 10. The maximum number of samples is 2,000.

Then, we'll use the Get Data for Polyline to request three equally spaced samples of elevation data along a path. We'll define the precise location for the samples by passing in three Long/Lat coordinate pairs.

Finally, we'll use the [Post Data For Polyline API](https://docs.microsoft.com/rest/api/maps/elevation/postdataforpolyline) to request elevation data at the same three equally spaced samples.

Latitudes and longitudes in the URL are expected to be in WGS84 (World Geodetic System) decimal degree.

 >[!IMPORTANT]
 >Due to the URL character length limit of 2048, it's not possible to pass more than 100 coordinates as a pipeline delimited string in a URL GET request. If you intend to pass more than 100 coordinates as a pipeline delimited string, use the POST Data For Points.

1. Select **New**. In the **Create New** window, select **Request**. Enter a **Request name** and select a collection. Click **Save**.

2. Select the **GET** HTTP method in the builder tab and enter the following URL. For this request, and other requests mentioned in this article, replace `{Azure-Maps-Primary-Subscription-key}` with your primary subscription key.

   ```http
    https://atlas.microsoft.com/elevation/line/json?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}&lines=-73.998672,40.714728|150.644,-34.397&samples=5
    ```

3. Click the **Send** button.  You'll receive the following JSON response:

    ```JSON
    {
        "data": [
            {
                "coordinate": {
                    "latitude": 40.714728,
                    "longitude": -73.998672
                },
                "elevationInMeter": 12.14236
            },
            {
                "coordinate": {
                    "latitude": 21.936796000000001,
                    "longitude": -17.838003999999998
                },
                "elevationInMeter": 0.0
            },
            {
                "coordinate": {
                    "latitude": 3.1588640000000012,
                    "longitude": 38.322664000000003
                },
                "elevationInMeter": 598.66943
            },
            {
                "coordinate": {
                    "latitude": -15.619067999999999,
                    "longitude": 94.483332000000019
                },
                "elevationInMeter": 0.0
            },
            {
                "coordinate": {
                    "latitude": -34.397,
                    "longitude": 150.644
                },
                "elevationInMeter": 384.47041
            }
        ]
    }
    ```

4. Now, we'll request three samples of elevation data along a path between coordinates at Mount Everest, Chamlang, and Jannu mountains. In the **Params** section, copy the following coordinate array for the value of the `lines` query key.

    ```html
        86.9797222, 27.775|86.9252778, 27.9880556 | 88.0444444, 27.6822222
    ```

5. Change the `samples` query key value to `3`.  The image below shows the new values.

     :::image type="content" source="./media/how-to-request-elevation-data/get-elevation-samples.png" alt-text="Retrieve three elevation data samples.":::

6. Click the blue **Send** button. You'll receive the following JSON response:

    ```json
    {
        "data": [
            {
                "coordinate": {
                    "latitude": 27.775,
                    "longitude": 86.9797222
                },
                "elevationInMeter": 7116.0348851572589
            },
            {
                "coordinate": {
                    "latitude": 27.737403546316028,
                    "longitude": 87.411180791156454
                },
                "elevationInMeter": 1798.6945512521534
            },
            {
                "coordinate": {
                    "latitude": 27.682222199999998,
                    "longitude": 88.0444444
                },
                "elevationInMeter": 7016.9372013588072
            }
        ]
    }
    ```

7. Now, we'll call the [Post Data For Polyline API](https://docs.microsoft.com/rest/api/maps/elevation/postdataforpolyline) to get elevation data for the same three points. Select the **POST** HTTP method in the builder tab and enter the following URL. For this request, and other requests mentioned in this article, replace `{Azure-Maps-Primary-Subscription-key}` with your primary subscription key.

    ```http
    https://atlas.microsoft.com/elevation/line/json?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}&samples=5
    ```

8. In the **Headers** of the **POST** request, set `Content-Type` to `application/json`. In the **Body**, provide the coordinate point information below. When you're done, click **Send**.

     ```json
    [
        {
            "lon": 86.9797222,
            "lat": 27.775
        },
        {
            "lon": 86.9252778,
            "lat": 27.9880556
        },
        {
            "lon": 88.0444444,
            "lat": 27.6822222
        }
    ]
    ```

### Request elevation data by Bounding Box

Now we'll use the [Get Data for Bounding Box](https://docs.microsoft.com/rest/api/maps/elevation/getdataforboundingbox) to request elevation data near Mt. Rainier, WA. The elevation data will be returned at equally spaced locations within a bounding box. The bounding area defined by (2) sets of lat/long coordinates (south latitude, west longitude | north latitude, east longitude) is divided into rows and columns. The edges of the bounding box account for two (2) of the rows and two (2) of the columns. Elevations are returned for the grid vertices created at row and column intersections. Up to 2000 elevations can be returned in a single request.

In this example, we'll specify rows=3 and columns=6. 18 elevation values are returned in the response. In the following diagram, the elevation values are ordered starting with the southwest corner, and then continue west to east and south to north.  The elevation points are numbered in the order that they're returned.

:::image type="content" source="./media/how-to-request-elevation-data/bounding-box.png" border="false" alt-text="Bounding box coordinates at NE and SE corners.":::

1. Select **New**. In the **Create New** window, select **Request**. Enter a **Request name** and select a collection. Click **Save**.

2. Select the **GET** HTTP method in the builder tab and enter the following URL. For this request, and other requests mentioned in this article, replace `{Azure-Maps-Primary-Subscription-key}` with your primary subscription key.

    ```http
    https://atlas.microsoft.com/elevation/lattice/json?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0&bounds=-121.66853362143818, 46.84646479863713,-121.65853362143818, 46.85646479863713&rows=2&columns=3
    ```

3. Click the blue **Send** button. 18  elevation data samples, one for each vertex of the grid, are returned in the response.

    ```json
    {
    "data": [
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.66853362143819
            },
            "elevationInMeter": 2298.6581875651746
        },
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.66653362143819
            },
            "elevationInMeter": 2306.3980756609963
        },
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.66453362143818
            },
            "elevationInMeter": 2279.3385479564113
        },
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.66253362143819
            },
            "elevationInMeter": 2233.1549264690366
        },
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.66053362143818
            },
            "elevationInMeter": 2196.4485923541492
        },
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.65853362143818
            },
            "elevationInMeter": 2133.1756767157253
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.66853362143819
            },
            "elevationInMeter": 2345.3227848228803
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.66653362143819
            },
            "elevationInMeter": 2292.2449195443587
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.66453362143818
            },
            "elevationInMeter": 2270.5867788258074
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.66253362143819
            },
            "elevationInMeter": 2296.8311427390604
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.66053362143818
            },
            "elevationInMeter": 2266.0729430891065
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.65853362143818
            },
            "elevationInMeter": 2242.216346631234
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.66853362143819
            },
            "elevationInMeter": 2378.460838833359
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.66653362143819
            },
            "elevationInMeter": 2327.6761137260387
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.66453362143818
            },
            "elevationInMeter": 2208.3782743402949
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.66253362143819
            },
            "elevationInMeter": 2106.9526472760981
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.66053362143818
            },
            "elevationInMeter": 2054.3270174034078
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.65853362143818
            },
            "elevationInMeter": 2030.6438331110671
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.66853362143819
            },
            "elevationInMeter": 2318.753153399402
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.66653362143819
            },
            "elevationInMeter": 2253.88875188271
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.66453362143818
            },
            "elevationInMeter": 2136.6145845357587
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.66253362143819
            },
            "elevationInMeter": 2073.6734467948486
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.66053362143818
            },
            "elevationInMeter": 2042.994055784251
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.65853362143818
            },
            "elevationInMeter": 1988.3631481900356
        }
    ]
    }
    ```

## Samples: Use Elevation service (Preview) APIs in Azure Maps Control

### Get elevation data by coordinate position

The following sample web page shows you how to use the map control to display elevation data at a coordinate point. When the user drags the marker, the map will display the elevation data in a popup.

<br/>

<iframe height="500" style="width:100%;" scrolling="no" title="Get elevation at position" src="https://codepen.io/azuremaps/embed/c840b510e113ba7cb32809591d5f96a2?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/azuremaps/pen/c840b510e113ba7cb32809591d5f96a2'>Get elevation at position</a> by Azure Maps
  (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

### Get elevation data by bounding box

The following sample web page shows you how to use the map control to display elevation data contained within a bounding box. The user defines the bounding box by clicking on the `square` icon in the upper-left hand corner, and drawing the square anywhere on the map. The map control will then render the elevation data in accordance with the colors that are specified in the key that's located in the upper-right hand corner.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Elevations by bounding box" src="https://codepen.io/azuremaps/embed/619c888c70089c3350a3e95d499f3e48?height=500&theme-id=default&default-tab=js,result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/azuremaps/pen/619c888c70089c3350a3e95d499f3e48'>Elevations by bounding box</a> by Azure Maps
  (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

### Get elevation data by PolyLine path

The following sample web page shows you how to use the map control to display elevation data along a path. The user defines the path by clicking on the `PolyLine` icon in the upper-left hand corner, and drawing the PolyLine on the map. The map control then renders the elevation data in colors that are specified in the key located in the upper-right hand corner.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Elevation path gradient" src="https://codepen.io/azuremaps/embed/7bee08e5cb13d05cb0a11636b60f14ca?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/azuremaps/pen/7bee08e5cb13d05cb0a11636b60f14ca'>Elevation path gradient</a> by Azure Maps
  (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>


## Next steps

To further explore the Azure Maps Elevation (Preview) APIs, see:

> [!div class="nextstepaction"]
> [Elevation (Preview) - Get Data for Lat Long Coordinates](/rest/api/maps/elevation/getdataforpoints)

> [!div class="nextstepaction"]
> [Elevation (Preview) - Get Data for Bounding Box](https://docs.microsoft.com/rest/api/maps/elevation/getdataforboundingbox)

> [!div class="nextstepaction"]
> [Elevation (Preview) - Get Data for Polyline](https://docs.microsoft.com/rest/api/maps/elevation/getdataforpolyline)

> [!div class="nextstepaction"]
> [Render V2 – Get Map Tile](https://docs.microsoft.com/rest/api/maps/renderv2)

For a complete list of Azure Maps REST APIs, see:

> [!div class="nextstepaction"]
> [Azure Maps REST APIs](https://docs.microsoft.com/rest/api/maps/)
