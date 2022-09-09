---
title: Monitoring the security of your containers in Azure Security Center
description: Learn how to check the security posture of your containers from Azure Security Center
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 06/30/2020
ms.author: memildin
---


# Monitor the security of your containers

This page explains how to use the container security features described in the [Container Security article](container-security.md) in our concepts section.


## Scanning your ARM-based container registries for vulnerabilities 

1. To enable vulnerability scans of your Azure Container Registry images:

    1. Ensure you're on Azure Security Center's standard pricing tier.

    1. From the **Pricing & settings** page, enable the optional Container Registries bundle for your subscription:
    ![Enabling the Container Registries bundle](media/monitor-container-security/enabling-container-registries-bundle.png)

        Security Center is now ready to scan images that get pushed to the registry. 

        >[!NOTE]
        >This feature is charged per image.


1. To trigger the scan of an image, push it to your registry. 

    When the scan completes (typically after approximately 2 minutes, but can be up to 15 minutes), findings are available as Security Center recommendations like this:

1. To view the findings, go to the **Recommendations** page. If issues were found, you'll see the following recommendation:

    ![Recommendation to remediate issues ](media/monitor-container-security/acr-finding.png)

1. Select the recommendation. 
    The recommendation details page opens with additional information. This information includes the list of registries with vulnerable images ("Affected resources") and the remediation steps. 

1. Select a specific registry to see the repositories within it that have vulnerable repositories.

    ![Select a registry](media/monitor-container-security/acr-finding-select-registry.png)

    The registry details page opens with the list of affected repositories.

1. Select a specific repository to see the repositories within it that have vulnerable images.

    ![Select a repository](media/monitor-container-security/acr-finding-select-repository.png)

    The repository details page opens. It lists the vulnerable images together with an assessment of the severity of the findings.

1. Select a specific image to see the vulnerabilities.

    ![Select images](media/monitor-container-security/acr-finding-select-image.png)

    The list of findings for the selected image opens.

    ![List of findings](media/monitor-container-security/acr-findings.png)

1. To learn more about a finding, select the finding. 

    The findings details pane opens.

    [![Findings details pane](media/monitor-container-security/acr-finding-details-pane.png)](media/monitor-container-security/acr-finding-details-pane.png#lightbox)

    This pane includes a detailed description of the issue and links to external resources to help mitigate the threats.

1. Follow the steps in the remediation section of this pane.

1. When you have taken the steps required to remediate the security issue, replace the image in your registry:

    1. Push the updated image. This will trigger a scan. 
    
    1. Check the recommendations page for the recommendation "Vulnerabilities in Azure Container Registry images should be remediated". 
    
        If the recommendation still appears and the image you've handled still appears in the list of vulnerable images, check the remediation steps again.

    1. When you are sure the updated image has been pushed, scanned, and is no longer appearing in the recommendation, delete the “old” vulnerable image from your registry.


## Hardening your containers' Docker hosts

Security Center constantly monitors the configuration of your Docker hosts, and generates security recommendations that reflect industry standards.

To view Azure Security Center's security recommendations for your containers' Docker hosts:

1. From the Security Center navigation bar, open **Compute & apps** and select the **Containers** tab.

1. Optionally, filter the list of your container resources to container hosts hosts.

    ![Container resources filter](media/monitor-container-security/container-resources-filter.png)

1. From the list of your container host machines, select one to investigate further.

    ![Container host recommendations](media/monitor-container-security/container-resources-filtered-to-hosts.png)

    The **Container host information page** opens with details of the host and a list of recommendations.

1. From the recommendations list, select a recommendation to investigate further.

    ![Container host recommendation list](media/monitor-container-security/container-host-rec.png)

1. Optionally, read the description, information, threats, and remediation steps. 

1. Select **Take Action** at the bottom of the page.

    [![Take action button](media/monitor-container-security/host-security-take-action-button.png)](media/monitor-container-security/host-security-take-action.png#lightbox)

    Log Analytics opens with a custom operation ready to run. The default custom query includes a list of all failed rules that were assessed, along with guidelines to help you resolve the issues.

    [![Log Analytics action](media/monitor-container-security/log-analytics-for-action-small.png)](media/monitor-container-security/log-analytics-for-action.png#lightbox)

1. Tweak the query parameters if necessary.

1. When you're sure the command is appropriate and ready for your host, select **Run**.



## Next steps

In this article, you learned how to use Security Center's container security features. 

For other related material, see the following pages: 

- [Security Center recommendations for containers](recommendations-reference.md#recs-containers)
- [Alerts for AKS cluster level](alerts-reference.md#alerts-akscluster)
- [Alerts for Container host level](alerts-reference.md#alerts-containerhost)
