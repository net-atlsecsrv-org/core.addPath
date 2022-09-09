---
title: Create a Video Indexer account in the Azure portal
titleSuffix: Azure Media Services
description: This article shows how to create a Video Indexer account in the Azure portal.
services: media-services
author: Juliako
manager: femila

ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 08/05/2019
ms.author: juliako
---

# Create a Video Indexer account connected to Azure

When creating a Video Indexer account, you can choose a free trial account (where you get a certain number of free indexing minutes) or a paid option (where you are not limited by the quota). With free trial, Video Indexer provides up to 600 minutes of free indexing to website users and up to 2400 minutes of free indexing to API users. With the paid option, you create a Video Indexer account that is connected to your Azure subscription and an Azure Media Services account. You pay for minutes indexed as well as the Media Account related charges. 

This article shows how to create a Video Indexer account that's linked to an Azure subscription and an Azure Media Services account. The topic provides steps for connecting to Azure using the automatic (default) flow. It also shows how to connect to Azure manually (advanced).

## Prerequisites

* An Azure subscription.

    If you don't have an Azure subscription yet, sign up for [Azure Free Trial](https://azure.microsoft.com/free/).

* An Azure Active Directory (AD) domain.

    If you don't have an Azure AD domain, create this domain with your Azure subscription. For more information, see [Managing custom domain names in your Azure Active Directory](../../active-directory/users-groups-roles/domains-manage.md)

* A user in your Azure AD domain with an **Application administrator** role. You'll use this member when connecting your Video Indexer account to Azure.

    This user should be an Azure AD user with a work or school account, not a personal account, such as outlook.com, live.com, or hotmail.com.

    ![all AAD users](./media/create-account/all-aad-users.png)

### Additional prerequisites for automatic flow

A user and member in your Azure AD domain. You'll use this member when connecting your Video Indexer account to Azure.

This user should be a member in your Azure subscription with either an **Owner** role, or both **Contributor** and **User Access Administrator** roles. A user can be added twice, with 2 roles. Once with Contributor and once with user Access Administrator.

![access control](./media/create-account/access-control-iam.png)

### Additional prerequisites for manual flow

Register the EventGrid resource provider using the Azure portal.

