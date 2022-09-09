---
title: Azure Functions Premium plan 
description: Details and configuration options (VNet, no cold start, unlimited execution duration) for the Azure Functions Premium plan.
author: jeffhollan
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: jehollan

---

# Azure Functions Premium plan

The Azure Functions Premium plan is a hosting option for function apps. The Premium plan provides features like VNet connectivity, no cold start, and premium hardware.  Multiple function apps can be deployed to the same Premium plan, and the plan allows you to configure compute instance size, base plan size, and maximum plan size.  For a comparison of the Premium plan and other plan and hosting types, see [function scale and hosting options](functions-scale.md).

## Create a Premium plan

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]

You can also create a Premium plan using [az functionapp plan create](/cli/azure/functionapp/plan#az-functionapp-plan-create) in the Azure CLI. The following example creates an _Elastic Premium 1_ tier plan:

```azurecli-interactive
az functionapp plan create --resource-group <RESOURCE_GROUP> --name <PLAN_NAME> \
--location <REGION> --sku EP1
```

In this example, replace `<RESOURCE_GROUP>` with your resource group and `<PLAN_NAME>` with a name for your plan that is unique in the resource group. Specify a [supported `<REGION>`](#regions). To create a Premium plan that supports Linux, include the `--is-linux` option.

With the plan created, you can use [az functionapp create](/cli/azure/functionapp#az-functionapp-create) to create your function app. In the portal, both the plan and the app are created at the same time. 

## Features

The following features are available to function apps deployed to a Premium plan.

### Pre-warmed instances

If no events and executions occur today in the Consumption plan, your app may scale down to zero instances. When new events come in, a new instance needs to be specialized with your app running on it.  Specializing new instances may take some time depending on the app.  This additional latency on the first call is often called app cold start.

In the Premium plan, you can have your app pre-warmed on a specified number of instances, up to your minimum plan size.  Pre-warmed instances also let you pre-scale an app before high load. As the app scales out, it first scales into the pre-warmed instances. Additional instances continue to buffer out and warm immediately in preparation for the next scale operation. By having a buffer of pre-warmed instances, you can effectively avoid cold start latencies.  Pre-warmed instances is a feature of the Premium plan, and you need to keep at least one instance running and available at all times the plan is active.

You can configure the number of pre-warmed instances in the Azure portal by selected your **Function App**, going to the **Platform Features** tab, and selecting the **Scale Out** options. In the function app edit window, pre-warmed instances is specific to that app, but the minimum and maximum instances apply to your entire plan.

![Elastic Scale Settings](./media/functions-premium-plan/scale-out.png)

You can also configure pre-warmed instances for an app with the Azure CLI

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.preWarmedInstanceCount=<desired_prewarmed_count> --resource-type Microsoft.Web/sites
```

### Private network connectivity

Azure Functions deployed to a Premium plan takes advantage of [new VNet integration for web apps](../app-service/web-sites-integrate-with-vnet.md).  When configured, your app can communicate with resources within your VNet or secured via service endpoints.  IP restrictions are also available on the app to restrict incoming traffic.

When assigning a subnet to your function app in a Premium plan, you need a subnet with enough IP addresses for each potential instance. We require an IP block with at least 100 available addresses.

Fore more information, see [integrate your function app with a VNet](functions-create-vnet.md).

### Rapid elastic scale

Additional compute instances are automatically added for your app using the same rapid scaling logic as the Consumption plan.  To learn more about how scaling works, see [Function scale and hosting](./functions-scale.md#how-the-consumption-and-premium-plans-work).

### Longer run duration

Azure Functions in a Consumption plan are limited to 10 minutes for a single execution.  In the Premium plan, the run duration defaults to 30 minutes to prevent runaway executions. However, you can [modify the host.json configuration](./functions-host-json.md#functiontimeout) to make this 60 minutes for Premium plan apps.

## Plan and SKU settings

When you create the plan, you configure two settings: the minimum number of instances (or plan size) and the maximum burst limit.  Minimum instances are reserved and always running.

> [!IMPORTANT]
> You are charged for each instance allocated in the minimum instance count regardless if functions are executing or not.

If your app requires instances beyond your plan size, it can continue to scale out until the number of instances hits the maximum burst limit.  You are billed for instances beyond your plan size only while they are running and rented to you.  We will make a best effort at scaling your app out to its defined maximum limit, whereas the minimum plan instances are guaranteed for your app.

You can configure the plan size and maximums in the Azure portal by selected the **Scale Out** options in the plan or a function app deployed to that plan (under **Platform Features**).

You can also increase the maximum burst limit from the Azure CLI:

```azurecli-interactive
az resource update -g <resource_group> -n <premium_plan_name> --set properties.maximumElasticWorkerCount=<desired_max_burst> --resource-type Microsoft.Web/serverfarms 
```

### Available instance SKUs

When creating or scaling your plan, you can choose between three instance sizes.  You will be billed for the total number of cores and memory consumed per second.  Your app can automatically scale out to multiple instances as needed.  

|SKU|Cores|Memory|Storage|
|--|--|--|--|
|EP1|1|3.5GB|250GB|
|EP2|2|7GB|250GB|
|EP3|4|14GB|250GB|

## Regions

Below are the currently supported regions for each OS.

|Region| Windows | Linux |
|--| -- | -- |
|Australia Central| ✔<sup>1</sup> | |
|Australia Central 2| ✔<sup>1</sup> | |
|Australia East| ✔ | |
|Australia Southeast | ✔ | ✔ |
|Brazil South| ✔<sup>2</sup> |  |
|Canada Central| ✔ |  |
|Central US| ✔ |  |
|East Asia| ✔ |  |
|East US | ✔ | ✔ |
|East US 2| ✔ |  |
|France Central| ✔ |  |
|Japan East| ✔ | ✔ |
|Japan West| ✔ | |
|Korea Central| ✔ |  |
|North Central US| ✔ |  |
|North Europe| ✔ | ✔ |
|South Central US| ✔ |  |
|South India | ✔ | |
|Southeast Asia| ✔ | ✔ |
|UK South| ✔ | |
|UK West| ✔ |  |
|West Europe| ✔ | ✔ |
|West India| ✔ |  |
|West US| ✔ | ✔ |
|West US 2| ✔ |  |

<sup>1</sup>Maximum scale out limited to 20 instances.  
<sup>2</sup>Maximum scale out limited to 60 instances.


## Next steps

> [!div class="nextstepaction"]
> [Understand Azure Functions scale and hosting options](functions-scale.md)
