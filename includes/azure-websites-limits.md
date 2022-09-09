---
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 03/04/2020
ms.author: jroth
---
| Resource | Free | Shared | Basic | Standard | Premium (v2) | Isolated </th> |
| --- | --- | --- | --- | --- | --- | --- |
| [Web, mobile, or API apps](https://azure.microsoft.com/services/app-service/) per [Azure App Service plan](../articles/app-service/overview-hosting-plans.md)<sup>1</sup> |10 |100 |Unlimited<sup>2</sup> |Unlimited<sup>2</sup> |Unlimited<sup>2</sup> |Unlimited<sup>2</sup>|
| [App Service plan](../articles/app-service/overview-hosting-plans.md) |10 per region |10 per resource group |100 per resource group |100 per resource group |100 per resource group |100 per resource group|
| Compute instance type |Shared |Shared |Dedicated<sup>3</sup> |Dedicated<sup>3</sup> |Dedicated<sup>3</sup></p> |Dedicated<sup>3</sup>|
| [Scale out](../articles/app-service/manage-scale-up.md) (maximum instances) |1 shared |1 shared |3 dedicated<sup>3</sup> |10 dedicated<sup>3</sup> |30 dedicated<sup>3</sup>|100 dedicated<sup>4</sup>|
| Storage<sup>5</sup> |1 GB<sup>5</sup> |1 GB<sup>5</sup> |10 GB<sup>5</sup> |50 GB<sup>5</sup> |250 GB<sup>5</sup></p> |1 TB<sup>5</sup>|
| CPU time (5 minutes)<sup>6</sup> |3 minutes |3 minutes |Unlimited, pay at standard [rates](https://azure.microsoft.com/pricing/details/app-service/)</a> |Unlimited, pay at standard [rates](https://azure.microsoft.com/pricing/details/app-service/)</a> |Unlimited, pay at standard [rates](https://azure.microsoft.com/pricing/details/app-service/)</a> |Unlimited, pay at standard [rates](https://azure.microsoft.com/pricing/details/app-service/)</a>|
| CPU time (day)<sup>6</sup> |60 minutes |240 minutes |Unlimited, pay at standard [rates](https://azure.microsoft.com/pricing/details/app-service/)</a> |Unlimited, pay at standard [rates](https://azure.microsoft.com/pricing/details/app-service/)</a> |Unlimited, pay at standard [rates](https://azure.microsoft.com/pricing/details/app-service/)</a> |Unlimited, pay at standard [rates](https://azure.microsoft.com/pricing/details/app-service/)</a> |
| Memory (1 hour) |1,024 MB per App Service plan |1,024 MB per app |N/A |N/A |N/A |N/A |
| Bandwidth |165 MB |Unlimited, [data transfer rates](https://azure.microsoft.com/pricing/details/data-transfers/) apply |Unlimited, [data transfer rates](https://azure.microsoft.com/pricing/details/data-transfers/) apply |Unlimited, [data transfer rates](https://azure.microsoft.com/pricing/details/data-transfers/) apply |Unlimited, [data transfer rates](https://azure.microsoft.com/pricing/details/data-transfers/) apply |Unlimited, [data transfer rates](https://azure.microsoft.com/pricing/details/data-transfers/) apply |
| Application architecture |32-bit |32-bit |32-bit/64-bit |32-bit/64-bit |32-bit/64-bit |32-bit/64-bit |
| Web sockets per instance<sup>7</sup> |5 |35 |350 |Unlimited |Unlimited |Unlimited |
| IP connections | 600 | 600 | Depends on instance size<sup>8</sup> | Depends on instance size<sup>8</sup> | Depends on instance size<sup>8</sup> | 16,000 |
| Concurrent [debugger connections](../articles/app-service/troubleshoot-dotnet-visual-studio.md) per application |1 |1 |1 |5 |5 |5 |
| App Service Certificates per subscription<sup>9</sup>| Not supported | Not supported |10 |10 |10 |10 |
| Custom domains per app</a> |0 (azurewebsites.net subdomain only)|500 |500 |500 |500 |500 |
| Custom domain [SSL support](../articles/app-service/configure-ssl-certificate.md) |Not supported, wildcard certificate for *.azurewebsites.net available by default|Not supported, wildcard certificate for *.azurewebsites.net available by default|Unlimited SNI SSL connections |Unlimited SNI SSL and 1 IP SSL connections included |Unlimited SNI SSL and 1 IP SSL connections included | Unlimited SNI SSL and 1 IP SSL connections included|
| Hybrid connections per plan | | | 5 | 25 | 200 | 200 |
| Integrated load balancer | |X |X |X |X |X<sup>10</sup> |
| [Always On](../articles/app-service/configure-common.md) | | |X |X |X |X |
| [Scheduled backups](../articles/app-service/manage-backup.md) | | | | Scheduled backups every 2 hours, a maximum of 12 backups per day (manual + scheduled) | Scheduled backups every hour, a maximum of 50 backups per day (manual + scheduled) | Scheduled backups every hour, a maximum of 50 backups per day (manual + scheduled) |
| [Autoscale](../articles/app-service/manage-scale-up.md) | | | |X |X |X |
| [WebJobs](../articles/app-service/webjobs-create.md)<sup>11</sup> |X |X |X |X |X |X |
| [Endpoint monitoring](../articles/app-service/web-sites-monitor.md) | | |X |X |X |X |
| [Staging slots](../articles/app-service/deploy-staging-slots.md) per app| | | |5 |20 |20 |
| SLA | |  |99.95%|99.95%|99.95%|99.95%|  

<sup>1</sup>Apps and storage quotas are per App Service plan unless noted otherwise.  
<sup>2</sup>The actual number of apps that you can host on these machines depends on the activity of the apps, the size of the machine instances, and the corresponding resource utilization.  
<sup>3</sup>Dedicated instances can be of different sizes. For more information, see [App Service pricing](https://azure.microsoft.com/pricing/details/app-service/).  
<sup>4</sup>More are allowed upon request.  
<sup>5</sup>The storage limit is the total content size across all apps in the same App service plan. The total content size of all apps across all App service plans in a single resource group and region cannot exceed 500GB.  
<sup>6</sup>These resources are constrained by physical resources on the dedicated instances (the instance size and the number of instances).  
<sup>7</sup>If you scale an app in the Basic tier to two instances, you have 350 concurrent connections for each of the two instances. For Standard tier and above, there are no theoretical limits to web sockets, but other factors can limit the number of web sockets. For example, maximum concurrent requests allowed (defined by `maxConcurrentRequestsPerCpu`) are: 7,500 per small VM, 15,000 per medium VM (7,500 x 2 cores), and 75,000 per large VM (18,750 x 4 cores).  
<sup>8</sup>The maximum IP connections are per instance and depend on the instance size: 1,920 per B1/S1/P1V2 instance, 3,968 per B2/S2/P2V2 instance, 8,064 per B3/S3/P3V2 instance.  
<sup>9</sup>The App Service Certificate quota limit per subscription can be increased via a support request to a maximum limit of 200.  
<sup>10</sup>App Service Isolated SKUs can be internally load balanced (ILB) with Azure Load Balancer, so there's no public connectivity from the internet. As a result, some features of an ILB Isolated App Service must be used from machines that have direct access to the ILB network endpoint.  
<sup>11</sup>Run custom executables and/or scripts on demand, on a schedule, or continuously as a background task within your App Service instance. Always On is required for continuous WebJobs execution. There's no predefined limit on the number of WebJobs that can run in an App Service instance. There are practical limits that depend on what the application code is trying to do.  
