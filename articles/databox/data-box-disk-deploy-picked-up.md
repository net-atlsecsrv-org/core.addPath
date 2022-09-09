---
title: Tutorial to ship Azure Data Box Disk back| Microsoft Docs
description: Use this tutorial to learn how to ship your Azure Data Box Disk to Microsoft
services: databox
author: alkohli

ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.date: 09/19/2019
ms.author: alkohli
ms.localizationpriority: high
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
---

::: zone target="chromeless"

## Return Azure Data Box Disk 

::: zone-end

::: zone target="docs"

# Tutorial: Return Azure Data Box Disk 

This tutorial describes how to schedule a pick up to return your Azure Data Box Disk. The pick up instructions depend on where you are returning the device. 

In this tutorial, you will learn how to:

> [!div class="checklist"]
> * Ship Data Box Disk to Microsoft
> * Pick up Data Box Disk in different regions

## Prerequisites

Before you begin, make sure that you have completed the [Tutorial: Copy data to Azure Data Box Disk and verify](data-box-disk-deploy-copy-data.md).


## Ship Data Box Disk back

::: zone-end

1. Once the data validation is complete, unplug the disks. Remove the connecting cables.
2. Wrap all the disks and the connecting cables with a bubble wrap and place those into the shipping box. Charges may apply if the accessories are missing.
    - Reuse the packaging from the initial shipment.  
    - We recommend that you pack disks using a well-secured bubbled wrap.
    - Make sure the fit is snug to reduce any movements within the box.

The next steps are determined by where you are returning the device. The instructions are different for US/Canada, European Union (EU), Australia, or countries in Asia.

### [In US or Canada](#tab/in-us-or-canada)

Take the following steps if returning the device in US or Canada.

1. Use the return shipping label in the clear plastic sleeve affixed to the box. If the label is damaged or lost:
    - Go to **Overview > Download shipping label** and download a return ship label.
    - Affix the label on the device.

2. Seal the shipping box and ensure that the return shipping label is visible.
3. Schedule a pickup with UPS. To schedule a pickup:

    - Call the local UPS (country/region-specific toll free number).
    - In your call, quote the reverse shipment tracking number as shown in your printed label.
    - If the tracking number is not quoted, UPS will require you to pay an additional charge during pickup.
    - Instead of scheduling the pickup, you can also drop off the Data Box Disk at the nearest drop-off location.

### [In Europe](#tab/in-europe)

Take the following steps if returning the device in Europe.

1. Use the return shipping label in the clear plastic sleeve affixed to the box. If the label is damaged or lost:
    - Go to **Overview > Download shipping label** and download a return ship label.
    - Affix the label on the device.

2. Seal the shipping box and ensure that the return shipping label is visible.
3. If you are returning the device in Europe with DHL, request for pickup from DHL by visiting their website and specifying the airway bill number.
4. Go to the country/region DHL Express website and choose **Book a Courier Collection > eReturn Shipment**.    
3. Specify the waybill number and click **Schedule Pickup** to arrange for pickup.

### [In Australia](#tab/in-australia)

Azure datacenters in Australia have an additional security notification. All the inbound shipments must have an advanced notification. Take the following steps for pickup in Australia.

1. Use the provided return ship label and make sure that the TAU code (reference number) is written on it. If the provided shipping label is missing or you have any other issues, email [Data Box Asia Operations](mailto:adbo@microsoft.com). Provide the order name in subject header and details of the issue you are facing.
3. Affix the label on the box. 
4. Book a pick-up online at the link https://mydhl.express.dhl/au/en/schedule-pickup.html#/schedule-pickup#label-reference. 

### [In Japan](#tab/in-japan)

1. Write your company name and address information on the consignment note as your sender information.
2. Email Quantium solution using the following email template.

    ```
    To: Customerservice.JP@quantiumsolutions.com
    Subject: Pickup request for Microsoft Azure Data Box Disk｜Job Name： 
    Body: 
    - Japan Post Yu-Pack tracking number (reference number)：
    - Requested pickup date：mmdd (Select a requested time slot from below).
        a. 08：00-13：00 
        b. 13：00-15：00 
        c. 15：00-17：00 
        d. 17：00-19：00 
    ```
    - **If you are picking up in Osaka**, then modify the subject in the email template to: `Pickup request for Microsoft Azure OSA`.
    - If Japan Post Chakubarai consignment note wasn't included or is missing, note that in this email. Quantium Solutions Japan will request Japan Post to bring the consignment note upon pickup.
    - If you have multiple orders, email to ensure individual pickup.

3. Receive an email confirmation from Quantium solutions after you've booked a pickup. The email confirmation also includes information on the Chakubarai consignment note.

If needed, you can contact Quantium Solution Support (Japanese language) at the following information: 

- Email：Customerservice.JP@quantiumsolutions.com 
- Telephone：03-5755-0150 

### [In Korea](#tab/in-korea)

1. Make sure to include the return consignment note.
2. To request pickup when consignment note is present:
    1. Call *Quantium Solutions International* hotline at 070-8231-1418 during office hours (10 AM to 5 PM, Monday to Friday). Quote *Microsoft Azure pickup* and the service request number to arrange for a collection.  
    2. If the hotline is busy, email `microsoft@rocketparcel.com`, with the email subject *Microsoft Azure Pickup* and the service request number as reference.
    3. If the courier does not arrive for collection, call *Quantium Solutions International* hotline for alternate arrangements. 
    4. You receive an email confirmation for the pickup schedule.
3. Do this step only if the consignment note is not present. To request pickup:
    1. Call *Quantium Solutions International* hotline at 070-8231-1418 during office hours (10 AM to 5 PM, Monday to Friday). Quote *Microsoft Azure pickup* and the service request number to arrange for a collection. Specify that you need a new consignment note to arrange for a collection. Provide sender (customer), receiver information (Azure datacenter), and reference number (service request number). 
    2. If the hotline is busy, email `microsoft@rocketparcel.com`, with the email subject *Microsoft Azure Pickup* and the service request number as reference.
    3. If the courier does not arrive for collection, call *Quantium Solutions International* hotline for alternate arrangements. 
    4. You receive a verbal confirmation if the request is made via telephone.


### [In Singapore](#tab/in-singapore)

1. Print the shipping label and attach onto the box. If the label is damaged or lost:
    - Go to **Overview > Download shipping label** and get a return shipping label.
    - Affix the label on the device. Make sure that the label is visible.

2. To request pickup:
    - Call **SingPost** hotline at **6845 6485** during office hours (9am to 5pm, Monday to Friday).  
    - Quote *Microsoft Azure pickup* and the service request number (tracking number on the return shipping label) to arrange for a collection. 
    - You will get a verbal confirmation for the pickup schedule. 
    - If the courier does not arrive for collection, call **SingPost** at **6845 6485** for alternate arrangements. 
3. Hand over to the courier. 


::: zone target="docs"

## Next steps

In this tutorial, you learned about Azure Data Box Disk topics such as:

> [!div class="checklist"]
> * Ship Data Box Disk to Microsoft
> * Pick up Data Box Disk in different regions

Advance to the next how-to to learn how to verify data upload from Data Box Disk to the Azure Storage account.

> [!div class="nextstepaction"]
> [Verify data upload from Azure Data Box Disk](./data-box-disk-deploy-picked-up.md)

::: zone-end




