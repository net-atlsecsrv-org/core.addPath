---
title: IT Service Management Connector in Azure Monitor
description: This article provides information about how to connect your ITSM products/services with the IT Service Management Connector (ITSMC) in Azure Monitor to centrally monitor and manage the ITSM work items.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/12/2020

---

# Connect ITSM products/services with IT Service Management Connector
This article provides information about how to configure the connection between your ITSM product/service and the IT Service Management Connector (ITSMC) in Log Analytics to centrally manage your work items. For more information about ITSMC,  see [Overview](./itsmc-overview.md).

The following ITSM products/services are supported. Select the product to view detailed information about how to connect the product to ITSMC.

- [ServiceNow](./itsmc-connections-servicenow.md)
- [System Center Service Manager](./itsmc-connections-scsm.md)
- [Cherwell](./itsmc-connections-cherwell.md)
- [Provance](./itsmc-connections-provance.md)

> [!NOTE]
> We propose our Cherwell and Provance customers to use [Webhook action](./action-groups.md#webhook) to Cherwell and Provance endpoint as another solution to the integration.

## Next steps

* [ITSM Connector Overview](itsmc-overview.md)
* [Create ITSM work items from Azure alerts](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts)
* [Troubleshooting problems in ITSM Connector](./itsmc-resync-servicenow.md)