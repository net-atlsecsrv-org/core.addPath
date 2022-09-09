---
title: Connect your non-Azure machines to Azure Security Center
description: Learn how to connect your non-Azure machines to Security Center
author: memildin
ms.author: memildin
ms.date: 10/01/2020
ms.topic: quickstart
ms.service: security-center
manager: rkarlin

---

#  Connect your non-Azure machines to Security Center

Security Center can monitor the security posture of your non-Azure computers but you need to first onboard these resources. 

You can add non-Azure computers in any of the following ways:

- Using Azure Arc (**recommended**)
- From Security Center's pages in the Azure portal (**Getting started** and **Inventory**)

Each of these is described below.

## Add non-Azure machines with Azure Arc

Using Azure Arc is the preferred way of adding your non-Azure machines to Azure Security Center.

A machine with Azure Arc enabled, becomes an Azure resource and appears in Security Center with recommendations like your other Azure resources. 

In addition, Azure Arc provides enhanced capabilities such as the option to enable policies on the machine, deploy the Log Analytics agent as an extension, simplify deployment with other Azure services, and more. For an overview of the benefits, see [Supported scenarios](../azure-arc/servers/overview.md#supported-scenarios).

**To deploy Azure Arc:**

- For one machine, follow the instructions in [Quickstart: Connect hybrid machine with Azure Arc enabled servers](../azure-arc/servers/learn/quick-enable-hybrid-vm.md).
- To deploy Azure Arc at scale, see [Connect hybrid machines to Azure at scale](../azure-arc/servers/onboard-service-principal.md)

Learn more about [Azure Arc](../azure-arc/servers/overview.md).

> [!TIP]
> If you're onboarding AWS machines, Security Center's connector for AWS transparently handles the Azure Arc deployment for you. Learn more in [Connect your AWS accounts to Azure Security Center](quickstart-onboard-aws.md).

## Add non-Azure machines from Security Center's portal pages

1. From Security Center's menu, open the **Getting started** page.
1. Select the **Get started** tab.

    :::image type="content" source="./media/security-center-onboarding/onboarding-get-started-tab.png" alt-text="Get Started tab in the Getting started page" lightbox="./media/security-center-onboarding/onboarding-get-started-tab.png":::

1. Below **Add non-Azure servers**, select **Configure** .

    > [!TIP]
    > You can also open add machines from the **inventory** page's **Add non-Azure servers** button.
    > 
    > :::image type="content" source="./media/security-center-onboarding/onboard-inventory.png" alt-text="Adding non-Azure machines from the asset inventory page":::

    A list of your Log Analytics workspaces is shown. The list includes, if applicable, the default workspace created for you by Security Center when automatic provisioning was enabled. Select this workspace or another workspace you want to use.

    You can add computers to an existing workspace or create a new workspace. 

1. Optionally, to create a new workspace, select  **Create new workspace**.

1. From the list of workspaces, select **Add Servers** for the relevant workspace.
    The **Agents management** page appears.

    From here, choose the relevant procedure below depending on the type of machines you're onboarding:

    - [Onboard your Azure Stack VMs](#onboard-your-azure-stack-vms)
    - [Onboard your Linux machines](#onboard-your-linux-machines)
    - [Onboard your Windows machines](#onboard-your-windows-machines)


### Onboard your Azure Stack VMs
To add Azure Stack VMs, you need the information on the **Agents management** page and to configure the **Azure Monitor, Update and Configuration Management** virtual machine extension on the virtual machines running on your Azure Stack.
1. From the **Agents management** page, copy the **Workspace ID** and **Primary Key** into Notepad.
1. Log into your **Azure Stack** portal and open the **Virtual machines** page.
1. Select the virtual machine that you want to protect with Security Center.
    >[!TIP]
    > For information on how to create a virtual machine on Azure Stack, see [this quickstart for Windows virtual machines](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-windows-portal) or [this quickstart for Linux virtual machines](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-linux-portal).
1. Select **Extensions**. The list of virtual machine extensions installed on this virtual machine is shown.
1. Select the **Add** tab. The **New Resource** menu shows the list of available virtual machine extensions.
1. Select the **Azure Monitor, Update and Configuration Management** extension and select **Create**. The **Install extension** configuration page opens.
    >[!NOTE]
    > If you do not see the **Azure Monitor, Update and Configuration Management** extension listed in your marketplace, please reach out to your Azure Stack operator to make it available.
1. On the **Install extension** configuration page, paste the **Workspace ID** and **Workspace Key (Primary Key)** that you copied into Notepad in the previous step.
1. When you complete the configuration, select **OK**. The extension's status will show as **Provisioning Succeeded**. It might take up to one hour for the virtual machine to appear in Security Center.


### Onboard your Linux machines
To add Linux machines, you need the WGET command from the **Agents management** page.
1. From the **Agents management** page, copy the **WGET** command into Notepad. Save this file to a location that can be accessible from your Linux computer.
1. On your Linux computer, open the file with the WGET command. Select the entire content and copy and paste it into a terminal console.
1. When the installation completes, you can validate that the *omsagent* is installed by running the *pgrep* command. The command will return the *omsagent* PID.
    The logs for the Agent can be found at: */var/opt/microsoft/omsagent/\<workspace id>/log/*
    It might take up to 30 minutes for the new Linux machine to appear in Security Center.


### Onboard your Windows machines
To add Windows machines, you need the information on the **Agents management** page and to download the appropriate agent file (32/64-bit).
1. Select the **Download Windows Agent** link applicable to your computer processor type to download the setup file.
1. From the **Agents management** page, copy the **Workspace ID** and **Primary Key** into Notepad.
1. Copy the downloaded setup file to the target computer and run it.
1. Follow the installation wizard (**Next**, **I Agree**, **Next**, **Next**).
    1. On the **Azure Log Analytics** page, paste the **Workspace ID** and **Workspace Key (Primary Key)** that you copied into Notepad.
    1. If the computer should report to a Log Analytics workspace in Azure Government cloud, select **Azure US Government** from the **Azure Cloud** dropdown list.
    1. If the computer needs to communicate through a proxy server to the Log Analytics service, select **Advanced** and provide the URL and port number of the proxy server.
    1. When you've entered all of the configuration settings, select **Next**.
    1. From the **Ready to Install** page, review the settings to be applied and select **Install**.
    1. On the **Configuration completed successfully** page, select **Finish**.

When complete, the **Log Analytics agent** appears in **Control Panel**. You can review your configuration there and verify that the agent is connected.

For further information on installing and configuring the agent, see [Connect Windows machines](../azure-monitor/platform/agent-windows.md#install-agent-using-setup-wizard).


## Verifying
Congratulations! Now you can see your Azure and non-Azure machines together in one place. Open the [asset inventory page](asset-inventory.md) and filter to the relevant resource types. These icons distinguish the types:

  ![ASC icon for non-Azure machine](./media/quick-onboard-linux-computer/security-center-monitoring-icon1.png) Non-Azure machine

  ![ASC icon for Azure machine](./media/quick-onboard-linux-computer/security-center-monitoring-icon2.png) Azure VM

  ![ASC icon for Azure Arc machine](./media/quick-onboard-linux-computer/arc-enabled-machine-icon.png) Azure Arc enabled machine

## Next steps

This page showed you how to add your non-Azure machines to Azure Security Center. To monitor their status, use the inventory tools as explained in the following page:

- [Explore and manage your resources with asset inventory and management tools](asset-inventory.md)