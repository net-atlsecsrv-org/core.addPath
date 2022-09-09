---
title: Add a symbol layer to Android maps | Microsoft Azure Maps
description: Learn how to add a marker to a map. See an example that uses the Azure Maps Android SDK to add a symbol layer that contains point-based data from a data source.
author: rbrundritt
ms.author: richbrun
ms.date: 12/08/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
---

# Add a symbol layer (Android SDK)

This article shows you how to render point data from a data source as a symbol layer on a map using the Azure Maps Android SDK. Symbol layers render points as an image and text on the map.

> [!TIP]
> Symbol layers by default will render the coordinates of all geometries in a data source. To limit the layer so that it only renders point geometry features, set the `filter` option of the layer to `eq(geometryType(), "Point")`. If you want to include MultiPoint features as well, set the `filter` option of the layer to `any(eq(geometryType(), "Point"), eq(geometryType(), "MultiPoint"))`.

## Prerequisites

Be sure to complete the steps in the [Quickstart: Create an Android app](quick-android-map.md) document. Code blocks in this article can be inserted into the maps `onReady` event handler.

## Add a symbol layer

Before you can add a symbol layer to the map, you need to take a couple of steps. First, create a data source, and add it to the map. Create a symbol layer. Then, pass in the data source to the symbol layer, to retrieve the data from the data source. Finally, add data into the data source, so that there's something to be rendered.

The code below demonstrates what should be added to the map after it has loaded. This sample renders a single point on the map using a symbol layer.

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a point and add it to the data source.
source.add(Point.fromLngLat(0, 0));

//Create a symbol layer to render icons and/or text at points on the map.
SymbolLayer layer = new SymbolLayer(source);

//Add the layer to the map.
map.layers.add(layer);
```

There are three different types of point data that can be added to the map:

- GeoJSON Point geometry - This object only contains a coordinate of a point and nothing else. The `Point.fromLngLat` static method can be used to easily create these objects.
- GeoJSON MultiPoint geometry - This object contains the coordinates of multiple points and nothing else. Pass an array of points into the `MultiPoint` class to create these objects.
- GeoJSON Feature - This object consists of any GeoJSON geometry and a set of properties that contain metadata associated to the geometry.

For more information, see the [Create a data source](create-data-source-android-sdk.md) document on creating and adding data to the map.

The following code sample creates a GeoJSON Point geometry and passes it into the GeoJSON Feature and has a `title` value added to its properties. The `title` property is displayed as text above the symbol icon on the map.

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a point feature.
Feature feature = Feature.fromGeometry(Point.fromLngLat(0, 0));

//Add a property to the feature.
feature.addStringProperty("title", "Hello World!");

//Add the feature to the data source.
source.add(feature);

//Create a symbol layer to render icons and/or text at points on the map.
SymbolLayer layer = new SymbolLayer(source, 
    //Get the title property of the feature and display it on the map.
    textField(get("title"))
);

//Add the layer to the map.
map.layers.add(layer);
```

The following screenshot shows the above code rending a point feature using an icon and text label with a symbol layer.

![Map with point rendered using a symbol layer displaying an icon and text label for a point feature](media/how-to-add-symbol-to-android-map/android-map-pin.png)

> [!TIP]
> By default, symbol layers optimize the rendering of symbols by hiding symbols that overlap. As you zoom in, the hidden symbols become visible. To disable this feature and render all symbols at all times, set the `iconAllowOverlap` and `textAllowOverlap` options to `true`.

## Add a custom icon to a symbol layer

Symbol layers are rendered using WebGL. As such all resources, such as icon images, must be loaded into the WebGL context. This sample shows how to add a custom icon to the map resources. This icon is then used to render point data with a custom symbol on the map. The `textField` property of the symbol layer requires an expression to be specified. In this case, we want to render the temperature property. Since temperature is a number, it needs to be converted to a string. Additionally we want to append "°F" to it. An expression can be used to do this concatenation; `concat(Expression.toString(get("temperature")), literal("°F"))`.

```java
//Load a custom icon image into the image sprite of the map.
map.images.add("my-custom-icon", R.drawable.showers);

//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a point feature.
Feature feature = Feature.fromGeometry(Point.fromLngLat(-73.985708, 40.75773));

//Add a property to the feature.
feature.addNumberProperty("temperature", 64);

//Add the feature to the data source.
source.add(feature);

//Create a symbol layer to render icons and/or text at points on the map.
SymbolLayer layer = new SymbolLayer(source,
    iconImage("my-custom-icon"),
    iconSize(0.5f),

    //Get the title property of the feature and display it on the map.
    textField(concat(Expression.toString(get("temperature")), literal("°F"))),
    textOffset(new Float[]{0f, -1.5f})
);
```

For this sample, the following image was loaded into the drawable folder of the app.

| ![Weather icon image of rain showers](media/how-to-add-symbol-to-android-map/showers.png)|
|:-----------------------------------------------------------------------:|
| showers.png                                                  |

The following screenshot shows the above code rending a point feature using a custom icon and formatted text label with a symbol layer.

![Map with point rendered using a symbol layer displaying a custom icon and formatted text label for a point feature](media/how-to-add-symbol-to-android-map/android-custom-symbol-layer.png)

> [!TIP]
> When you want to render only text with a symbol layer, you can hide the icon by setting the `iconImage` property of the icon options to `"none"`.

## Next steps

See the following articles for more code samples to add to your maps:

> [!div class="nextstepaction"]
> [Create a data source](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [Add a bubble layer](map-add-bubble-layer-android.md)

> [!div class="nextstepaction"]
> [Use data-driven style expressions](data-driven-style-expressions-android-sdk.md)

> [!div class="nextstepaction"]
> [Display feature information](display-feature-information-android.md)
