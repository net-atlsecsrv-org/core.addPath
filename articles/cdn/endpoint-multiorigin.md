---
title: 'Azure CDN endpoint multi-origin (Preview)' 
description: Get started with Azure CDN endpoint multiple origins.
services: cdn
author: asudbring
manager: KumudD
ms.service: azure-cdn
ms.topic: how-to
ms.date: 9/06/2020
ms.author: allensu
---

# Azure CDN endpoint multi-origin (Preview)

Multi-origin support eliminates downtime and establishes global redundancy. 

By choosing multiple origins within an Azure CDN endpoint, the redundancy provided spreads the risk by probing the health of each origin and failing over if necessary.

Setup one or more origin groups and choose a default origin group. Each origin group is a collection of one or more origins that can take similar workloads.

> [!NOTE]
> Currently this feature is only available from Azure CDN from Microsoft. 

> [!IMPORTANT]
> Azure CDN endpoint multi-origin is currently in public preview.
> This preview version is provided without a service level agreement, and it's not recommended for production workloads. Certain features might not be supported or might have constrained capabilities. 
> For more information, see [Supplemental Terms of Use for Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## Create the origin group

1. Sign in to the [Azure portal](https://portal.azure.com)

2. Select your Azure CDN profile and then select the endpoint to be configured for multi-origin.

3. Select **Origin** under **Settings** in the endpoint configuration:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-1.png" alt-text="CDN endpoint" border="true":::

4. To enable multi-origin, you need at least one origin group. Select **Create Origin Group**:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-2.png" alt-text="Origin settings" border="true":::

5. In the **Add Origin Group** configuration, enter or select the following information:

   | Setting           | Value                                                                 |
   |-------------------|-----------------------------------------------------------------------|
   | Origin group name | Enter a name for your origin group.                                   |
   | Probe status      | Select **Enabled**. </br> Azure CDN will run health probes from different points across the globe to determine origin health. Don't enable if the current origin group isn't active to avoid additional cost.
   | Probe path        | The path in the origin that is used to determine the health. |
   | Probe interval    | Select a probe interval of 1, 2, or 4 minutes.                        |
   | Probe protocol    | Select **HTTP** or **HTTPS**.                                         |
   | Probe method      | Select **Head** or **Get**.                                           |
   | Default origin group | Select the box to set as default origin group.
    
   :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-3.png" alt-text="Add origin group" border="true":::

6. Select **Add**.

## Add multiple origins

1. In the origin settings for your endpoint, select **+ Create Origin**:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-5.png" alt-text="Create origin" border="true":::

2. Enter or select the following information in the **Add Origin** configuration:

   | Setting           | Value                                                                 |
   |-------------------|-----------------------------------------------------------------------|
   | Name        | Enter a name for the origin.        |
   | Origin Type | Select **Storage**, **Cloud Service**, **Web App**, or **Custom origin**.                                   |
   | Origin hostname        | Select or enter your origin hostname.  The drop-down lists all available origins of the type you specified in the previous setting. If you selected **Custom origin** as your origin type, enter the domain of your customer origin server. |
   | Origin host header    | Enter the host header you want Azure CDN to send with each request, or leave the default.                        |
   | HTTP port   | Enter the HTTP port.                                         |
   | HTTPS port     | Enter the HTTPS port.                                           |
   | Priority    | Enter a number between 1 and 5.       |
   | Weight      | Enter a number between 1 and 1000.   |

    > [!NOTE]
    > When an origin is created within an origin group, it must be accorded a priority and weight. If an origin group has only one origin, then the default priority and weight is set as 1. Traffic is routed to the highest priority origins if the origin is healthy. If an origin is determined to be unhealthy then the connections are diverted to another origin in the order of priority. If two origins have the same priority, then traffic is distributed as per weight specified for the origin 

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-6.png" alt-text="Add additional origin" border="true":::

3. Select **Add**.

4. Select **Configure origin** to set the origin path for all origins:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-7.png" alt-text="Configure origin path" border="true":::

5. Select **OK**.

## Configure origins and origin group settings

Once you have several origins and an origin group, you can add or remove the origins into different groups. Origins within the same group should serve similar workloads. Traffic will be distributed into these origins based on their healthy status, priority, and weight value. 

1. In the origin settings of the Azure CDN endpoint, select the name of the origin group you wish to configure:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-8.png" alt-text="Configure origins and origin group settings" border="true":::

2. In **Update origin group**, select **+ Select Origin**:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-9.png" alt-text="Update origin group" border="true":::

4. Select the origin to be added to the group in the pull-down box and select **OK**.

5. Verify the origin has been added to the group, then select **Save**:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-10.png" alt-text="Verify additional origin added to group" border="true":::

## Remove origin from origin group

1. In the origin settings of the Azure CDN endpoint, select the name of the origin group:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-8.png" alt-text="Remove origin from group" border="true":::

2. To remove an origin from the origin group, select the trash can icon next to the origin and select **Save**:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-11.png" alt-text="Update origin group delete origin" border="true":::

## Override origin group with rules engine

Customize how traffic is distributed to different origin groups by using the standard rules engine.

Distribute traffic to a different group based on the request URL.

1. In your CDN endpoint, select **Rules engine** under **Settings**:

:::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-12.png" alt-text="Rules engine" border="true":::

2. Select **+ Add rule**.

3. Enter a name for the rule in **Name**.

4. Select **+ Condition**, then select **URL path**.

5. In the **operator** pull-down, select **Contains**.

6. In **Value**, enter **/images**.

7. Select **+ Add action**, then select **Origin group override**.

8. In **Origin group**, select the origin group in the pull-down box.

:::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-13.png" alt-text="Rules engine conditions" border="true":::

For all incoming requests if the URL path contains **/images**, then the request will be assigned to the origin group in the action section **(myorigingroup)**. 

## Next Steps
In this article, you enabled Azure CDN endpoint multi-origin.

For more information on Azure CDN and the other Azure services mentioned in this article, see:

* [Azure CDN](./cdn-overview.md)
* [Compare Azure CDN product feature](./cdn-features.md)
