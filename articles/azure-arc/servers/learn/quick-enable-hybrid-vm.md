---
title: Connect hybrid machine with Azure Arc enabled servers (preview)
description: Learn how to connect and register your hybrid machine with Azure Arc enabled servers (preview).
ms.topic: quickstart
ms.date: 08/12/2020
---

# Quickstart: Connect hybrid machine with Azure Arc enabled servers (preview)

[Azure Arc enabled servers](../overview.md) (preview) enables you to manage and govern your Windows and Linux machines hosted across on-premises, edge, and multicloud environments. In this quickstart, you'll deploy and configure the Connected Machine agent on your Windows or Linux machine hosted outside of Azure for management by Arc enabled servers (preview).

## Prerequisites

* If you don't have an Azure subscription, create a [free account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) before you begin.

* Deploying the Arc enabled servers (preview) Hybrid Connected Machine agent requires that you have administrator permissions on the machine to install and configure the agent. On Linux, by using the root account, and on Windows, with an account that is a member of the Local Administrators group.

* Before you get started, be sure to review the agent [prerequisites](../agent-overview.md#prerequisites) and verify the following:

    * Your target machine is running a supported [operating system](../agent-overview.md#supported-operating-systems).

    * Your account is granted assignment to the [required Azure roles](../agent-overview.md#required-permissions).

    * If the machine connects through a firewall or proxy server to communicate over the Internet, make sure the URLs [listed](../agent-overview.md#networking-configuration) are not blocked.

    * Azure Arc enabled servers (preview) supports only the regions specified [here](../overview.md#supported-regions).

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

## Register Azure resource providers

Azure Arc enabled servers (preview) depends on the following Azure resource providers in your subscription in order to use this service:

* Microsoft.HybridCompute
* Microsoft.GuestConfiguration

Register them using the following commands:

```azurecli-interactive
az account set --subscription "{Your Subscription Name}"
az provider register --namespace 'Microsoft.HybridCompute'
az provider register --namespace 'Microsoft.GuestConfiguration'
```

## Generate installation script

The script to automate the download, installation, and establish the connection with Azure Arc, is available from the Azure portal. To complete the process, do the following:

1. Launch the Azure Arc service in the Azure portal by clicking **All services**, then searching for and selecting **Machines - Azure Arc**.

    :::image type="content" source="./media/quick-enable-hybrid-vm/search-machines.png" alt-text="Search for Arc enabled servers in All Services" border="false":::

1. On the **Machines - Azure Arc** page, select either **Add**, at the upper left, or the **Create machine - Azure Arc** option at the bottom of the middle pane.

1. On the **Select a method** page, select the **Add machines using interactive script** tile, and then select **Generate script**.

1. On the **Generate script** page, select the subscription and resource group where you want the machine to be managed within Azure. Select an Azure location where the machine metadata will be stored.

1. On the **Generate script** page, in the **Operating system** drop-down list, select the operating system that the script will be running on.

1. If the machine is communicating through a proxy server to connect to the internet, select **Next: Proxy Server**.

1. On the **Proxy server** tab, specify the proxy server IP address or the name and port number that the machine will use to communicate with the proxy server. Enter the value in the format `http://<proxyURL>:<proxyport>`.

1. Select **Review + generate**.

1. On the **Review + generate** tab, review the summary information, and then select **Download**. If you still need to make changes, select **Previous**.

## Install the agent using the script

### Windows agent

1. Log in to the server.

1. Open an elevated 64-bit PowerShell command prompt.

1. Change to the folder or share that you copied the script to, and execute it on the server by running the `./OnboardingScript.ps1` script.

### Linux agent

1. To install the Linux agent on the target machine that can directly communicate to Azure, run the following command:

    ```bash
    bash ~/Install_linux_azcmagent.sh
    ```

    * If the target machine communicates through a proxy server, run the following command:

        ```bash
        bash ~/Install_linux_azcmagent.sh --proxy "{proxy-url}:{proxy-port}"
        ```

## Verify the connection with Azure Arc

After you install the agent and configure it to connect to Azure Arc enabled servers (preview), go to the Azure portal to verify that the server has successfully connected. View your machine in the [Azure portal](https://aka.ms/hybridmachineportal).

:::image type="content" source="./media/quick-enable-hybrid-vm/enabled-machine.png" alt-text="A successful machine connection" border="false":::

## Next steps

Now that you've enabled your Linux or Windows hybrid machine and successfully connected to the service, you are ready to enable Azure Policy to understand compliance in Azure.

To learn how to identify Azure Arc enabled servers (preview) enabled machine that doesn't have the Log Analytics agent installed, continue to the tutorial:

> [!div class="nextstepaction"]
> [Create a policy assignment to identify non-compliant resources](tutorial-assign-policy-portal.md)
