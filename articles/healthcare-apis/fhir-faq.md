---
title: FAQs about FHIR services in Azure - Azure API for FHIR
description: Get answers to frequently asked questions about the Azure API for FHIR, such as the storage location of data behind FHIR APIs and version support.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 08/03/2020
ms.author: matjazl
---

# Frequently asked questions about the Azure API for FHIR

## Azure API for FHIR: The Basics

### What is FHIR?
The Fast Healthcare Interoperability Resources (FHIR - Pronounced "fire") is an interoperability standard intended to enable the exchange of healthcare data between different health systems. This standard was developed by the HL7 organization and is being adopted by healthcare organizations around the world. The most current version of FHIR available is R4 (Release 4). The Azure API for FHIR supports R4 and also supports the previous version STU3 (Standard for Trial Use 3). For more information on FHIR, visit [HL7.org](http://hl7.org/fhir/summary.html).

### Is the data behind the FHIR APIs stored in Azure?

Yes, the data is stored in managed databases in Azure. The Azure API for FHIR does not provide direct access to the underlying data store.

### What identity provider do you support?

We currently support Microsoft Azure Active Directory as the identity provider.

### What is the Recovery Point Objective (RPO) for the Azure API for FHIR?
The Azure API for FHIR is backed by Cosmos DB as our persistence provider. Because of this, the RPO for the service equals [Cosmos DB (single region)](../cosmos-db/consistency-levels.md) and is < 240 minutes.

### What FHIR version do you support?

We support versions 4.0.0 and 3.0.1 on both the Azure API for FHIR (PaaS) and FHIR Server for Azure (open source).

For details, see [Supported features](fhir-features-supported.md). Read about what has changed between FHIR versions (i.e. STU3 to R4) in the [version history for HL7 FHIR](https://hl7.org/fhir/R4/history.html).

Azure IoT Connector for FHIR (preview) currently supports only FHIR version R4, and is visible only on R4 instances of Azure API for FHIR.

### What's the difference between 'Microsoft FHIR Server for Azure' and the 'Azure API for FHIR'?

The Azure API for FHIR is a hosted and managed version of the open-source Microsoft FHIR Server for Azure. In the managed service, Microsoft provides all maintenance and updates. 

When you run the FHIR Server for Azure, you have direct access to the underlying services, but are responsible for maintaining and updating the server and all required compliance work if you're storing PHI data.

For a development standpoint, every feature that doesn't apply only to the managed service is first deployed to the open-source Microsoft FHIR Server for Azure. Once it has been validated in open-source, it will be released to the PaaS Azure API for FHIR solution. The time between the release in open-source and PaaS depends on the complexity of the feature and other roadmap priorities. This is the same process for all of our services, such as Azure IoT Connector for FHIR (preview).

### Where can I see what is releasing into the Azure API for FHIR?

To see some of what is releasing into the Azure API for FHIR, please refer to the [release](https://github.com/microsoft/fhir-server/releases) of the open-source FHIR Server. Starting in November 2020, we have tagged items with Azure-API-for-FHIR if the open-source item will release to the managed service. These features are typically available two weeks after they are on the release page in open-source. We have also included instructions on how to test the build [here] (https://github.com/microsoft/fhir-server/blob/master/docs/Testing-Releases.md) if you would like to test in your own environment. We are evaluating how to best share additional managed service updates.

### In which regions is Azure API for FHIR Available?

Currently, we have general availability for both public and government in [multiple geo-regions](https://azure.microsoft.com/global-infrastructure/services/?products=azure-api-for-fhir&regions=non-regional,us-east,us-east-2,us-central,us-north-central,us-south-central,us-west-central,us-west,us-west-2,canada-east,canada-central,usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-texas,usgov-virginia). For information about government cloud services at Microsoft, check out [Azure services by FedRAMP](../azure-government/compliance/azure-services-in-fedramp-auditscope.md).

### Where can I see what is releasing into the Azure API for FHIR?

To see some of what is releasing into the Azure API for FHIR, please refer to the [release](https://github.com/microsoft/fhir-server/releases) of the open-source FHIR Server. We have worked to tag items with Azure-API-for-FHIR if they will release to the managed service and are usually available two weeks after they are on the release page in open-source. We have also included instructions on how to test the build [here](https://github.com/microsoft/fhir-server/blob/master/docs/Testing-Releases.md) if you would like to test in your own environment. We are evaluating how to best share additional managed service updates.

### What is SMART on FHIR?

SMART (Substitutable Medical Applications and Reusable Technology) on FHIR is a set of open specifications to integrate partner applications with FHIR Servers and other Health IT systems, such as Electronic Health Records and Health Information Exchanges. By creating a SMART on FHIR application, you can ensure that your application can be accessed and leveraged by a plethora of different systems.
Authentication and Azure API for FHIR. To learn more about SMART, visit [SMART Health IT](https://smarthealthit.org/).

### Where can I find what version of FHIR is running on my database. 

You can find the exact FHIR version exposed in the capability statement under the "fhirVersion" property.

## FHIR Implementations and Specifications

### Can I create a custom FHIR resource?

We do not allow custom FHIR resources. If you need a custom FHIR resource, you can build a custom resource on top of the [Basic resource](http://www.hl7.org/fhir/basic.html) with extensions. 

### Are [extensions](https://www.hl7.org/fhir/extensibility.html) supported on Azure API for FHIR?

We allow you to load any valid FHIR JSON data into the server. If you want to store the structure definition that defines the extension, you could save this as a structure definition resource. Currently, you cannot search on extensions.

### What is the limit on _count?

The current limit on _count is 100. If you set _count to more than 100, you will receive a warning in the bundle that only 100 records will be shown.

### Are there any limitations on the Group Export functionality?

For Group Export we only export the included references from the group, not all the characteristics of the [group resource](https://www.hl7.org/fhir/group.html).

### Can I post a bundle to the Azure API for FHIR?

We currently support posting [batch bundles](https://www.hl7.org/fhir/valueset-bundle-type.html) but do not support posting transaction bundles in the Azure API for FHIR. You can use the open-source FHIR Server backed by SQL to post transaction bundles.

### How can I get all resources for a single patient in the Azure API for FHIR?

We support [compartment search](https://www.hl7.org/fhir/compartmentdefinition.html) in the Azure API for FHIR. This allows you to get all the resources related to a specific patient. Note that right now compartment includes all the resources related to the patient but not the patient itself so you will need to also search to get the patient if you need the patient resource in your results.

Some examples of this are below:

* GET Patient/<id>/*
* GET Patient/<id>/Observation
* GET Patient/<id>/Observation?code=8302-2

### What is the default sort when searching for resources in Azure API for FHIR?

We support sorting by the date last updated: _sort=_lastUpdated. For more information about other supported search parameters, check out our [supported features page](./fhir-features-supported.md#search).

### How does $export work?

$export is part of the FHIR specification: https://hl7.org/fhir/uv/bulkdata/export/index.html. If the FHIR service is configured with a managed identity and a storage account, and if the managed identity has access to that storage account - you can simply call $export on the FHIR API and all the FHIR resources will be exported to the storage account. For more information, check out our [article on $export](./export-data.md).

## Using Azure API for FHIR

### How do I enable log analytics for Azure API for FHIR?

We enable diagnostic logging and allow reviewing sample queries for these logs. For details on enabling audit logs and sample queries, check out [this section](./enable-diagnostic-logging.md). If you want to include additional information in the logs, check out [using custom HTTP headers](./use-custom-headers.md).

### Where can I see some examples of using the Azure API for FHIR within a workflow?

We have a collection of reference architectures available on the [Health Architecture GitHub page](https://github.com/microsoft/health-architectures).

### Where can I see an example of connecting a web application to Azure API for FHIR?

We have a [Health Architecture GitHub page](https://aka.ms/health-architectures) that contains example applications and scenarios. It illustrates how to connect a web application to Azure API for FHIR.  

## Azure API for FHIR Features and Services 

### Is there a way to encrypt my data using my personal key not a default key?

Yes, Azure API for FHIR allows configuring customer-managed keys, leveraging support from Cosmos DB. For more information about encrypting your data with a personal key, check out [this section](./customer-managed-key.md).

## Azure API for FHIR: Preview Features

### Can I configure scaling capacity for Azure IoT Connector for FHIR (preview)?

Since Azure IoT Connector for FHIR is free of charge during public preview, its scaling capacity is fixed and limited. Azure IoT Connector for FHIR configuration available in public preview is expected to provide a throughput of about 200 messages per second. Some form of resource capacity configuration will be made available in General Availability (GA).

### Why can't I install Azure IoT Connector for FHIR (preview) when Private Link is enabled on Azure API for FHIR?

Azure IoT Connector for FHIR doesn't support Private Link capability at this time. Hence, if you have Private Link enabled on Azure API for FHIR, you can't install Azure IoT Connector for FHIR and vice-versa. This limitation is expected to go away when Azure IoT Connector for FHIR is available for General Availability (GA).
