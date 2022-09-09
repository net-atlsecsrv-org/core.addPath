---
title: Secure score in Azure Security Center
description: Description of Azure Security Center's secure score and its security controls 
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetd: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/10/2020
ms.author: memildin

---

# Secure score in Azure Security Center

## Introduction to secure score

Azure Security Center has two main goals: 

- to help you understand your current security situation
- to help you efficiently and effectively improve your security

The central feature in Security Center that enables you to achieve those goals is **secure score**.

Security Center continually assesses your resources, subscriptions, and organization for security issues. It then aggregates all the findings into a single score so that you can tell, at a glance, your current security situation: the higher the score, the lower the identified risk level.

The secure score is shown in the Azure portal pages as a percentage value, but the underlying values are also clearly presented:

:::image type="content" source="./media/secure-score-security-controls/single-secure-score-via-ui.png" alt-text="Overall secure score as shown in the portal":::

To increase your security, review Security Center's recommendations page for the outstanding actions necessary to raise your score. Each recommendation includes instructions to help you remediate the specific issue.

Recommendations are grouped into **security controls**. Each control is a logical group of related security recommendations, and reflects your vulnerable attack surfaces. Your score only improves when you remediate *all* of the recommendations for a single resource within a control. To see how well your organization is securing each individual attack surface, review the scores for each security control.

