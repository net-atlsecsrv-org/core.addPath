---
title: Real User Measurements with web pages - Azure Traffic Manager
description: In this article, learn how to set up your web pages to send Real User Measurements to Azure Traffic Manager.
services: traffic-manager
documentationcenter: traffic-manager
author: duongau
manager: kumud
ms.service: traffic-manager
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 04/06/2021
ms.author: duau 
---

# How to send Real User Measurements to Azure Traffic Manager using web pages

You can configure your web pages to send Real User Measurements to Traffic Manager by obtaining a Real User Measurements (RUM) key and embedding the generated code to web page.

## Obtain a Real User Measurements key

The measurements you take and send to Traffic Manager from your client application gets identified by the service using a unique string, called the **Real User Measurements (RUM) Key**. You can get a RUM key by using the Azure portal, a REST API, PowerShell, or Azure CLI.

To obtain the RUM Key using Azure portal:
1. From a browser, sign in to the Azure portal. If you don’t already have an account, you can sign up for a free one-month trial.

1. In the portal’s search bar, search for the Traffic Manager profile name that you want to modify, and then select the Traffic Manager profile in the results that the displayed.

1. In the Traffic Manager profile page, select **Real User Measurements** under **Settings**.

1. Select **Generate Key** to create a new RUM Key.

    :::image type="content" source="./media/traffic-manager-create-rum-web-pages/generate-rum-key.png" alt-text="Screenshot of how to generate key."::: 

   **Figure 1: Real User Measurements Key Generation**

1. The page now displays the RUM Key generated and a JavaScript code snippet that needs to be embedded into your HTML page.

    :::image type="content" source="./media/traffic-manager-create-rum-web-pages/rum-javascript-code.png" alt-text="Screenshot of generated JavaScript code."::: 

    **Figure 2: Real User Measurements Key and Measurement JavaScript**
 
1. Select the **Copy** button to copy the JavaScript code. 

> [!IMPORTANT]
> Use the generated JavaScript for Real User Measurements feature to function properly. Any changes to this script or the scripts used by Real User Measurements can lead to unpredictable behavior.

## Embed the code to an HTML web page

After you've obtained the RUM key, the next step is to embed this copied JavaScript into an HTML page that your end users visit. Editing HTML can be done in many ways and using different tools and workflows. This example shows how to update an HTML page to add this script. You can use this guidance to adapt it to your HTML source management workflow.

1. Open the HTML page in a text editor.

1. Paste the JavaScript code you copied in the last section into the BODY section of the HTML. The copied code is on line 8 & 9, see figure 3.

    :::image type="content" source="./media/traffic-manager-create-rum-web-pages/real-user-measurement-embed-script.png" alt-text="Screenshot of generated JavaScript embedded into webpage."::: 

    **Figure 3: Simple HTML with embedded Real User Measurements JavaScript**

1. Save the HTML file and host it on a webserver connected to the internet.

1. Next time this page will render on a web browser, the JavaScript referenced gets downloaded, and the script will execute the measurement and reporting operations.

## Next steps
- Learn more about [Real User Measurements](traffic-manager-rum-overview.md)
- Learn [how Traffic Manager works](traffic-manager-overview.md)
- Learn more about the [traffic-routing methods](traffic-manager-routing-methods.md) supported by Traffic Manager
- Learn how to [create a Traffic Manager profile](./quickstart-create-traffic-manager-profile.md)
