---
title: Container security with Azure Security Center and Azure Defender
description: Learn about Azure Security Center's container security features
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 04/06/2021
ms.author: memildin

---

# Container security in Security Center

Azure Security Center is the Azure-native solution for securing your containers.

Security Center can protect the following container resource types:

| Resource type | Protections offered by Security Center |
|:--------------------:|-----------|
| ![Kubernetes service](./media/security-center-virtual-machine-recommendations/icon-kubernetes-service-rec.png)<br>**Kubernetes clusters** | Continuous assessment of your clusters to provide visibility into misconfigurations and guidelines to help you mitigate identified threats. Learn more about [environment hardening through security recommendations](#environment-hardening).<br><br>Threat protection for clusters and Linux nodes. Alerts for suspicious activities are provided by [Azure Defender for Kubernetes](defender-for-kubernetes-introduction.md). This Azure Defender plan defends your Kubernetes clusters whether they're hosted in Azure Kubernetes Service (AKS), on-premises, or on other cloud providers. clusters. <br>Learn more about [run-time protection for Kubernetes nodes and clusters](#run-time-protection-for-kubernetes-nodes-and-clusters).|
| ![Container host](./media/security-center-virtual-machine-recommendations/icon-container-host-rec.png)<br>**Container hosts**<br>(VMs  running Docker) | Continuous assessment of your Docker environments to provide visibility into misconfigurations and guidelines to help you  mitigate threats identified by the optional [Azure Defender for servers](defender-for-servers-introduction.md).<br>Learn more about [environment hardening through security recommendations](#environment-hardening).|
| ![Container registry](./media/security-center-virtual-machine-recommendations/icon-container-registry-rec.png)<br>**Azure Container Registry (ACR) registries** | Vulnerability assessment and management tools for the images in your Azure Resource Manager-based ACR registries with the optional [Azure Defender for container registries](defender-for-container-registries-introduction.md).<br>Learn more about [scanning your container images for vulnerabilities](#vulnerability-management---scanning-container-images). |
|||

This article describes how you can use Security Center, together with the optional Azure Defender plans for container registries, severs, and Kubernetes, to improve, monitor, and maintain the security of your containers and their apps.

You'll learn how Security Center helps with these core aspects of container security:

- [Vulnerability management - scanning container images](#vulnerability-management---scanning-container-images)
- [Environment hardening](#environment-hardening)
- [Run-time protection for Kubernetes nodes and clusters](#run-time-protection-for-kubernetes-nodes-and-clusters)

The following screenshot shows the asset inventory page and the various container resource types protected by Security Center.

:::image type="content" source="./media/container-security/inventory-container-resources.png" alt-text="Container-related resources in Security Center's asset inventory page" lightbox="./media/container-security/inventory-container-resources.png":::

## Vulnerability management - scanning container images

To monitor images in your Azure Resource Manager-based Azure container registries, enable [Azure Defender for container registries](defender-for-container-registries-introduction.md). Security Center scans any images pulled within the last 30 days, pushed to your registry, or imported. The integrated scanner is provided by the industry-leading vulnerability scanning vendor, Qualys.

When issues are found ??? by Qualys or Security Center ??? you'll get notified in the [Azure Defender dashboard](azure-defender-dashboard.md). For every vulnerability, Security Center provides actionable recommendations, along with a severity classification, and guidance for how to remediate the issue. For details of Security Center's recommendations for containers, see the [reference list of recommendations](recommendations-reference.md#recs-compute).

Security Center filters and classifies findings from the scanner. When an image is healthy, Security Center marks it as such. Security Center generates security recommendations only for images that have issues to be resolved. By only notifying when there are problems, Security Center reduces the potential for unwanted informational alerts.

## Environment hardening

### Continuous monitoring of your Docker configuration

Azure Security Center identifies unmanaged containers hosted on IaaS Linux VMs, or other Linux machines running Docker containers. Security Center continuously assesses the configurations of these containers. It then compares them with the [Center for Internet Security (CIS) Docker Benchmark](https://www.cisecurity.org/benchmark/docker/).

Security Center includes the entire ruleset of the CIS Docker Benchmark and alerts you if your containers don't satisfy any of the controls. When it finds misconfigurations, Security Center generates security recommendations. Use Security Center's **recommendations page** to view recommendations and remediate issues. The CIS benchmark checks don't run on AKS-managed instances or Databricks-managed VMs.

For details of the relevant Security Center recommendations that might appear for this feature, see the [compute section](recommendations-reference.md#recs-compute) of the recommendations reference table.

When you're exploring the security issues of a VM, Security Center provides additional information about the containers on the machine. Such information includes the Docker version and the number of images running on the host. 

To monitor unmanaged containers hosted on IaaS Linux VMs, enable the optional [Azure Defender for servers](defender-for-servers-introduction.md).


### Continuous monitoring of your Kubernetes clusters
Security Center works together with Azure Kubernetes Service (AKS), Microsoft's managed container orchestration service for developing, deploying, and managing containerized applications.

AKS provides security controls and visibility into the security posture of your clusters. Security Center uses these features to constantly monitor the configuration of your AKS clusters and generate security recommendations aligned with industry standards.

This is a high-level diagram of the interaction between Azure Security Center, Azure Kubernetes Service, and Azure Policy:

:::image type="content" source="./media/defender-for-kubernetes-intro/kubernetes-service-security-center-integration-detailed.png" alt-text="High-level architecture of the interaction between Azure Security Center, Azure Kubernetes Service, and Azure Policy" lightbox="./media/defender-for-kubernetes-intro/kubernetes-service-security-center-integration-detailed.png":::

You can see that the items received and analyzed by Security Center include:

- audit logs from the API server
- raw security events from the Log Analytics agent

    > [!NOTE]
    > We don't currently support installation of the Log Analytics agent on Azure Kubernetes Service clusters that are running on virtual machine scale sets.

- cluster configuration information from the AKS cluster
- workload configuration from Azure Policy (via the **Azure Policy add-on for Kubernetes**)

For details of the relevant Security Center recommendations that might appear for this feature, see the [compute section](recommendations-reference.md#recs-compute) of the recommendations reference table.


###  Workload protection best-practices using Kubernetes admission control

For a bundle of recommendations to protect the workloads of your Kubernetes containers, install the  **Azure Policy add-on for Kubernetes**. You can also auto deploy this add-on as explained in [Enable auto provisioning of the Log Analytics agent and extensions](security-center-enable-data-collection.md#auto-provision-mma). When auto provisioning for the add-on is set to "on", the extension is enabled by default in all existing and future clusters (that meet the add-on installation requirements).

As explained in [this Azure Policy for Kubernetes page](../governance/policy/concepts/policy-for-kubernetes.md), the add-on extends the open-source [Gatekeeper???v3](https://github.com/open-policy-agent/gatekeeper)???admission controller webhook???for???[Open Policy Agent](https://www.openpolicyagent.org/). Kubernetes admission controllers are plugins that enforce how your clusters are used. The add-on registers as a web hook to Kubernetes admission control and makes it possible to apply at-scale enforcements and safeguards on your clusters in a centralized, consistent manner. 

With the add-on on your AKS cluster, every request to the Kubernetes API server will be monitored against the predefined set of best practices before being persisted to the cluster. You can then configure to **enforce** the best practices and mandate them for future workloads. 

For example, you can mandate that privileged containers shouldn't be created, and any future requests to do so will be blocked.

Learn more in [Protect your Kubernetes workloads](kubernetes-workload-protections.md).


## Run-time protection for Kubernetes nodes and clusters

[!INCLUDE [AKS in ASC threat protection](../../includes/security-center-azure-kubernetes-threat-protection.md)]



## Next steps

In this overview, you learned about the core elements of container security in Azure Security Center. For related material see:

- [Introduction to Azure Defender for Kubernetes](defender-for-kubernetes-introduction.md)
- [Introduction to Azure Defender for container registries](defender-for-container-registries-introduction.md)