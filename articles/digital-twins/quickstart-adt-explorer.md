---
# Mandatory fields.
title: Explore a sample scenario
titleSuffix: Azure Digital Twins
description: Use the ADT Explorer sample to visualize and explore a pre-built scenario.
author: baanders
ms.author: baanders # Microsoft employees only
ms.date: 8/12/2020
ms.topic: quickstart
ms.service: digital-twins

# Optional fields. Don't forget to remove # if you need a field.
# ms.custom: can-be-multiple-comma-separated
# ms.reviewer: MSFT-alias-of-reviewer
# manager: MSFT-alias-of-manager-or-PM-counterpart
---

# Explore a sample Azure Digital Twins scenario using ADT Explorer

With Azure Digital Twins, you can create and interact with live models of your real-world environments. This is done by modeling individual elements as **digital twins**, then connecting them into a knowledge **graph** that can respond to live events and be queried for information.

In this quickstart, you will explore a pre-built Azure Digital Twins graph, with the help of a sample application called [**Azure Digital Twins (ADT) Explorer**](https://docs.microsoft.com/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/). ADT Explorer lets you upload a scenario, view visual representations of your twins and graph, and perform other management activities through a browser-based, visual experience.

The quickstart contains the following major steps:

1. Set up an Azure Digital Twins instance and ADT Explorer
1. Upload pre-built models and graph data to construct the sample scenario
1. Explore the scenario graph that is created
1. Make changes to the graph

The sample graph you will be working with represents a building with two floors and two rooms. The graph will look like this:

:::image type="content" source="media/quickstart-adt-explorer/graph-view-full.png" alt-text="View of a graph made of 4 circular nodes connected by arrows. A circle labeled 'Floor1' is connected by a arrow labeled 'contains' to a circle labeled 'Room1'; a circle labeled 'Floor0' is connected by an arrow labeled 'contains' to a circle labeled 'Room0'. 'Floor1' and 'Floor0' are not connected.":::

## Prerequisites

You'll need an Azure subscription to complete this quickstart. If you don't have one already, **[create one for free](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)** now.

Before starting the quickstart, you will also need to download two samples:
* The **ADT Explorer** sample application. This sample contains the main app you use in the quickstart to load and explore an Azure Digital Twins scenario. To get the app, navigate here: [Azure Digital Twins (ADT) explorer](https://docs.microsoft.com/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/). Hit the *Download ZIP* button to download a *.ZIP* file of this sample code to your machine as _**ADT_Explorer.zip**_.
* The **example Azure Digital Twins scenario**. This includes a pre-built Azure Digital Twins graph that you will be loading into ADT Explorer to work with. To get the scenario, navigate here: [Azure Digital Twins samples](https://docs.microsoft.com/samples/azure-samples/digital-twins-samples/digital-twins-samples). Hit the *Download ZIP* button to download a *.ZIP* file of this sample code to your machine as _**Azure_Digital_Twins_samples.zip**_.

## Set up Azure Digital Twins and ADT Explorer

The first step in working with Azure Digital Twins is to set up an **Azure Digital Twins instance**. After you create an instance of the service, you'll be able to populate it with the example data later in the quickstart.

You'll also set up permissions for ADT Explorer to run on your computer and access your Azure Digital Twins instance. This will allow you to use the sample app to explore your instance and its data.

### Set up Azure Digital Twins instance

The simplest way to set up an instance and the required authentication is to run an automated deployment script sample. Follow the instructions in [*How-to: Set up an instance and authentication (scripted)*](how-to-set-up-instance-scripted.md). The instructions also contain steps to verify that you have completed each step successfully and are ready to move on to using your new instance.

In this quickstart, you will need the following values from when you set up your instance. If you need to gather these values again, use the links below to the corresponding sections in the setup article for finding them in the [Azure portal](https://portal.azure.com).
* Azure Digital Twins instance **_host name_** ([find in portal](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values))
* Azure AD app registration **_Application (client) ID_** ([find in portal](how-to-set-up-instance-portal.md#collect-important-values))
* Azure AD app registration **_Directory (tenant) ID_** ([find in portal](how-to-set-up-instance-portal.md#collect-important-values))

### Set ADT Explorer permissions

Next, prepare the Azure Digital Twins instance you created to work with ADT Explorer, which is a locally-hosted web application. Visit the [App registrations](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) page in the Azure portal and select the name of your app registration from the list.

Select *Authentication* from the registration's menu, and hit *+ Add a platform*.

:::image type="content" source="media/quickstart-adt-explorer/authentication-pre.png" alt-text="Azure portal page of the Authentication details for an app registration. There is a highlight around an 'Add a platform' button" lightbox="media/quickstart-adt-explorer/authentication-pre.png":::

In the *Configure platforms* page that follows, select *Web*.
Fill the configuration details as follows:
* **Redirect URIs**: Add a redirect URI of *http://localhost:3000*.
* **Implicit grant**: Check the box for *Access tokens*.

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/authentication-configure-web.png" alt-text="The Configure platforms page, highlighting the info described above onscreen":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Hit *Configure* to finish.

Now you have a web configuration configured that ADT Explorer will use. The Authentication tab in the Azure portal should reflect this.

:::image type="content" source="media/quickstart-adt-explorer/authentication-post.png" alt-text="Azure portal page of the Authentication details for an app registration. There are highlights around a Web platform section with a redirect URI of http://localhost:3000, and Implicit Grant being enabled for access tokens":::

### Run and configure ADT Explorer

Next, run the ADT Explorer application and configure it for your Azure Digital Twins instance.

Navigate to the downloaded _**ADT_Explorer.zip**_ folder and unzip it. 
Open a command prompt at the folder location *ADT_explorer/client/src*.

Run `npm install` to download all the required dependencies.

Then, start the app by running `npm run start`.

After a few seconds, a browser window will open and the app will appear in the browser.

:::image type="content" source="media/quickstart-adt-explorer/explorer-blank.png" alt-text="Browser window showing an app running at localhost:3000. The app is called ADT Explorer and contains boxes for a Query Explorer, Model View, Graph View, and Property Explorer. There is no onscreen data yet." lightbox="media/quickstart-adt-explorer/explorer-blank.png":::

Hit the *Sign in* button at the top of the window to configure ADT Explorer to work with the instance you've set up. 

:::image type="content" source="media/quickstart-adt-explorer/sign-in.png" alt-text="ADT Explorer highlighting the Sign In icon near the top of the window. The icon shows a simple silhouette of a person overlaid with a silhouette of a key." lightbox="media/quickstart-adt-explorer/sign-in.png":::

Enter the important information you gathered earlier in the [Prerequisites](#prerequisites) section:
* Application (client) ID
* Directory (tenant) ID
* Azure Digital Twins instance URL, in the format *https://{instance host name}*

>[!NOTE]
> You can revisit/edit this information at any time by selecting the same icon to pull up the Sign In box again. It will keep the values that you passed in.

> [!TIP]
> If a `SignalRService.subscribe` error message is shown when you connect, make sure that your Azure Digital Twins URL begins with *https://*.

If you see a *Permissions requested* pop-up window from Microsoft, grant consent for this application and accept to continue.

## Add the sample data

Next, you will import the sample scenario and graph into ADT Explorer.

The sample scenario is located in your downloaded  _**Azure_Digital_Twins_samples.zip**_ folder, so you should navigate to and unzip the folder now.

### Models

The first step in an Azure Digital Twins solution is defining the vocabulary for your environment. This is done by creating custom [**models**](concepts-models.md), which describe the types of entity that exist in your environment. 

Each model is written in a JSON-LD-like language called **Digital Twin Definition Language (DTDL)**, and describes a single type of entity in terms of its *properties*, *telemetry*, *relationships*, and *components*. 
Later, you will use these models as the basis for digital twins that represent specific instances of these types.

Typically when creating a model, you will complete three steps:
1. Write the model definition (in the quickstart, already done as part of the sample solution)
2. Validate it to make sure the syntax is accurate (in the quickstart, already done as part of the sample solution)
3. Upload it to your Azure Digital Twins instance
 
For this quickstart, the model files have already been written and validated for you, and are included with the solution you downloaded. In this section, you will upload two pre-written models to your instance to define these components of a building environment:
* Floor
* Room

#### Upload models

In the *MODEL VIEW* box, hit the *Upload a Model* icon.

:::image type="content" source="media/quickstart-adt-explorer/upload-model.png" alt-text="In the Model View box, the middle icon is highlighted. It shows an arrow pointing into a cloud." lightbox="media/quickstart-adt-explorer/upload-model.png":::
 
1. In the file selector box that appears, navigate to the *Azure_Digital_Twins_samples/AdtSampleApp/SampleClientApp/models* folder in the downloaded repository.
2. Select *Room.json* and *Floor.json*, and hit OK. (You can upload the other models if you'd like, but they won't be used in this quickstart.)
3. Follow the popup dialog asking you to sign into your Azure account.

>[!NOTE]
>If you see the following error message:
> :::image type="content" source="media/quickstart-adt-explorer/error-models-popup.png" alt-text="A popup reading 'Error: Error fetching models: ClientAuthError: Error opening popup window. This can happen if you are using IE or if popups are blocked in the browser.' with a Close button at the bottom" border="false"::: 
> Try disabling your popup blocker or using a different browser.

ADT Explorer will now upload these model files to your Azure Digital Twins instance. They should show up in the *MODEL VIEW* box, displaying their friendly names and full model IDs. You can click the *View Model* information bubbles to see the DTDL code behind them.

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/model-info.png" alt-text="A view of the 'Model View' box with two model definitions listed inside, Floor (dtmi:example:Floor;1) and Room (dtmi:example:Room;1). The 'View model' icon showing a letter 'i' in a circle is highlighted for each model." lightbox="media/quickstart-adt-explorer/model-info.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

### Twins and the twin graph

Now that some models have been uploaded to your Azure Digital Twins instance, you can add [**digital twins**](concepts-twins-graph.md) that follow the model definitions. 

Digital twins represent the actual entities within your business environment: things like sensors on a farm, lights in a car, or—in this quickstart—rooms on a building floor. You can create many twins of any given model type (like multiple rooms that all use the *Room* model), and connect them with relationships into a **twin graph** that represents the full environment.

In this section, you will upload pre-created twins that are connected into a pre-created graph. The graph contains two floors and two rooms, connected in the following layout:
* *Floor0*
    - contains *Room0*
* *Floor1*
    - contains *Room1*

#### Import the graph

In the *GRAPH VIEW* box, hit the *Import Graph* icon.

:::image type="content" source="media/quickstart-adt-explorer/import-graph.png" alt-text="In the Graph View box, an icon is highlighted. It shows an arrow pointing into a cloud." lightbox="media/quickstart-adt-explorer/import-graph.png":::

In the file selector box, navigate to the *Azure_Digital_Twins_samples/AdtSampleApp/SampleClientApp* folder and choose the _**buildingScenario.xlsx**_ spreadsheet file. This file contains a description of the sample graph. Hit OK.

After a few seconds, ADT Explorer will open an *Import* view displaying a preview of the graph that is going to be loaded.

To confirm the graph upload, hit the *Save* icon in the upper right corner of the *GRAPH VIEW*:

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/graph-preview-save.png" alt-text="Highlighting the Save icon in the Graph Preview pane" lightbox="media/quickstart-adt-explorer/graph-preview-save.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

ADT Explorer will now use the uploaded file to create the requested twins and relationships between them. A dialog will appear to show that it is finished. Hit *Close*.

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/import-success.png" alt-text="Dialog box indicating graph import success. It reads 'Import successful. 49 twins imported. 50 relationships imported.'" lightbox="media/quickstart-adt-explorer/import-success.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

The graph has now been uploaded to ADT Explorer. To see see the graph, hit the *Run Query* button in the *GRAPH EXPLORER* box, near the top of the ADT Explorer window. 

:::image type="content" source="media/quickstart-adt-explorer/run-query.png" alt-text="A button reading 'Run Query' near the top of the window is highlighted" lightbox="media/quickstart-adt-explorer/run-query.png":::

This will run the default query to select and display all digital twins. ADT Explorer will retrieve all twins and relationships from the service, and draw the graph defined by them in the *GRAPH VIEW* box.

## Explore the graph

Now, you can see the uploaded graph of the sample scenario:

:::image type="content" source="media/quickstart-adt-explorer/graph-view-full.png" alt-text="View of the 'Graph View' box with a twin graph inside. A circle labeled 'floor1' is connected by a arrow labeled 'contains' to a circle labeled 'room1'; a circle labeled 'floor0' is connected by an arrow labeled 'contains' to a circle labeled 'room0'.":::

The circles (graph "nodes") represent digital twins, and the lines represent relationships. You will see that the *Floor0* twin contains *Room0*, and the *Floor1* twin contains *Room1*.

If you're using a mouse, you can click and drag pieces of the graph to move them around.

### View twin properties 

You can select a twin to see a list of its properties and their values in the *PROPERTY EXPLORER* box. 

Here are the properties of *Room0*:

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/properties-room0.png" alt-text="Highlight around the 'Property Explorer' box showing properties for Room0, including (among others) a $dtId field of 'Room0', a Temperature field of 70, and a Humidity field of 30." lightbox="media/quickstart-adt-explorer/properties-room0.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Note that *Room0* has a temperature of **70**.

Here are the properties of *Room1*:

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/properties-room1.png" alt-text="Highlight around the 'Property Explorer' box showing properties for Room1, including (among others) a $dtId field of 'Room1', a Temperature field of 80, and a Humidity field of 60." lightbox="media/quickstart-adt-explorer/properties-room1.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Note that *Room1* has a temperature of **80**.

### Query the graph

A main feature of Azure Digital Twins is the ability to [query](concepts-query-language.md) your twin graph easily and efficiently to answer questions about your environment. 

One way to query the twins in your graph is by their *properties*. Querying based on properties can help answer a variety of questions, including finding outliers in your environment that might need attention.

In this section, you will run a query to answer the following question: _**What are all the twins in my environment with a temperature above 75?**_

To see the answer, run the following query in the *QUERY EXPLORER* box:

```SQL
SELECT * FROM DigitalTwins T WHERE T.Temperature > 75
```

Recall from viewing the twin properties earlier that *Room0* has a temperature of **70** and *Room1* has a temperature of **80**. As a result, only _**Room1**_ shows up in the results here.
    
:::image type="content" source="media/quickstart-adt-explorer/result-query-property-before.png" alt-text="Results of property query, showing only Room1" lightbox="media/quickstart-adt-explorer/result-query-property-before.png":::

>[!TIP]
> Other comparison operators (*<*,*>*, *=*, or *!=*) are also supported within the query above. You can try plugging these, different values, or different twin properties into the query to try out answering your own questions.

## Edit data in the graph

You can use ADT Explorer to edit the properties of the twins represented in your graph. In this section, we will **_raise the temperature of_ Room0 to 76**.

To do this, select *Room0*, bringing up its property list in the *PROPERTY EXPLORER* box.

The properties in this list are editable. Select the temperature value of **70** to enable entering a new value. Enter **76**, and hit the *Save* icon to update the temperature to **76**.

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/new-properties-room0.png" alt-text="The 'Property Explorer' box showing properties for Room0. The temperature value is an editable box showing 76, and there is a highlight around the Save icon." lightbox="media/quickstart-adt-explorer/new-properties-room0.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Upon successful save, you will see a *Patch Information* window displaying the patch code that was used behind the scenes with the Azure Digital Twins [APIs](how-to-use-apis-sdks.md) to make the update. Hit *Close*.

### Query to see the result

To verify that the graph successfully registered your update to *Room0*'s temperature, re-run the query from earlier to **get all the twins in the environment with a temperature above 75**:

```SQL
SELECT * FROM DigitalTwins T WHERE T.Temperature > 75
```

Now that the temperature of *Room0* has been changed from **70** to **76**, both twins should show up in the result.

:::image type="content" source="media/quickstart-adt-explorer/result-query-property-after.png" alt-text="Results of property query, showing both Room0 and Room1" lightbox="media/quickstart-adt-explorer/result-query-property-after.png":::

## Review and contextualize learnings

In this quickstart, you created an Azure Digital Twins instance, connected it to ADT Explorer, and populated it with a sample scenario. 

You then explored the graph, by...
1. Using a query to answer a question about the scenario.
2. Editing a property on a digital twin.
    * Running the query again to see how the answer changed as a result of your update.

The intent of this exercise is to demonstrate how you can use the Azure Digital Twins graph to answer questions about your environment, even as the environment continues to change. 

Although in this quickstart, you made the temperature update manually, it is common in Azure Digital Twins to connect digital twins to real IoT devices so that they receive updates automatically, based on telemetry data. This allows you to build a live graph that always reflects the real state of your environment, and use queries to get information about what's happening in your environment in real time.

## Clean up resources

To wrap up the work for this quickstart, first end the running console app. This will shut off the connection to the ADT Explorer app in the browser, and you will no longer be able to view live data in the browser. You can close the browser tab.

If you plan to continue to the Azure Digital Twins tutorials, the instance used in this quickstart can be reused for those articles, and you don't need to remove it.
 
[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

Finally, delete the project sample folders you downloaded to your local machine (_**ADT_Explorer.zip**_ and _**Azure_Digital_Twins_samples.zip**_).

## Next steps 

Next, continue on to the Azure Digital Twins tutorials to build out your own Azure Digital Twins scenario and interaction tools.

> [!div class="nextstepaction"]
> [*Tutorial: Code a client app*](tutorial-code.md)
