---
title: 'PowerShell: Deploy FHIR server for Azure – Azure API for FHIR'
description: This quickstart explains how to deploy the Microsoft Open Source FHIR server using PowerShell.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart 
ms.date: 02/07/2019
ms.author: matjazl
---

# Quickstart: Deploy Open Source FHIR server using PowerShell

In this quickstart, learn how to deploy the Open Source Microsoft FHIR server for Azure Using PowerShell.

If you don't have an Azure subscription, create a [free account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) before you begin.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## Create a resource group

Pick a name for the resource group that will contain the provisioned resources and create it:

```azurepowershell-interactive
$fhirServiceName = "MyFhirService"
$rg = New-AzResourceGroup -Name $fhirServiceName -Location westus2
```

## Deploy the FHIR server template

The Microsoft FHIR Server for Azure [GitHub Repository](https://github.com/Microsoft/fhir-server) contains a template that will deploy all necessary resources. The deployment process takes several minutes as required Azure resources are created and configured. Deploy it with:

```azurepowershell-interactive
New-AzResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Microsoft/fhir-server/master/samples/templates/default-azuredeploy.json -ResourceGroupName $rg.ResourceGroupName -serviceName $fhirServiceName
```

> [!NOTE]
> If you aren't already logged in, run the following commands first.

```azurepowershell-interactive
Connect-AzAccount
get-azsubscription
Set-AzContext -SubscriptionId yoursubscriptionid
```

To use an existing resource group, change $rg values in the $rg variable definition line and the Azure ARM template deployment command line, as shown in the code.

```azurepowershell-interactive
$fhirServiceName = "MyFhirService"
$rg = "MyExistingResourceGroupName"
New-AzResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Microsoft/fhir-server/master/samples/templates/default-azuredeploy.json -ResourceGroupName $rg -serviceName $fhirServiceName
```

## Verify FHIR server is running

```azurepowershell-interactive
$metadataUrl = "https://" + $fhirServiceName + ".azurewebsites.net/metadata" 
$metadata = Invoke-WebRequest -Uri $metadataUrl
$metadata.RawContent
```

It will take a minute or so for the server to respond the first time.

## Clean up resources

If you're not going to continue to use this application, delete the resource group
with the following steps:

```azurepowershell-interactive
Remove-AzResourceGroup -Name $rg.ResourceGroupName
```

## Next steps

In this tutorial, you've deployed the Microsoft Open Source FHIR Server for Azure into your subscription. To learn how to access the FHIR API using Postman, proceed to the Postman tutorial.
 
>[!div class="nextstepaction"]
>[Access FHIR API using Postman](access-fhir-postman-tutorial.md)