For more information, see [How your secure score is calculated](secure-score-security-controls.md#how-your-secure-score-is-calculated) below. 


## Access your secure score

You can find your overall secure score, as well as your score per subscription, through the Azure portal or programatically as described in the following sections:

- [Get your secure score from the portal](#get-your-secure-score-from-the-portal)
- [Get your secure score from the REST API](#get-your-secure-score-from-the-rest-api)
- [Get your secure score from Azure Resource Graph (ARG)](#get-your-secure-score-from-azure-resource-graph-arg)

### Get your secure score from the portal

Security Center displays your score prominently in the portal: it's the first main tile the Security Center overview page. Selecting this tile, takes you to the dedicated secure score page, where you'll see the score broken down by subscription. Select a single subscription to see the detailed list of prioritized recommendations and the potential impact that remediating them will have on the subscription's score.

To recap, your secure score is shown in the following locations in Security Center's portal pages.

- In a tile on Security Center's **Overview** (main dashboard):

    :::image type="content" source="./media/secure-score-security-controls/score-on-main-dashboard.png" alt-text="The secure score on Security Center's dashboard":::

- In the dedicated **Secure score** page:

    :::image type="content" source="./media/secure-score-security-controls/score-on-dedicated-dashboard.png" alt-text="The secure score on Security Center's secure score page":::

- At the top of the **Recommendations** page:

    :::image type="content" source="./media/secure-score-security-controls/score-on-recommendations-page.png" alt-text="The secure score on Security Center's recommendations page":::

### Get your secure score from the REST API

You can access your score via the secure score API (currently in preview). The API methods provide the flexibility to query the data and build your own reporting mechanism of your secure scores over time. For example, you can use the [Secure Scores API](/rest/api/securitycenter/securescores) to get the score for a specific subscription. In addition, you can use the [Secure Score Controls API](/rest/api/securitycenter/securescorecontrols) to list the security controls and the current score of your subscriptions.

![Retrieving a single secure score via the API](media/secure-score-security-controls/single-secure-score-via-api.png)

For examples of tools built on top of the secure score API, see [the secure score area of our GitHub community](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score). 

### Get your secure score from Azure Resource Graph (ARG)

Azure Resource Graph provides instant access to resource information across your cloud environments with robust filtering, grouping, and sorting capabilities. It's a quick and efficient way to query information across Azure subscriptions programmatically or from within the Azure portal. [Learn more about Azure Resource Graph](../governance/resource-graph/index.yml).

To access the secure score for multiple subscriptions with ARG:

1. From the Azure portal, open **Azure Resource Graph Explorer**.

    :::image type="content" source="./media/security-center-identity-access/opening-resource-graph-explorer.png" alt-text="Launching Azure Resource Graph Explorer** recommendation page" :::

1. Enter your Kusto query (using the examples below for guidance).

    - This query returns the subscription ID, the current score in points and as a percentage, and the maximum score for the subscription. 

        ```kusto
        SecurityResources 
        | where type == 'microsoft.security/securescores' 
        | extend current = properties.score.current, max = todouble(properties.score.max)
        | project subscriptionId, current, max, percentage = ((current / max)*100)
        ```

    - This query returns the status of all the security controls. For each control, you'll get the number of unhealthy resources, the current score, and the maximum score. 

        ```kusto
        SecurityResources 
        | where type == 'microsoft.security/securescores/securescorecontrols'
        | extend SecureControl = properties.displayName, unhealthy = properties.unhealthyResourceCount, currentscore = properties.score.current, maxscore = properties.score.max
        | project SecureControl , unhealthy, currentscore, maxscore
        ```

1. Select **Run query**.




## Tracking your secure score over time

If you're a Power BI user with a Pro account, you can use the **Secure Score Over Time** Power BI dashboard to track your secure score over time and investigate any changes.

> [!TIP]
> You can find this dashboard, as well as other tools for working programatically with secure score, in the dedicated area of the Azure Security Center community on GitHub: https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score

The dashboard contains the following two reports to help you analyze your security status:

- **Resources Summary** - provides summarized data regarding your resources’ health.
- **Secure Score Summary** - provides summarized data regarding your score progress. Use the “Secure score over time per subscription” chart to view changes in the score. If you notice a dramatic change in your score, check the “detected changes that may affect your secure score” table for possible changes that could have caused the change. This table presents deleted resources, newly deployed resources, or resources that their security status changed for one of the recommendations.

:::image type="content" source="./media/secure-score-security-controls/power-bi-secure-score-dashboard.png" alt-text="The optional Secure Score Over Time PowerBI dashboard for tracking your secure score over time and investigating changes":::





## How your secure score is calculated 

The contribution of each security control towards the overall secure score is shown clearly on the recommendations page.

[![The enhanced secure score introduces security controls](media/secure-score-security-controls/security-controls.png)](media/secure-score-security-controls/security-controls.png#lightbox)

To get all the possible points for a security control, all your resources must comply with all of the security recommendations within the security control. For example, Security Center has multiple recommendations regarding how to secure your management ports. You'll need to remediate them all to make a difference to your secure score.

For example, the security control called "Apply system updates" has a maximum score of six points, which you can see in the tooltip on the potential increase value of the control:

[![The security control "Apply system updates"](media/secure-score-security-controls/apply-system-updates-control.png)](media/secure-score-security-controls/apply-system-updates-control.png#lightbox)

The maximum score for this control, Apply system updates, is always 6. In this example, there are 50 resources. So we divide the max score by 50, and the result is that every resource contributes 0.12 points. 

* **Potential increase** (0.12 x 8 unhealthy resources = 0.96) - The remaining points available to you within the control. If you remediate all the recommendations in this control, your score will increase by 2% (in this case, 0.96 points rounded up to 1 point). 
* **Current score** (0.12 x 42 healthy resources = 5.04) - The current score for this control. Each control contributes towards the total score. In this example, the control is contributing 5.04 points to current secure total.
* **Max score** - The maximum number of points you can gain by completing all recommendations within a control. The maximum score for a control indicates the relative significance of that control. Use the max score values to triage the issues to work on first. 


### Calculations - understanding your score

|Metric|Formula and example|
|-|-|
|**Security control's current score**|<br>![Equation for calculating a security control's score](media/secure-score-security-controls/secure-score-equation-single-control.png)<br><br>Each individual security control contributes towards the Security Score. Each resource affected by a recommendation within the control, contributes towards the control's current score. The current score for each control is a measure of the status of the resources *within* the control.<br>![Tooltips showing the values used when calculating the security control's current score](media/secure-score-security-controls/security-control-scoring-tooltips.png)<br>In this example, the max score of 6 would be divided by 78 because that's the sum of the healthy and unhealthy resources.<br>6 / 78 = 0.0769<br>Multiplying that by the number of healthy resources (4) results in the current score:<br>0.0769 * 4 = **0.31**<br><br>|
|**Secure score**<br>Single subscription|<br>![Equation for calculating a subscription's secure score](media/secure-score-security-controls/secure-score-equation-single-sub.png)<br><br>![Single subscription secure score with all controls enabled](media/secure-score-security-controls/secure-score-example-single-sub.png)<br>In this example, there is a single subscription with all security controls available (a potential maximum score of 60 points). The score shows 28 points out of a possible 60 and the remaining 32 points are reflected in the "Potential score increase" figures of the security controls.<br>![List of controls and the potential score increase](media/secure-score-security-controls/secure-score-example-single-sub-recs.png)|
|**Secure score**<br>Multiple subscriptions|<br>![Equation for calculating the secure score for multiple subscriptions](media/secure-score-security-controls/secure-score-equation-multiple-subs.png)<br><br>When calculating the combined score for multiple subscriptions, Security Center includes a *weight* for each subscription. The relative weights for your subscriptions are determined by Security Center based on factors such as the number of resources.<br>The current score for each subscription is calculated in the same way as for a single subscription, but then the weight is applied as shown in the equation.<br>When viewing multiple subscriptions, secure score evaluates all resources within all enabled policies and groups their combined impact on each security control's maximum score.<br>![Secure score for multiple subscriptions with all controls enabled](media/secure-score-security-controls/secure-score-example-multiple-subs.png)<br>The combined score is **not** an average; rather it's the evaluated posture of the status of all resources across all subscriptions.<br>Here too, if you go to the recommendations page and add up the potential points available, you will find that it's the difference between the current score (24) and the maximum score available (60).|
||||

### Which recommendations are included in the secure score calculations?

Only built-in recommendations have an impact on the secure score.

Recommendations flagged as **Preview** aren't included in the calculations of your secure score. They should still be remediated wherever possible, so that when the preview period ends they'll contribute towards your score.

An example of a preview recommendation:

:::image type="content" source="./media/secure-score-security-controls/example-of-preview-recommendation.png" alt-text="Recommendation with the preview flag":::

## Improve your secure score

To improve your secure score, remediate security recommendations from your recommendations list. You can remediate each recommendation manually for each resource, or by using the **Quick Fix!** option (when available) to apply a remediation for a recommendation to a group of resources quickly. For more information, see [Remediate recommendations](security-center-remediate-recommendations.md).

Another way to improve your score and ensure your users don't create resources that negatively impact your score is to configure the Enforce and Deny options on the relevant recommendations. Learn more in [Prevent misconfigurations with Enforce/Deny recommendations](prevent-misconfigurations.md).

## Security controls and their recommendations

The table below lists the security controls in Azure Security Center. For each control, you can see the maximum number of points you can add to your secure score if you remediate *all* of the recommendations listed in the control, for *all* of your resources. 

The set of security recommendations provided with Security Center is tailored to the available resources in each organization’s environment. The recommendations can be further customized by [disabling policies](tutorial-security-policy.md#disable-security-policies-and-disable-recommendations) and [exempting specific resources from a recommendation](exempt-resource.md). 
 
We recommend every organization carefully review their assigned Azure Policy initiatives. 

> [!TIP]
> For details of reviewing and editing your initiatives, see [Working with security policies](tutorial-security-policy.md). 

Even though Security Center’s default security initiative is based on industry best practices and standards, there are scenarios in which the built-in recommendations listed below might not completely fit your organization. Consequently, it’ll sometimes be necessary to adjust the default initiative - without compromising security - to ensure it’s aligned with your organization’s own policies. industry standards, regulatory standards, and benchmarks you’re obligated to meet.<br><br>
<div class="foo">

<style type="text/css">
.tg  {border-collapse:collapse;border-spacing:0;}
.tg td{border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;
  overflow:hidden;padding:10px 5px;word-break:normal;}
.tg th{border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:18px;
  font-weight:normal;overflow:hidden;padding:10px 5px;word-break:normal;}
.tg .tg-cly1{text-align:left;vertical-align:middle}
.tg .tg-lboi{border-color:inherit;text-align:left;vertical-align:middle}
</style>
<table class="tg">
<thead>
  <tr>
    <th class="tg-cly1"><b>Security control, score, and description</b><br></th>
    <th class="tg-cly1"><b>Recommendations</b></th>
  </tr>
</thead>
<tbody>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Enable MFA (max score 10)</p></strong>If you only use a password to authenticate a user, it leaves an attack vector open. If the password is weak or has been exposed elsewhere, is it really the user signing in with the username and password?<br>With <a href="https://www.microsoft.com/security/business/identity/mfa">MFA</a> enabled, your accounts are more secure, and users can still authenticate to almost any application with single sign-on (SSO).</td>
    <td class="tg-lboi"; width=55%>- MFA should be enabled on accounts with owner permissions on your subscription<br>- MFA should be enabled accounts with write permissions on your subscription</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Secure management ports (max score 8)</p></strong>Brute force attacks target management ports to gain access to a VM. Since the ports don’t always need to be open, one mitigation strategy is to reduce exposure to the ports using just-in-time network access controls, network security groups, and virtual machine port management.<br>Since many IT organizations don't block SSH communications outbound from their network, attackers can create encrypted tunnels that allow RDP ports on infected systems to communicate back to the attacker command to control servers. Attackers can use the Windows Remote Management subsystem to move laterally across your environment and use stolen credentials to access other resources on a network.</td>
    <td class="tg-lboi"; width=55%>- Management ports of virtual machines should be protected with just-in-time network access control<br>- Virtual machines should be associated with a Network Security Group<br>- Management ports should be closed on your virtual machines</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Apply system updates (max score 6)</p></strong>System updates provide organizations with the ability to maintain operational efficiency, reduce security vulnerabilities, and provide a more stable environment for end users. Not applying updates leaves unpatched vulnerabilities and results in environments that are susceptible to attacks. These vulnerabilities can be exploited and lead to data loss, data exfiltration, ransomware, and resource abuse. To deploy system updates, you can use the <a href="/azure/automation/update-management/overview">Update Management solution to manage patches and updates</a> for your virtual machines. Update management is the process of controlling the deployment and maintenance of software releases.</td>
    <td class="tg-lboi"; width=55%>- Monitoring agent health issues should be resolved on your machines<br>- Monitoring agent should be installed on virtual machine scale sets<br>- Monitoring agent should be installed on your machines<br>- OS version should be updated for your cloud service roles<br>- System updates on virtual machine scale sets should be installed<br>- System updates should be installed on your machines<br>- Your machines should be restarted to apply system updates<br>- Kubernetes Services should be upgraded to a non-vulnerable Kubernetes version<br>- Monitoring agent should be installed on your virtual machines<br>- Log Analytics agent should be installed on your Windows-based Azure Arc machines (Preview)<br>- Log Analytics agent should be installed on your Linux-based Azure Arc machines (Preview)</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Remediate vulnerabilities (max score 6)</p></strong>A vulnerability is a weakness that a threat actor could leverage, to compromise the confidentiality, availability, or integrity of a resource. <a href="/windows/security/threat-protection/microsoft-defender-atp/next-gen-threat-and-vuln-mgt">Managing vulnerabilities</a> reduces organizational exposure, hardens endpoint surface area, increases organizational resilience, and reduces the attack surface of your resources. Threat and Vulnerability Management provides visibility into software and security misconfigurations and provide recommendations for mitigations.</td>
    <td class="tg-lboi"; width=55%>- Advanced data security should be enabled on SQL Database<br>- Vulnerabilities in Azure Container Registry images should be remediated<br>- Vulnerabilities on your SQL databases should be remediated<br>- Vulnerabilities should be remediated by a Vulnerability Assessment solution<br>- Vulnerability assessment should be enabled on SQL Managed Instance<br>- Vulnerability assessment should be enabled on your SQL servers<br>- Vulnerability assessment solution should be installed on your virtual machines<br>- Container images should be deployed from trusted registries only (preview)<br>- Azure Policy add-on for Kubernetes should be installed and enabled on your clusters (preview)</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Enable encryption at rest (max score 4)</p></strong><a href="/azure/security/fundamentals/encryption-atrest">Encryption at rest</a> provides data protection for stored data. Attacks against data at rest include attempts to gain physical access to the hardware on which the data is stored. Azures use symmetric encryption to encrypt and decrypt large amounts of data at rest. A symmetric encryption key is used to encrypt data as it is written to storage. That encryption key is also used to decrypt that data as it is readied for use in memory. Keys must be stored in a secure location with identity-based access control and audit policies. One such secure location is Azure Key Vault. If an attacker obtains the encrypted data but not the encryption keys, the attacker can't access the data without breaking the encryption.</td>
    <td class="tg-lboi"; width=55%>- Disk encryption should be applied on virtual machines<br>- Transparent Data Encryption on SQL Database should be enabled<br>- Automation account variables should be encrypted<br>- Service Fabric clusters should have the ClusterProtectionLevel property set to EncryptAndSign<br>- SQL server TDE protector should be encrypted with your own key</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Encrypt data in transit (max score 4)</p></strong>Data is “in transit” when it's transmitted between components, locations, or programs. Organizations that fail to protect data in transit are susceptible to man-in-the-middle attacks, eavesdropping, and session hijacking. SSL/TLS protocols should be used to exchange data and a VPN is recommended. When sending encrypted data between an Azure virtual machine and an on-premise location, over the internet, you can use a virtual network gateway such as <a href="/azure/vpn-gateway/vpn-gateway-about-vpngateways">Azure VPN Gateway</a> to send encrypted traffic.</td>
    <td class="tg-lboi"; width=55%>- API App should only be accessible over HTTPS<br>- Function App should only be accessible over HTTPS<br>- Only secure connections to your Redis Cache should be enabled<br>- Secure transfer to storage accounts should be enabled<br>- Web Application should only be accessible over HTTPS<br>- Private endpoint should be enabled for PostgreSQL servers<br>- Enforce SSL connection should be enabled for PostgreSQL database servers<br>- Enforce SSL connection should be enabled for MySQL database servers<br>- TLS should be updated to the latest version for your API app<br>- TLS should be updated to the latest version for your function app<br>- TLS should be updated to the latest version for your web app<br>- FTPS should be required in your API App<br>- FTPS should be required in your function App<br>- FTPS should be required in your web App</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Manage access and permissions (max score 4)</p></strong>A core part of a security program is ensuring your users have the necessary access to do their jobs but no more than that: the <a href="/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models">least privilege access model</a>.<br>Control access to your resources by creating role assignments with <a href="/azure/role-based-access-control/overview">Azure role-based access control (Azure RBAC)</a>. A role assignment consists of three elements:<br>- <strong>Security principal</strong>: the object the user is requesting access to<br>- <strong>Role definition</strong>: their permissions<br>- <strong>Scope</strong>: the set of resources to which the permissions apply</td>
    <td class="tg-lboi"; width=55%>- Deprecated accounts should be removed from your subscription (Preview)<br>- Deprecated accounts with owner permissions should be removed from your subscription (Preview)<br>- External accounts with owner permissions should be removed from your subscription (Preview)<br>- External accounts with write permissions should be removed from your subscription (Preview)<br>- There should be more than one owner assigned to your subscription<br>- Azure role-based access control (Azure RBAC) should be used on Kubernetes Services (Preview)<br>- Service Fabric clusters should only use Azure Active Directory for client authentication<br>- Service principals should be used to protect your subscriptions instead of Management Certificates<br>- Least privileged Linux capabilities should be enforced for containers (preview)<br>- Immutable (read-only) root filesystem should be enforced for containers (preview)<br>- Container with privilege escalation should be avoided (preview)<br>- Running containers as root user should be avoided (preview)<br>- Containers sharing sensitive host namespaces should be avoided (preview)<br>- Usage of pod HostPath volume mounts should be restricted to a known list (preview)<br>- Privileged containers should be avoided (preview)<br>- Azure Policy add-on for Kubernetes should be installed and enabled on your clusters (preview)<br>- Web apps should request an SSL certificate for all incoming requests<br>- Managed identity should be used in your API App<br>- Managed identity should be used in your function App<br>- Managed identity should be used in your web App</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Remediate security configurations (max score 4)</p></strong>Misconfigured IT assets have a higher risk of being attacked. Basic hardening actions are often forgotten when assets are being deployed and deadlines must be met. Security misconfigurations can be at any level in the infrastructure: from the operating systems and network appliances, to cloud resources.<br>Azure Security Center continually compares the configuration of your resources with requirements in industry standards, regulations, and benchmarks. When you've configured the relevant "compliance packages" (standards and baselines) that matter to your organization, any gaps will result in security recommendations that include the CCEID and an explanation of the potential security impact.<br>Commonly used packages are <a href="/azure/security/benchmarks/introduction">Azure Security Benchmark</a> and <a href="https://www.cisecurity.org/benchmark/azure/">CIS Microsoft Azure Foundations Benchmark version 1.1.0</a></td>
    <td class="tg-lboi"; width=55%>- Vulnerabilities in container security configurations should be remediated<br>- Vulnerabilities in security configuration on your machines should be remediated<br>- Vulnerabilities in security configuration on your virtual machine scale sets should be remediated<br>- Monitoring agent should be installed on your virtual machines<br>- Monitoring agent should be installed on your machines<br>- Log Analytics agent should be installed on your Windows-based Azure Arc machines (Preview)<br>- Log Analytics agent should be installed on your Linux-based Azure Arc machines (Preview)<br>- Monitoring agent should be installed on virtual machine scale sets<br>- Monitoring agent health issues should be resolved on your machines<br>- Overriding or disabling of containers AppArmor profile should be restricted (preview)<br>- Azure Policy add-on for Kubernetes should be installed and enabled on your clusters (preview)</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Restrict unauthorized network access (max score 4)</p></strong>Endpoints within an organization provide a direct connection from your virtual network to supported Azure services. Virtual machines in a subnet can communicate with all resources. To limit communication to and from resources within a subnet, create a network security group and associate it to the subnet. Organizations can limit and protect against unauthorized traffic by creating inbound and outbound rules.</td>
    <td class="tg-lboi"; width=55%>- IP forwarding on your virtual machine should be disabled<br>- Authorized IP ranges should be defined on Kubernetes Services (Preview)<br>- (DEPRECATED) Access to App Services should be restricted (Preview)<br>- (DEPRECATED) The rules for web applications on IaaS NSGs should be hardened<br>- Virtual machines should be associated with a Network Security Group<br>- CORS should not allow every resource to access your API App<br>- CORS should not allow every resource to access your Function App<br>- CORS should not allow every resource to access your Web Application<br>- Remote debugging should be turned off for API App<br>- Remote debugging should be turned off for Function App<br>- Remote debugging should be turned off for Web Application<br>- Access should be restricted for permissive Network Security Groups with Internet-facing VMs<br>- Network Security Group Rules for Internet facing virtual machines should be hardened<br>- Azure Policy add-on for Kubernetes should be installed and enabled on your clusters (preview)<br>- Containers should listen on allowed ports only (preview)<br>- Services should listen on allowed ports only (preview)<br>- Usage of host networking and ports should be restricted (preview)<br>- Virtual networks should be protected by Azure Firewall (preview)<br>- Private endpoint should be enabled for MariaDB servers<br>- Private endpoint should be enabled for MySQL servers<br>- Private endpoint should be enabled for PostgreSQL servers</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Apply adaptive application control (max score 3)</p></strong>Adaptive application control (AAC) is an intelligent, automated, end-to-end solution, which allows you to control which applications can run on your Azure and non-Azure machines. It also helps to harden your machines against malware.<br>Security Center uses machine learning to create a list of known-safe applications for a group of machines.<br>This innovative approach to approved application listing provides the security benefits without the management complexity.<br>AAC is particularly relevant for purpose-built servers that need to run a specific set of applications.</td>
    <td class="tg-lboi"; width=55%>- Adaptive Application Controls should be enabled on virtual machines<br>- Monitoring agent should be installed on your virtual machines<br>- Monitoring agent should be installed on your machines<br>- Log Analytics agent should be installed on your Windows-based Azure Arc machines (Preview)<br>- Log Analytics agent should be installed on your Linux-based Azure Arc machines (Preview)<br>- Monitoring agent health issues should be resolved on your machines</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Apply data classification (max score 2)</p></strong>Classifying your organization's data by sensitivity and business impact allows you to determine and assign value to the data, and provides the strategy and basis for governance.<br><a href="/azure/information-protection/what-is-information-protection">Azure Information Protection</a> can assist with data classification. It uses encryption, identity, and authorization policies to protect data and restrict data access. Some classifications that Microsoft uses are Non-business, Public, General, Confidential, and Highly Confidential.</td>
    <td class="tg-lboi"; width=55%>- Sensitive data in your SQL databases should be classified (Preview)</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Protect applications against DDoS attacks (max score 2)</p></strong>Distributed denial-of-service (DDoS) attacks overwhelm resources and render applications unusable. Use <a href="/azure/virtual-network/ddos-protection-overview">Azure DDoS Protection Standard</a> to defend your organization from the three main types of DDoS attacks:<br>- <strong>Volumetric attacks</strong> flood the network with legitimate traffic. DDoS Protection Standard mitigates these attacks by absorbing or scrubbing them automatically.<br>- <strong>Protocol attacks</strong> render a target inaccessible, by exploiting weaknesses in the layer 3 and layer 4 protocol stack. DDoS Protection Standard mitigates these attacks by blocking malicious traffic.<br>- <strong>Resource (application) layer attacks</strong> target web application packets. Defend against this type with a web application firewall and DDoS Protection Standard.</td>
    <td class="tg-lboi"; width=55%>- DDoS Protection Standard should be enabled<br>- Container CPU and memory limits should be enforced (preview)<br>- Azure Policy add-on for Kubernetes should be installed and enabled on your clusters (preview)</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Enable endpoint protection (max score 2)</p></strong>To ensure your endpoints are protected from malware, behavioral sensors collect and process data from your endpoints' operating systems and send this data to the private cloud for analysis. Security analytics leverage big-data, machine-learning, and other sources to recommend responses to threats. For example, <a href="/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection">Microsoft Defender ATP</a> uses threat intelligence to identify attack methods and generate security alerts.<br>Security Center supports the following endpoint protection solutions: Windows Defender, System Center Endpoint Protection, Trend Micro, Symantec v12.1.1.1100, McAfee v10 for Windows, McAfee v10 for Linux and Sophos v9 for Linux. If Security Center detects any of these solutions, the recommendation to install endpoint protection will no longer appear.</td>
    <td class="tg-lboi"; width=55%>- Endpoint protection health failures should be remediated on virtual machine scale sets<br>- Endpoint protection health issues should be resolved on your machines<br>- Endpoint protection solution should be installed on virtual machine scale sets<br>- Install endpoint protection solution on virtual machines<br>- Monitoring agent health issues should be resolved on your machines<br>- Monitoring agent should be installed on virtual machine scale sets<br>- Monitoring agent should be installed on your machines<br>- Monitoring agent should be installed on your virtual machines<br>- Log Analytics agent should be installed on your Windows-based Azure Arc machines (Preview)<br>- Log Analytics agent should be installed on your Linux-based Azure Arc machines (Preview)<br>- Install endpoint protection solution on your machines</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Enable auditing and logging (max score 1)</p></strong>Logging data provides insights into past problems, prevents potential ones, can improve application performance, and provides the ability to automate actions that would otherwise be manual.<br>- <strong>Control and management logs</strong> provide information about <a href="/azure/azure-resource-manager/management/overview">Azure Resource Manager</a> operations.<br>- <strong>Data plane logs</strong> provide information about events raised as part of Azure resource usage.<br>- <strong>Processed events</strong> provide information about analyzed events/alerts that have been processed.</td>
    <td class="tg-lboi"; width=55%>- Auditing on SQL server should be enabled<br>- Diagnostic logs in App Services should be enabled<br>- Diagnostic logs in Azure Data Lake Store should be enabled<br>- Diagnostic logs in Azure Stream Analytics should be enabled<br>- Diagnostic logs in Batch accounts should be enabled<br>- Diagnostic logs in Data Lake Analytics should be enabled<br>- Diagnostic logs in Event Hub should be enabled<br>- Diagnostic logs in IoT Hub should be enabled<br>- Diagnostic logs in Key Vault should be enabled<br>- Diagnostic logs in Logic Apps should be enabled<br>- Diagnostic logs in Search service should be enabled<br>- Diagnostic logs in Service Bus should be enabled<br>- Diagnostic logs in Virtual Machine Scale Sets should be enabled<br>- Metric alert rules should be configured on Batch accounts<br>- SQL Auditing settings should have Action-Groups configured to capture critical activities<br>- SQL servers should be configured with auditing retention days greater than 90 days.</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Enable advanced threat protection (max score 0)</p></strong>Azure Security Center's optional Azure Defender threat protection plans provide comprehensive defenses for your environment. When Security Center detects a threat in any area of your environment, it generates an alert. These alerts describe details of the affected resources, suggested remediation steps, and in some cases an option to trigger a logic app in response.<br>Each Azure Defender plan is a separate, optional offering which you can enable using the relevant recommendation in this security control.<br><a href="/azure/security-center/threat-protection">Learn more about threat protection in Security Center</a>.</td>
    <td class="tg-lboi"; width=55%>- Advanced data security should be enabled on Azure SQL Database servers<br>- Advanced data security should be enabled on SQL servers on machines<br>- Advanced threat protection should be enabled on Virtual Machines<br>- Advanced threat protection should be enabled on Azure App Service plans<br>- Advanced threat protection should be enabled on Azure Storage accounts<br>- Advanced threat protection should be enabled on Azure Kubernetes Service clusters<br>- Advanced threat protection should be enabled on Azure Container Registry registries<br>- Advanced threat protection should be enabled on Azure Key Vault vaults</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Implement security best practices (max score 0)</p></strong>Modern security practices “assume breach” of the network perimeter. For that reason, many of the best practices in this control focus on managing identities.<br>Losing keys and credentials is a common problem. <a href="/azure/key-vault/key-vault-overview">Azure Key Vault</a> protects keys and secrets by encrypting keys, .pfx files, and passwords.<br>Virtual private networks (VPNs) are a secure way to access your virtual machines. If VPNs aren't available, use complex passphrases and two-factor authentication such as <a href="/azure/active-directory/authentication/concept-mfa-howitworks">Azure AD Multi-Factor Authentication</a>. Two-factor authentication avoids the weaknesses inherent in relying only on usernames and passwords.<br>Using strong authentication and authorization platforms is another best practice. Using federated identities allows organizations to delegate management of authorized identities. This is also important when employees are terminated, and their access needs to be revoked.</td>
    <td class="tg-lboi"; width=55%>- A maximum of 3 owners should be designated for your subscription<br>- External accounts with read permissions should be removed from your subscription<br>- MFA should be enabled on accounts with read permissions on your subscription<br>- Access to storage accounts with firewall and virtual network configurations should be restricted<br>- All authorization rules except RootManageSharedAccessKey should be removed from Event Hub namespace<br>- An Azure Active Directory administrator should be provisioned for SQL servers<br>- Advanced data security should be enabled on your managed instances<br>- Authorization rules on the Event Hub instance should be defined<br>- Storage accounts should be migrated to new Azure Resource Manager resources<br>- Virtual machines should be migrated to new Azure Resource Manager resources<br>- Subnets should be associated with a Network Security Group<br>- [Preview] Windows exploit guard should be enabled <br>- [Preview] Guest configuration agent should be installed<br>- Non-internet-facing virtual machines should be protected with network security groups<br>- Azure Backup should be enabled for virtual machines<br>- Geo-redundant backup should be enabled for Azure Database for MariaDB<br>- Geo-redundant backup should be enabled for Azure Database for MySQL<br>- Geo-redundant backup should be enabled for Azure Database for PostgreSQL<br>- PHP should be updated to the latest version for your API app<br>- PHP should be updated to the latest version for your web app<br>- Java should be updated to the latest version for your API app<br>- Java should be updated to the latest version for your function app<br>- Java should be updated to the latest version for your web app<br>- Python should be updated to the latest version for your API app<br>- Python should be updated to the latest version for your function app<br>- Python should be updated to the latest version for your web app<br>- Audit retention for SQL servers should be set to at least 90 days</td>
  </tr>
</tbody>
</table>


</div>




## Secure score FAQ

### If I address only three out of four recommendations in a security control, will my secure score change?
No. It won't change until you remediate all of the recommendations for a single resource. To get the maximum score for a control, you must remediate all recommendations, for all resources.

### If a recommendation isn't applicable to me, and I disable it in the policy, will my security control be fulfilled and my secure score updated?
Yes. We recommend disabling recommendations when they're inapplicable in your environment. For instructions on how to disable a specific recommendation, see [Disable security policies](./tutorial-security-policy.md#disable-security-policies-and-disable-recommendations).

### If a security control offers me zero points towards my secure score, should I ignore it?
In some cases, you'll see a control max score greater than zero, but the impact is zero. When the incremental score for fixing resources is negligible, it's rounded to zero. Don't ignore these recommendations as they still bring security improvements. The only exception is the "Additional Best Practice" control. Remediating these recommendations won't increase your score, but it will enhance your overall security.

## Next steps

This article described the secure score and the security controls it introduces. For related material, see the following articles:

- [Learn about the different elements of a recommendation](security-center-recommendations.md)
- [Learn how to remediate recommendations](security-center-remediate-recommendations.md)
- [View the GitHub-based tools for working programmatically with secure score](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score)