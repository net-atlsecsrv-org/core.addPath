---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018	
ms.author: jroth
---
The following table lists the limits that apply to Azure IoT Hub Device Provisioning Service resources.

| Resource | Limit |
| --- | --- |
| Maximum device provisioning services per Azure subscription | 10 |
| Maximum number of enrollments | 1,000,000 |
| Maximum number of registrations | 1,000,000 |
| Maximum number of enrollment groups | 100 |
| Maximum number of CAs | 25 |
| Maximum number of linked IoT hubs | 10 |
| Maximum size of message | 96 KB|


> [!NOTE]
> To increase the number of instances in your subscription, contact [Microsoft Support](https://azure.microsoft.com/support/options/).

> [!NOTE]
> To increase the number of enrollments and registrations on your provisioning service, contact [Microsoft Support](https://azure.microsoft.com/support/options/).

The Device Provisioning Service throttles requests when the following quotas are exceeded.

| Throttle | Per-unit value |
| --- | --- |
| Operations | 200/min/service |
| Device registrations | 200/min/service |
| Device polling operation | 5/10 sec/device |
