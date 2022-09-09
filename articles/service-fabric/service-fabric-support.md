---
title: Learn about Azure Service Fabric Support options 
description: Azure Service Fabric cluster versions supported and links to file support tickets
author: pkcsf
ms.topic: troubleshooting
ms.date: 8/24/2018
ms.author: pkc
---
# Azure Service Fabric support options

We have created a number of support request options to serve the needs of managing your Service Fabric clusters and application workloads, depending on the urgency of support needed and the severity of the issue.

## Create an Azure support request

To report issues related to your Service Fabric cluster running on Azure, open a support ticket [on the Azure portal](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)
or [Microsoft support portal](https://support.microsoft.com/oas/default.aspx?prid=16146).

Learn more about:

- [Azure support options](https://azure.microsoft.com/support/plans/?b=16.44).
- [Microsoft premier support](https://support.microsoft.com/premier).

> [!Note]
> Clusters running on a bronze reliability tier or Single Node Cluster will allow you to run test workloads only. If you experience issues with a cluster running on bronze reliability or Single Node Cluster, the Microsoft support team will assist you in mitigating the issue, but will not perform a Root Cause Analysis. For more information, please refer to [the reliability characteristics of the cluster](./service-fabric-cluster-capacity.md#reliability-characteristics-of-the-cluster).
>
> For more information about what is required for a production ready cluster, please refer to the [production readiness checklist](./service-fabric-production-readiness-checklist.md).

<a id="getlivesitesupportonprem"></a>

## Create a support request for standalone Service Fabric clusters

To report issues related to Service Fabric clusters running on-premises or on other clouds, you may open a ticket for professional support on the [Microsoft support portal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

Learn more about:

- [Professional Support from Microsoft for on-premises](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0).
- [Microsoft premier support](https://support.microsoft.com/en-us/premier).

## Post a question to Microsoft Q&A

Get answers to Service Fabric questions directly from Microsoft engineers, Azure Most Valuable Professionals (MVPs), and members of our expert community.

[Microsoft Q&A](https://docs.microsoft.com/answers/products/) is Azure's recommended source of community support.

If you can't find an answer to your problem by searching Microsoft Q&A, submit a new question. Be sure to post your question using the **azure-service-fabric** tag. Here are some Microsoft Q&A tips for writing [high-quality questions](https://docs.microsoft.com/answers/articles/24951/how-to-write-a-quality-question.html).

## Open a GitHub issue

Report Azure Service Fabric issues at the [Service Fabric GitHub](https://github.com/microsoft/service-fabric/issues). This repo is intended for reporting and tracking issues as well as making small feature requests related to Azure Service Fabric. **Do not use this medium to report live-site issues**.

## Check the StackOverflow forum

The `azure-service-fabric` tag on [StackOverflow][stackoverflow] is used for asking general questions about how the platform works and how you may use it to accomplish certain tasks.

## Submit feedback on Azure Feedback

The [Azure Feedback Forum for Service Fabric][uservoice-forum] is the best place for submitting significant product feature ideas. We review the most popular requests and factor them for our medium to long-term planning. We encourage you to rally support for your suggestions within the community.


> [!Note]
> **Service Fabric preview versions are not supported for production use.** Occasionally we make special preview releases containing significant feature changes for which we would like to survey early feedback. You should only use preview versions in isolated test environments that do not serve production workloads. Your production cluster should always be running a supported, stable, Service Fabric version. We don't offer a paid support option for these preview releases.
>
> A preview version always begins with a major and minor version number of 255. For example, if you see a Service Fabric version 255.255.5703.949, this version is in preview and is only intended to be used in test clusters. These preview releases are also announced on the [Service Fabric team blog](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) and will have details on the features included. Please use one of the options listed above to provide feedback.

## Next steps

[Supported Service Fabric versions](service-fabric-versions.md)

<!--references-->
[Microsoft Q&A question page]: /answers/topics/azure-service-fabric.html
[stackoverflow]: https://stackoverflow.com/questions/tagged/azure-service-fabric
[uservoice-forum]: https://feedback.azure.com/forums/293901-service-fabric
[acom-docs]: ./index.yml
[sample-repos]: /samples/browse/?products=azure