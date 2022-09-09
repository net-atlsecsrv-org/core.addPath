---
title: Deploy a Linux Hybrid Runbook Worker in Azure Automation
description: This article tells how to install an Azure Automation Hybrid Runbook Worker to run runbooks on Linux-based machines in your local datacenter or cloud environment.
services: automation
ms.subservice: process-automation
ms.date: 11/23/2020
ms.topic: conceptual
---

# Deploy a Linux Hybrid Runbook Worker

You can use the user Hybrid Runbook Worker feature of Azure Automation to run runbooks directly on the Azure or non-Azure machine, including servers registered with [Azure Arc enabled servers](../azure-arc/servers/overview.md). From the machine or server that's hosting the role, you can run runbooks directly it and against resources in the environment to manage those local resources.

The Linux Hybrid Runbook Worker executes runbooks as a special user that can be elevated for running commands that need elevation. Azure Automation stores and manages runbooks and then delivers them to one or more designated machines. This article describes how to install the Hybrid Runbook Worker on a Linux machine, how to remove the worker, and how to remove a Hybrid Runbook Worker group.

After you successfully deploy a runbook worker, review [Run runbooks on a Hybrid Runbook Worker](automation-hrw-run-runbooks.md) to learn how to configure your runbooks to automate processes in your on-premises datacenter or other cloud environment.

## Prerequisites

Before you start, make sure that you have the following.

### A Log Analytics workspace