In the [Azure portal](https://portal.azure.com/), go to **Subscriptions**->[subscription]->**ResourceProviders**. 

Search for **Microsoft.Media** and **Microsoft.EventGrid**. If not in the "Registered" state, click **Register**. It takes a couple of minutes to register.

![EventGrid](./media/create-account/event-grid.png)

## Connect to Azure

> [!NOTE]
> If your Azure subscription uses certificate-based multi-factor authentication, it is crucial that you perform the following steps on a device that has the required certificates installed.

1. Browse to the [Video Indexer](https://www.videoindexer.ai/) website and sign in.

2. Click on the **Create new account** button:

    ![connect to Azure](./media/create-account/connect-to-azure.png)

3. When the subscriptions list appears, select the subscription you want to use.

    ![connect Video Indexer to Azure](./media/create-account/connect-vi-to-azure-subscription.png)

4. Select an Azure region from the supported locations: West US 2, North Europe, or East Asia.
5. Under **Azure Media Services account**, choose one of these options:

    * To create a new Media Services account, select **Create new resource group**. Provide a name for your resource group.

        Azure will create your new account in your subscription, including a new Azure Storage account. Your new Media Services account has a default initial configuration with a Streaming Endpoint and 10 S3 Reserved Units.
    * To use an existing Media Services account, select **Use existing resource**. From the accounts list, select your account.

        Your Media Services account must have the same region as your Video Indexer account. 

        > [!NOTE]
        > To minimize indexing duration and low throughput, it is highly recommended to adjust the type and number of [Reserved Units](../previous/media-services-scale-media-processing-overview.md ) to **10 S3 Reserved Units** in your Media Services account. See [Use portal to change Reserved Units](../previous/media-services-portal-scale-media-processing.md).

    * To manually configure your connection, click the **Switch to manual configuration** link.

        For detailed information, see the [Connect to Azure manually](#connect-to-azure-manually-advanced-option) (advanced option) section that follows.
6. When you're done, choose **Connect**. This operation might take up to a few minutes. 

    After you're connected to Azure, your new Video Indexer account appears in the account list:

    ![new account](./media/create-account/new-account.png)

7. Browse to your new account

## Connect to Azure manually (advanced option)

If the connection to Azure failed, you can attempt to troubleshoot the problem by connecting manually.

> [!NOTE]
> It is highly recommended to have the following three accounts in the same region: the Video Indexer account that you are connecting with the Media Services account, as well as the Azure storage account connected to the same Media Services account.

### Create and configure a Media Services account

1. Use the [Azure](https://portal.azure.com/) portal to create an Azure Media Services account, as described in [Create an account](../previous/media-services-portal-create-account.md).

    When creating a storage account for your Media Services account, select **StorageV2** for account kind and **Geo-redundant (GRS)** for replication fields.

    ![new AMS account](./media/create-account/create-ams-account1.png)

    > [!NOTE]
    > Make sure to write down the Media Services resource and account names. You will need it for the steps in the next section.

2. Adjust the type and number of [Reserved Units](../previous/media-services-scale-media-processing-overview.md ) to **10 S3 Reserved Units** in the Media Services account you created. See [Use portal to change Reserved Units](../previous/media-services-portal-scale-media-processing.md).
3. Before you can play your videos in the Video Indexer web application, you must start the default **Streaming Endpoint** of the new Media Services account.

    In the new Media Services account, click **Streaming endpoints**. Select the Streaming Endpoint and press start.

    ![new AMS account](./media/create-account/create-ams-account2.png)

4. For Video Indexer to authenticate with Media Services API, an AD application needs to be created. The following steps guide you through the Azure AD authentication process described in [Get started with Azure AD authentication by using the Azure portal](../previous/media-services-portal-get-started-with-aad.md):

    1. In the new Media Services account, select **API access**.
    2. Select [Service principal authentication method](../previous/media-services-portal-get-started-with-aad.md).
    3. Get the client ID and client secret

        After you select **Settings**->**Keys**, add **Description**, press **Save**, the key value gets populated.

        If the key expires the account owner will have to contact Video Indexer support to renew the key.

        > [!NOTE]
        > Make sure to write down the key value and the Application ID. You will need it for the steps in the next section.

### Connect manually

In the **Connect Video Indexer to an Azure subscription** dialog of your [Video Indexer](https://www.videoindexer.ai/) page, select the **Switch to manual configuration** link.

In the dialog, provide the following information:

|Setting|Description|
|---|---|
|Video Indexer account region|The name of the Video Indexer account region. For better performance and lower costs, it is highly recommended to specify the name of the region where the Azure Media Services resource and Azure Storage account are located. |
|Azure Active Directory (AAD) tenant|The name of the Azure AD tenant, for example "contoso.onmicrosoft.com". The tenant information can be retrieved from the Azure portal. Place your cursor over the name of the signed-in user in the top-right corner. Find the name to the right of **Domain**.|
|Subscription ID|The Azure subscription under which this connection should be created. The subscription ID can be retrieved from the Azure portal. Click on **All services** in the left panel, and search for "subscriptions". Select **Subscriptions** and choose the desired ID from the list of your subscriptions.|
|Azure Media Services resource group name|The name for the resource group in which you created the Media Services account.|
|Media service resource name|The name of the Azure Media Services account that you created in the previous section.|
|Application ID|The Azure AD application ID (with permissions for the specified Media Services account) that you created in the previous section.|
|Application Key|The Azure AD application key that you created in the previous section. |

## Considerations

The following Azure Media Services related considerations apply:

* If you connect automatically, you see a new Resource Group, Media Services account, and a Storage account in your Azure subscription.
* If you connect automatically, Video Indexer sets the media **Reserved Units** to 10 S3 units:

    ![Media Services reserved units](./media/create-account/ams-reserved-units.png)

* If you connect to an existing Media Services account, Video Indexer does not change the existing media **Reserved Units** configuration.

   You might need to adjust the type and number of Media Reserved Units, according to your planned load. Keep in mind that if your load is high and you don't have enough units or speed, videos processing can result in timeout failures.

* If you connect to a new Media Services account, Video Indexer automatically starts the default **Streaming Endpoint** in it:

    ![Media Services streaming endpoint](./media/create-account/ams-streaming-endpoint.png)

    Streaming Endpoints have a considerable startup time. Therefore, it may take several minutes from the time you connected your account to Azure, until your videos can be streamed and watched in the Video Indexer web application.

* If you connect to an existing Media Services account, Video Indexer does not change the default Streaming Endpoint configuration. If there is no running **Streaming Endpoint**, you will not be able to watch videos from this Media Services account or in Video Indexer.

## Next steps

You can programmatically interact with your trial account and/or with your Video Indexer accounts that are connected to azure by following the instructions in: [Use APIs](video-indexer-use-apis.md).

You should use the same Azure AD user you used when connecting to Azure.


