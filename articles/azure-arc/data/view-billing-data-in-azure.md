---
title: Upload billing data to Azure and view it in the Azure portal
description: Upload billing data to Azure and view it in the Azure portal
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
---

# Upload billing data to Azure and view it in the Azure portal

> [!IMPORTANT] 
>  There is no cost to use Azure Arc enabled data services during the preview period. Although the billing system works end to end the billing meter is set to $0.  If you follow this scenario, you will see entries in your billing for a service currently named **hybrid data services** and for resources of a type called **microsoft.AzureData/`<resource type>`**. You will be able to see a record for each data service - Azure Arc that you create, but each record will be billed for $0.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## Connectivity Modes - Implications for billing data

In the future, there will be two modes in which you can run your Azure Arc enabled data services:

- **Indirectly connected** - There is no direct connection to Azure. Data is sent to Azure only through an export/upload process. All Azure Arc data services deployments work in this mode today in preview.
- **Directly connected** - In this mode there will be a dependency on the Azure Arc enabled Kubernetes service to provide a direct connection between Azure and the Kubernetes cluster on which the Azure Arc enabled data services are running. This will enable more capabilities and will also enable you to use the Azure portal and the Azure CLI to manage your Azure Arc enabled data services just like you manage your data services in Azure PaaS.  This connectivity mode is not yet available in preview, but will be coming soon.