The Hybrid Runbook Worker role depends on an Azure Monitor Log Analytics workspace to install and configure the role. You can create it through [Azure Resource Manager](../azure-monitor/samples/resource-manager-workspace.md#create-a-log-analytics-workspace), through [PowerShell](../azure-monitor/scripts/powershell-sample-create-workspace.md?toc=/powershell/module/toc.json), or in the [Azure portal](../azure-monitor/learn/quick-create-workspace.md).

If you don't have an Azure Monitor Log Analytics workspace, review the [Azure Monitor Log design guidance](../azure-monitor/platform/design-logs-deployment.md) before you create the workspace.

### Log Analytics agent

The Hybrid Runbook Worker role requires the [Log Analytics agent](../azure-monitor/platform/log-analytics-agent.md) for the supported Linux operating system. For servers or machines hosted outside of Azure, you can install the Log Analytics agent using [Azure Arc enabled servers](../azure-arc/servers/overview.md).

>[!NOTE]
>After installing the Log Analytics agent for Linux, you should not change the permissions of the `sudoers.d` folder or its ownership. Sudo permission is required for the **nxautomation** account, which is the user context the Hybrid Runbook Worker runs under. The permissions should not be removed. Restricting this to certain folders or commands may result in a breaking change.
>

### Supported Linux operating systems

The Hybrid Runbook Worker feature supports the following distributions. All operating systems are assumed to be x64. x86 is not supported for any operating system.

* Amazon Linux 2012.09 to 2015.09
* CentOS Linux 5, 6, and 7
* Oracle Linux 5, 6, and 7
* Red Hat Enterprise Linux Server 5, 6, and 7
* Debian GNU/Linux 6, 7, and 8
* Ubuntu 12.04 LTS, 14.04 LTS, 16.04 LTS, and 18.04 LTS
* SUSE Linux Enterprise Server 12

### Minimum requirements

The minimum requirements for a Linux system and user Hybrid Runbook Worker are:

* Two cores
* 4 GB of RAM
* Port 443 (outbound)

| **Required package** | **Description** | **Minimum version**|
|--------------------- | --------------------- | -------------------|
|Glibc |GNU C Library| 2.5-12 |
|Openssl| OpenSSL Libraries | 1.0 (TLS 1.1 and TLS 1.2 are supported)|
|Curl | cURL web client | 7.15.5|
|Python-ctypes | Python 2.x is required |
|PAM | Pluggable Authentication Modules|
| **Optional package** | **Description** | **Minimum version**|
| PowerShell Core | To run PowerShell runbooks, PowerShell Core needs to be installed. See [Installing PowerShell Core on Linux](/powershell/scripting/install/installing-powershell-core-on-linux) to learn how to install it. | 6.0.0 |

### Adding a machine to a Hybrid Runbook Worker group

You can add the worker machine to a Hybrid Runbook Worker group in one of your Automation accounts. For machines hosting the system Hybrid Runbook worker managed by Update Management, they can be added to a Hybrid Runbook Worker group. But you must use the same Automation account for both Update Management and the Hybrid Runbook Worker group membership.

>[!NOTE]
>Azure Automation [Update Management](./update-management/overview.md) automatically installs the system Hybrid Runbook Worker on an Azure or non-Azure machine that's enabled for Update Management. However, this worker is not registered with any Hybrid Runbook Worker groups in your Automation account. To run your runbooks on those machines, you need to add them to a Hybrid Runbook Worker group. Follow step 4 under the section [Install a Linux Hybrid Runbook Worker](#install-a-linux-hybrid-runbook-worker) to add it to a group.

## Supported Linux hardening

The following are not yet supported:

* CIS

## Supported runbook types

Linux Hybrid Runbook Workers support a limited set of runbook types in Azure Automation, and they are described in the following table.

|Runbook type | Supported |
|-------------|-----------|
|Python 2 |Yes |
|PowerShell |Yes<sup>1</sup> |
|PowerShell Workflow |No |
|Graphical |No |
|Graphical PowerShell Workflow |No |

<sup>1</sup>PowerShell runbooks require PowerShell Core to be installed on the Linux machine. See [Installing PowerShell Core on Linux](/powershell/scripting/install/installing-powershell-core-on-linux) to learn how to install it.

### Network configuration

For networking requirements for the Hybrid Runbook Worker, see [Configuring your network](automation-hybrid-runbook-worker.md#network-planning).

## Install a Linux Hybrid Runbook Worker

To install and configure a Linux Hybrid Runbook Worker, perform the following steps.

1. Enable the Azure Automation solution in your Log Analytics workspace by running the following command in an elevated PowerShell command prompt or in Cloud Shell in the [Azure portal](https://portal.azure.com):

    ```powershell
    Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <resourceGroupName> -WorkspaceName <workspaceName> -IntelligencePackName "AzureAutomation" -Enabled $true
    ```

2. Deploy the Log Analytics agent to the target machine.

    * For Azure VMs, install the Log Analytics agent for Linux using the [virtual machine extension for Linux](../virtual-machines/extensions/oms-linux.md). The extension installs the Log Analytics agent on Azure virtual machines, and enrolls virtual machines into an existing Log Analytics workspace. You can use an Azure Resource Manager template, the Azure CLI, or Azure Policy to assign the [Deploy Log Analytics agent for *Linux* or *Windows* VMs](../governance/policy/samples/built-in-policies.md#monitoring) built-in policy. Once the agent is installed, the machine can be added to a Hybrid Runbook Worker group in your Automation account.

    * For non-Azure machines, you can install the Log Analytics agent using [Azure Arc enabled servers](../azure-arc/servers/overview.md). Arc enabled servers support deploying the Log Analytics agent using the following methods:

        - Using the VM extensions framework.

            This feature in Azure Arc enabled servers allows you to deploy the Log Analytics agent VM extension to a non-Azure Windows and/or Linux server. VM extensions can be managed using the following methods on your hybrid machines or servers managed by Arc enabled servers:

            - The [Azure portal](../azure-arc/servers/manage-vm-extensions-portal.md)
            - The [Azure CLI](../azure-arc/servers/manage-vm-extensions-cli.md)
            - [Azure PowerShell](../azure-arc/servers/manage-vm-extensions-powershell.md)
            - Azure [Resource Manager templates](../azure-arc/servers/manage-vm-extensions-template.md)

        - Using Azure Policy.

            Using this approach, you use the Azure Policy [Deploy Log Analytics agent to Linux or Windows Azure Arc machines](../governance/policy/samples/built-in-policies.md#monitoring) built-in policy to audit if the Arc enabled server has the Log Analytics agent installed. If the agent is not installed, it automatically deploys it using a remediation task. Alternatively, if you plan to monitor the machines with Azure Monitor for VMs, instead use the [Enable Azure Monitor for VMs](../governance/policy/samples/built-in-initiatives.md#monitoring) initiative to install and configure the Log Analytics agent.

        We recommend installing the Log Analytics agent for Windows or Linux using Azure Policy.

    > [!NOTE]
    > To manage the configuration of machines that support the Hybrid Runbook Worker role with Desired State Configuration (DSC), you must add the machines as DSC nodes.

    > [!NOTE]
    > The [nxautomation account](automation-runbook-execution.md#log-analytics-agent-for-linux) with the corresponding sudo permissions must be present during installation of the Linux Hybrid Worker. If you try to install the worker and the account is not present or doesn’t have the appropriate permissions, the installation fails.

3. Verify agent is reporting to workspace.

    The Log Analytics agent for Linux connects machines to an Azure Monitor Log Analytics workspace. When you install the agent on your machine and connect it to your workspace, it automatically downloads the components that are required for the Hybrid Runbook Worker.

    When the agent has successfully connected to your Log Analytics workspace after a few minutes, you can run the following query to verify that it is sending heartbeat data to the workspace.

    ```kusto
    Heartbeat
    | where Category == "Direct Agent"
    | where TimeGenerated > ago(30m)
    ```

    In the search results, you should see heartbeat records for the machine, indicating that it is connected and reporting to the service. By default, every agent forwards a heartbeat record to its assigned workspace.

4. Run the following command to add the machine to a Hybrid Runbook Worker group, specifying the values for the parameters `-w`, `-k`, `-g`, and `-e`.

    You can get the information required for parameters `-k` and `-e` from the **Keys** page in your Automation account. Select **Keys** under the **Account settings** section from the left-hand side of the page.

    ![Manage Keys page](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

    * For the `-e` parameter, copy the value for **URL**.

    * For the `-k` parameter, copy the value for **PRIMARY ACCESS KEY**.

    * For the `-g` parameter, specify the name of the Hybrid Runbook Worker group that the new Linux Hybrid Runbook worker should join. If this group already exists in the Automation account, the current machine is added to it. If this group doesn't exist, it is created with that name.

    * For the `-w` parameter, specify your Log Analytics workspace ID.

   ```bash
   sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/onboarding.py --register -w <logAnalyticsworkspaceId> -k <automationSharedKey> -g <hybridGroupName> -e <automationEndpoint>
   ```

5. Verify the deployment after the script is completed. From the **Hybrid Runbook Worker Groups** page in your Automation account, under the **User hybrid runbook workers group** tab, it shows the new or existing group and the number of members. If it's an existing group, the number of members is incremented. You can select the group from the list on the page, from the left-hand menu choose **Hybrid Workers**. On the **Hybrid Workers** page, you can see each member of the group listed.

    > [!NOTE]
    > If you are using the Log Analytics virtual machine extension for Linux for an Azure VM, we recommend setting `autoUpgradeMinorVersion` to `false` as auto-upgrading versions can cause issues with the Hybrid Runbook Worker. To learn how to upgrade the extension manually, see [Azure CLI deployment](../virtual-machines/extensions/oms-linux.md#azure-cli-deployment).

## Turn off signature validation

By default, Linux Hybrid Runbook Workers require signature validation. If you run an unsigned runbook against a worker, you see a `Signature validation failed` error. To turn off signature validation, run the following command. Replace the second parameter with your Log Analytics workspace ID.

 ```bash
 sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --false <logAnalyticsworkspaceId>
 ```

## <a name="remove-linux-hybrid-runbook-worker"></a>Remove the Hybrid Runbook Worker

You can use the command `ls /var/opt/microsoft/omsagent` on the Hybrid Runbook Worker to get the workspace ID. A folder is created that is named with the workspace ID.

```bash
sudo python onboarding.py --deregister --endpoint="<URL>" --key="<PrimaryAccessKey>" --groupname="Example" --workspaceid="<workspaceId>"
```

> [!NOTE]
> This script doesn't remove the Log Analytics agent for Linux from the machine. It only removes the functionality and configuration of the Hybrid Runbook Worker role.

## Remove a Hybrid Worker group

To remove a Hybrid Runbook Worker group of Linux machines, you use the same steps as for a Windows hybrid worker group. See [Remove a Hybrid Worker group](automation-windows-hrw-install.md#remove-a-hybrid-worker-group).

## Next steps

* To learn how to configure your runbooks to automate processes in your on-premises datacenter or other cloud environment, see [Run runbooks on a Hybrid Runbook Worker](automation-hrw-run-runbooks.md).

* To learn how to troubleshoot your Hybrid Runbook Workers, see [Troubleshoot Hybrid Runbook Worker issues - Linux](troubleshoot/hybrid-runbook-worker.md#linux).