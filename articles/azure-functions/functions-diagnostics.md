---
title: Azure Functions diagnostics Overview
description: Learn how you can troubleshoot issues with your function app with Azure Functions diagnostics.
author: yunjchoi

ms.topic: article
ms.date: 11/01/2019
ms.author: yunjchoi
ms.custom: na
# Customer intent: As a developer, I want help diagnosing my function apps so I can more quickly get them back up and running when problems occur.
---
# Azure Functions diagnostics overview

When you’re running a function app, you want to be prepared for any issues that may arise, from 4xx errors to trigger failures. Azure Functions diagnostics is an intelligent and interactive experience to help you troubleshoot your function app with no configuration or extra cost. When you do run into issues with your function app, Azure Functions diagnostics points out what’s wrong to guide you to the right information to more easily and quickly troubleshoot and resolve the issue. This article shows you the basics of how to use Azure Functions diagnostics to more quickly diagnose and solve common function app issues.

## Start Azure Functions diagnostics

To access Azure Functions diagnostics:

1. Navigate to your function app in the [Azure portal](https://portal.azure.com).
2. Select the **Platform features** tab.
3. Select **Diagnose and solve problems** under **Resource Management**, which opens Azure Functions diagnostics.
4. Choose a category that best describes the issue of your function app by using the keywords in the homepage tile. You can also type a keyword that best describes your issue in the search bar. For example, you could type `execution` to see a list of diagnostic reports related to your function app execution and open them directly from the homepage.

![Homepage](./media/functions-diagnostics/homepage.png)

## Use the Interactive interface

Once you select a homepage category that best aligns with your function app's problem, Azure Functions diagnostics' interactive interface, Genie, can guide you through diagnosing and solving problem of your app. You can use the tile shortcuts provided by Genie to view the full diagnostic report of the problem category that you are interested in. The tile shortcuts provide you a direct way of accessing your diagnostic metrics.

![Genie](./media/functions-diagnostics/genie.png)

After selecting a tile, you can see a list of topics related to the issue described in the tile. These topics provide snippets of notable information from the full report. You can select any of these topics to investigate the issues further. Also, you can select **View Full Report** to explore all the topics on a single page.

![Preview of diagnostic report](./media/functions-diagnostics/preview-of-diagnostic-report.png)

## View a diagnostic report

After you choose a topic, you can view a diagnostic report specific to your function app. Diagnostic reports use status icons to indicate if there are any specific issues with your app. You see detailed description of the issue, recommended actions, related-metrics, and helpful docs. Customized diagnostic reports are generated from a series of checks run on your function app. Diagnostic reports can be a useful tool for pinpointing problems in your function app and guiding you towards resolving the issue.

## Find the problem code

For script-based functions, you can use **Function Execution and Errors** under **Function App Down or Reporting Errors** to narrow down on the line of code causing exceptions or errors. This feature can be a useful tool for getting to the root cause and fixing issues from a specific line of code. This option isn't available for precompiled C# and Java functions.

![Diagnostic report on function execution errors](./media/functions-diagnostics/diagnostic-report-on-function-execution-errors.png)

![Function exception](./media/functions-diagnostics/function-exception.png)

## Next steps

You can ask questions or provide feedback on Azure Functions diagnostics at [UserVoice](https://feedback.azure.com/forums/355860-azure-functions). Please include `[Diag]` in the title of your feedback.

> [!div class="nextstepaction"]
> [Monitor your function apps](functions-monitoring.md)