You can read more about the difference between the [connectivity modes](https://docs.microsoft.com/azure/azure-arc/data/connectivity).

In the indirectly connected mode, billing data is periodically exported out of the Azure Arc data controller to a secure file and then uploaded to Azure and processed.  In the upcoming directly connected mode, the billing data will be automatically sent to Azure approximately 1/hour to give a near real-time view into the costs of your services. The process of exporting and uploading the data in the indirectly connected mode can also be automated using scripts or we may build a service that will do it for you.

## Upload billing data to Azure

To upload billing data to Azure, the following should happen first:

1. Create an Azure Arc enabled data service if you don't have one already. For example create one of the following:
   - [Create an Azure SQL managed instance on Azure Arc](create-sql-managed-instance.md)
   - [Create an Azure Arc enabled PostgreSQL Hyperscale server group](create-postgresql-hyperscale-server-group.md)
1. [Upload resource inventory, usage data, metrics and logs to Azure Monitor](upload-metrics-and-logs-to-azure-monitor.md) if you haven't already.
1. Wait for at least 2 hours since the creation of the data service so that the billing telemetry collection process can collect some billing data.

Run the following command to export out the billing data:

```console
azdata arc dc export -t usage -p usage.json
```

For now, the file is not encrypted so that you can see the contents. Feel free to open in a text editor and see what the contents look like.

You will notice that there are two sets of data: `resources` and `data`. The `resources` are the data controller, PostgreSQL Hyperscale server groups, and SQL Managed Instances. The `resources` records in the data capture the pertinent events in the history of a resource - when it was created, when it was updated, and when it was deleted. The `data` records capture how many cores were available to be used by a given instance for every hour.

Example of a `resource` entry:

```console
    {
        "customObjectName": "<resource type>-2020-29-5-23-13-17-164711",
        "uid": "4bc3dc6b-9148-4c7a-b7dc-01afc1ef5373",
        "instanceName": "sqlInstance001",
        "instanceNamespace": "arc",
        "instanceType": "<resource>",
        "location": "eastus",
        "resourceGroupName": "production-resources",
        "subscriptionId": "482c901a-129a-4f5d-86e3-cc6b294590b2",
        "isDeleted": false,
        "externalEndpoint": "32.191.39.83:1433",
        "vCores": "2",
        "createTimestamp": "05/29/2020 23:13:17",
        "updateTimestamp": "05/29/2020 23:13:17"
    }
```

Example of a `data` entry:

```console
        {
          "requestType": "usageUpload",
          "clusterId": "4b0917dd-e003-480e-ae74-1a8bb5e36b5d",
          "name": "DataControllerTestName",
          "subscriptionId": "482c901a-129a-4f5d-86e3-cc6b294590b2",
          "resourceGroup": "production-resources",
          "location": "eastus",
          "uploadRequest": {
            "exportType": "usages",
            "dataTimestamp": "2020-06-17T22:32:24Z",
            "data": "[{\"name\":\"sqlInstance001\",
                       \"namespace\":\"arc\",
                       \"type\":\"<resource type>\",
                       \"eventSequence\":1, 
                       \"eventId\":\"50DF90E8-FC2C-4BBF-B245-CB20DC97FF24\",
                       \"startTime\":\"2020-06-17T19:11:47.7533333\",
                       \"endTime\":\"2020-06-17T19:59:00\",
                       \"quantity\":1,
                       \"id\":\"4BC3DC6B-9148-4C7A-B7DC-01AFC1EF5373\"}]",
           "signature":"MIIE7gYJKoZIhvcNAQ...2xXqkK"
          }
        }
```

Run the following command to upload the usage.json file to Azure:

```console
azdata arc dc upload -p usage.json
```

## View billing data in Azure portal

Follow these steps to view billing data in the Azure portal:

1. Open the Azure portal using the special URL:  [https://aka.ms/arcdata](https://aka.ms/arcdata).
1. In the search box at the top of the screen type in **Cost Management** and click on the Cost Management service.
1. Click on the **Cost analysis** tab on the left.
1. Click the **Cost by resource** button on the top of the view.
1. Make sure that your Scope is set to the subscription in which your data service resources were created.
1. Select **Cost by resource** in the View drop down next to the Scope selector near the top of the view.
1. Make sure the date filter is set to **This month** or some other time range that makes sense given the timing of when your created your data service resources.
1. Click **Add filter** to add a filter by **Resource type** = `microsoft.azuredata/<data service type>` if you want to filter down to just one type of Azure Arc enabled data service.
1. You will now see a list of all the resources that were created and uploaded to Azure. Since the billing meter is $0, you will see that the cost is always $0.

## Download billing data

You can download billing summary data directly from the Azure portal.

1. In the same **Cost analysis -> view by resource type** view that you reached by following the instructions above, click the Download button near the top.
1. Choose your download file type - Excel or CSV - and click the **Download data** button.
1. Open the file in an appropriate editor given the file type selected.

## Export billing data

You can also periodically, automatically export **detailed** usage and billing data to an Azure Storage container by creating a billing export job. This is useful if you want to see the details of your billing such as how many hours a given instance was billed for in the billing period.

Follow these steps to set up a billing export job:

1. Click Exports on the left.
1. Click Add.
1. Enter a name and export frequency and click Next.
1. Choose to either create a new storage account or create a new one and fill out the form to specify the storage account, container, and directory path to export the billing data files to and click Next.
1. Click Create.

The billing data export files will be available in approximately 4 hours and will be exported on the schedule you specified when creating the billing export job.

Follow these steps to view the billing data files that are exported:

You can validate the billing data files in the Azure portal. 

> [!IMPORTANT]
> After you create the billing export job, wait 4 hours before you proceed with the following steps.

1. In the search box at the top of the portal, type in **Storage accounts** and click on **Storage Accounts**.
3. Click on the storage account which you specified when creating the billing export job above.
4. Click on Containers on the left.
5. Click on the container you specified when creating the billing export job above.
6. Click on the folder you specified when creating the billing export job above.
7. Drill down into the generated folders and files and click on one of the generated .csv files.
8. Click the Download button which will save the file to your local Downloads folder.
9. Open the file using a .csv file viewer such as Excel.
10. Filter the results to show only the rows with the **Resource Type** = `Microsoft.AzureData/<data service resource type`.
11. You will see the number of hours the instance was used in the current 24 hour period in the UsageQuantity column.
