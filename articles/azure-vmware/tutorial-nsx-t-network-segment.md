---
title: "Tutorial: Create an NSX-T network segment in Azure VMware Solution (AVS)"
description: In this tutorial, you created the NSX-T network segments that are used for VMs in vCenter
ms.topic: tutorial
ms.date: 07/16/2020
---

# Tutorial: Create an NSX-T network segment in Azure VMware Solution (AVS)

Network segments created in NSX-T Manager are used as networks for virtual machines (VMs) in vCenter. The VMs created in vCenter are placed onto the network segments created in NSX-T and are visible in vCenter.

In this tutorial, you learn how to:

> [!div class="checklist"]
> * Navigate in NSX-T Manager to add network segments
> * Add a new network segment
> * Observe the new network segment in vCenter

## Prerequisites

An AVS private cloud with access to the vCenter and NSX-T Manager management interfaces are required to complete this tutorial. See the [Tutorial: Configure networking for your VMware private cloud in Azure](tutorial-configure-networking.md).

## Provision a network segment in NSX-T

1. In vCenter for your private cloud, select **SDDC-Datacenter > Networks** and note that there are no networks yet.

   :::image type="content" source="media/nsxt/vcenter-without-ls01.png" alt-text="Select SDDC-Datacenter > Networks":::

1. In NSX-T Manager for your private cloud, select **Networking**.

   :::image type="content" source="media/nsxt/nsxt-network-overview.png" alt-text="Select Networking to navigate to NSX-T Manager Network Overview view.":::

1. Select **Segments**.

   :::image type="content" source="media/nsxt/nsxt-select-segments.png" alt-text="Select Segments in the Network Overview page.":::

1. In the NSX-T Segments overview page, select **ADD SEGMENT**. Three segments get created as part of the private cloud provisioning and can't be used for VMs.  You'll need to add a new network segment for this purpose.

   :::image type="content" source="media/nsxt/nsxt-segments-overview.png" alt-text="Select Add Segment from Network Segment overview page.":::

1. Name the segment, choose the pre-configured Tier1 Gateway (TNTxx-T1) as the **Connected Gateway**, leave the **Type** as Flexible, choose the pre-configured overlay **Transport Zone** (TNTxx-OVERLAY-TZ), and then select Set Subnets. All other settings in this section and the **PORTS** and **SEGMENT PROFILES** can remain in the default, as is configuration.

   :::image type="content" source="media/nsxt/nsxt-create-segment-specs.png" alt-text="Set the Segment Name, Connected Gateway and Type, and Transport Zone, then select “Set Subnets”.":::

1. Set the IP address of the gateway for the new segment and then select **ADD**. The IP address that you use needs to be on a non-overlapping RFC1918 address block, which ensures that you can connect to the VMs on the new segment.

   :::image type="content" source="media/nsxt/nsxt-create-segment-gateway.png" alt-text="Specify the segment gateway IP address in CIDR notation and select ADD.":::

1. Apply the new network segment by selecting **APPLY** and then save the configuration with **SAVE**.

   :::image type="content" source="media/nsxt/nsxt-create-segment-apply.png" alt-text="Apply the new network segment to the NSX-T configuration with APPLY.":::

   :::image type="content" source="media/nsxt/nsxt-create-segment-save.png" alt-text="Save the new network segment to the NSX-T configuration with SAVE.":::

1. The new network segment has now been created and you'll decline the option to continue configuring the segment by selecting **NO**.

   :::image type="content" source="media/nsxt/nsxt-create-segment-continue-no.png" alt-text="Decline to further configure the newly created network segment by selecting NO.":::

1. Confirm the new network segment is present in NSX-T by selecting **Networking > Segments** and seeing the new segment is in the list (in this case, "ls01").

   :::image type="content" source="media/nsxt/nsxt-new-segment-overview-2.png" alt-text="Confirm that the new network segment is present in NSX-T.":::

1. Confirm the new network segment is present in vCenter by selecting **Networking > SDDC-Datacenter** and observing the new segment is in the list (in this case, "ls01").

   :::image type="content" source="media/nsxt/vcenter-with-ls01-2.png" alt-text="Confirm that the new network segment is present in vCenter.":::

## Next steps

In this tutorial, you created the NSX-T network segments that are used for VMs in vCenter. You can now use the [Tutorial: Create a content Library to deploy VMs in Azure VMware Solution (AVS)](tutorial-deploy-vm-content-library.md) to create a Content Library in vCenter and the provision a VM on the network you created in this tutorial.

<!-- LINKS - external-->

<!-- LINKS - internal -->
