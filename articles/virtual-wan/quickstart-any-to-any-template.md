---
title: 'Quickstart: Create an Any-to-any configuration using an ARM template'
titleSuffix: Azure Virtual WAN
description: This quickstart shows you how to create an Any-to-any configuration using an Azure Resource Manager template (ARM template).
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: quickstart
ms.date: 02/02/2021
ms.author: cherylmc
ms.custom: subject-armqs

---

# Quickstart: Create an Any-to-any configuration using an ARM template

This quickstart describes how to use an Azure Resource Manager template (ARM template) to create an Any-to-any scenario where any spoke can reach another spoke.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

If your environment meets the prerequisites and you're familiar with using ARM templates, select the **Deploy to Azure** button. The template will open in the Azure portal.

[![Deploy to Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f201-virtual-wan-with-all-gateways%2fazuredeploy.json)

## Prerequisites

* If you don't have an Azure subscription, create a [free account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) before you begin.
* Public key certificate data is required for this configuration. Sample data is provided in the article. However, the sample data is provided only to satisfy the template requirements in order to create a P2S gateway. After the template completes and the resources are deployed, you must update this field with your own certificate data in order for the configuration to work. See [User VPN certificates](certificates-point-to-site.md#cer).

## <a name="review"></a>Review the template

The template used in this quickstart is from [Azure Quickstart Templates](https://azure.microsoft.com/resources/templates/201-virtual-wan-with-all-gateways). The template for this article is too long to show here. To view the template, see [azuredeploy.json](https://github.com/Azure/azure-quickstart-templates/blob/master/201-virtual-wan-with-all-gateways/azuredeploy.json).

In this quickstart, you'll create an Azure Virtual WAN multi-hub deployment, including all gateways and VNet connections. The list of input parameters has been purposely kept at a minimum. The IP addressing scheme can be changed by modifying the variables inside of the template. The scenario is explained further in the [Any-to-any scenario](scenario-any-to-any.md) article.

:::image type="content" source="./media/routing-scenarios/any-any/figure-1.png" alt-text="Deployment architecture":::

This template creates a fully functional Azure Virtual WAN environment with the following resources:

* Two distinct hubs in different regions
* Four Azure virtual networks (VNet)
* Two VNet connections for each VWAN hub
* One Point-to-Site (P2S) VPN gateway in each hub
* One Site-to-Site (S2S) VPN gateway in each hub
* One ExpressRoute gateway in each hub

Multiple Azure resources are defined in the template:

* [**Microsoft.Network/virtualwans**](/azure/templates/microsoft.network/virtualwans)
* [**Microsoft.Network/virtualhubs**](/azure/templates/microsoft.network/virtualhubs)
* [**Microsoft.Network/virtualnetworks**](/azure/templates/microsoft.network/virtualnetworks)
* [**Microsoft.Network/hubvirtualnetworkconnections**](/azure/templates/microsoft.network/virtualhubs/hubvirtualnetworkconnections)
* [**Microsoft.Network/p2svpngateways**](/azure/templates/microsoft.network/p2svpngateways)
* [**Microsoft.Network/vpngateways**](/azure/templates/microsoft.network/vpngateways) 
* [**Microsoft.Network/expressroutegateways**](/azure/templates/microsoft.network/expressroutegateways)
* [**Microsoft.Network/vpnserverconfigurations**](/azure/templates/microsoft.network/vpnserverconfigurations)

>[!NOTE]
> This ARM template doesn't create the customer-side resources required for hybrid connectivity. After you deploy the template, you still need to create and configure the P2S VPN clients, the VPN branches (Local Sites), and connect the ExpressRoute circuits.
>

To find more templates, see [Azure Quickstart Templates](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular).

## <a name="deploy"></a>Deploy the template

To deploy this template properly, you must use the button to Deploy to Azure button and the Azure portal, rather than other methods, for the following reasons:

* In order to create the P2S configuration, you need to upload the root certificate data. The data field does not accept the certificate data when using PowerShell or CLI.
* This template does not work properly using Cloud Shell due to the certificate data upload.
* Additionally, you can easily modify the template and parameters in the portal to accommodate IP address ranges and other values.

1. Click **Deploy to Azure**.

   [![Deploy to Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f201-virtual-wan-with-all-gateways%2fazuredeploy.json)
1. To view the template, click **Edit template**. On this page, you can adjust some of the values such as address space or the name of certain resources. **Save** to save your changes, or **Discard**.
1. On the template page, enter the values. For this template, the P2S public certificate data is required. If you are using this article as an exercise, you can use the following data from this .cer file as sample data for both hubs. Once the template runs and deployment is complete, in order to use the P2S configuration, you must replace this information with the public key [certificate data](certificates-point-to-site.md#cer) for your own deployment.

   ```certificate-data
   MIIC5zCCAc+gAwIBAgIQGxd3Av1q6LJDZ71e3TzqcTANBgkqhkiG9w0BAQsFADAW
   MRQwEgYDVQQDDAtQMlNSb290Q2VydDAeFw0yMDExMDkyMjMxNTVaFw0yMTExMDky
   MjUxNTVaMBYxFDASBgNVBAMMC1AyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEF
   AAOCAQ8AMIIBCgKCAQEA33fFra/E0YmGuXLKmYcdvjsYpKwQmw8DjjDkbwhE9jcc
   Dp50e7F1P6Rxo1T6Hm3dIhEji+0QkP4Ie0XPpw0eW77+RWUiG9XJxGqtJ3Q4tyRy
   vBfsHORcqMlpV3VZOXIxrk+L/1sSm2xAc2QGuOqKaDNNoKmjrSGNVAeQHigxbTQg
   zCcyeuhFxHxAaxpW0bslK2hEZ9PhuAe22c2SHht6fOIDeXkadzqTFeV8wEZdltLr
   6Per0krxf7N2hFo5Cfz0KgWlvgdKLL7dUc9cjHo6b6BL2pNbLh8YofwHQOQbwt6H
   miAkEnx1EJ5N8AWuruUTByR2jcWyCnEAUSH41+nk4QIDAQABozEwLzAOBgNVHQ8B
   Af8EBAMCAgQwHQYDVR0OBBYEFJMgnJSYHH5AJ+9XB11usKRwjbjNMA0GCSqGSIb3
   DQEBCwUAA4IBAQBOy8Z5FBd/nvgDcjvAwNCw9h5RHzgtgQqDP0qUjEqeQv3ALeC+
   k/F2Tz0OWiPEzX5N+MMrf/jiYsL2exXuaPWCF5U9fu8bvs89GabHma8MGU3Qua2x
   Imvt0whWExQMjoyU8SNUi2S13fnRie9ZlSwNh8B/OIUUEtVhQsd4OfuZZFVH4xGp
   ibJMSMe5JBbZJC2tCdSdTLYfYJqrLkVuTjynXOjmz2JXfwnDNqEMdIMMjXzlNavR
   J8SNtAoptMOK5vAvlySg4LYtFyXkl0W0vLKIbbHf+2UszuSCijTUa3o/Y1FoYSfi
   eJH431YTnVLuwdd6fXkXFBrXDhjNsU866+hE
   ```

1. When you have finished entering values, select **Review + create**.
1. On the **Review + create** page, after validation passes, select **Create**.
1. It takes about 75 minutes for the deployment to complete. You can view the progress on the template **Overview** page.  If you close the portal, deployment will continue.

   :::image type="content" source="./media/quickstart-any-to-any-template/template.png" alt-text="Example of deployment complete":::

## <a name="validate"></a>Validate the deployment

1. Sign in to the [Azure portal](https://portal.azure.com).
1. Select **Resource groups** from the left pane.
1. Select the resource group that you created in the previous section. On the **Overview** page, you will see something similar to this example:
   :::image type="content" source="./media/quickstart-any-to-any-template/resources.png" alt-text="Example of resources" lightbox="./media/quickstart-any-to-any-template/resources.png":::

1. Click the virtual WAN to view the hubs. On the virtual WAN page, click each hub to view connections and other hub information.
   :::image type="content" source="./media/quickstart-any-to-any-template/hub.png" alt-text="Example of hubs" lightbox="./media/quickstart-any-to-any-template/hub.png":::

## <a name="complete"></a>Complete the hybrid configuration

The template does not configure all of the settings necessary for a hybrid network. You need to complete the following configurations and settings, depending on your requirements.

* [Configure the VPN branches - local sites](virtual-wan-site-to-site-portal.md#site)
* [Complete the P2S VPN configuration](virtual-wan-point-to-site-portal.md)
* [Connect the ExpressRoute circuits](virtual-wan-expressroute-portal.md)

## Clean up resources

When you no longer need the resources that you created, delete them. Some of the Virtual WAN resources must be deleted in a certain order due to dependencies. Deleting can take about 30 minutes to complete.

[!INCLUDE [Delete resources](../../includes/virtual-wan-resource-cleanup.md)]

## Next steps

> [!div class="nextstepaction"]
> [Complete the P2S VPN configuration](virtual-wan-point-to-site-portal.md)

> [!div class="nextstepaction"]
> [Configure the VPN branches - local sites](virtual-wan-site-to-site-portal.md#site)

> [!div class="nextstepaction"]
> [Connect the ExpressRoute circuits](virtual-wan-expressroute-portal.md)