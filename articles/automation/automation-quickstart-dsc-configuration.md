---
title: Azure Quickstart - Configure a VM with DSC | Microsoft Docs
description: Configure a LAMP Stack on a Linux Virtual Machine with Desired State Configuration
services: automation
ms.subservice: dsc
keywords: dsc, configuration, automation
ms.date: 11/06/2018
ms.topic: quickstart
ms.custom: mvc
---

# Configure a virtual machine with Desired State Configuration

By enabling Azure Automation State Configuration, you can manage and monitor the configurations of your Windows and Linux servers using Desired State Configuration (DSC). Configurations that drift from a desired configuration can be identified or auto-corrected. This quickstart steps through onboarding a Linux VM and deploying a LAMP stack with DSC.

## Prerequisites

To complete this quickstart, you need:

* An Azure subscription. If you don't have an Azure subscription, [create a free account](https://azure.microsoft.com/free/).
* An Azure Automation account. For instructions on creating an Azure Automation Run As account, see [Azure Run As Account](automation-sec-configure-azure-runas-account.md).
* An Azure Resource Manager VM (not Classic) running Red Hat Enterprise Linux, CentOS, or Oracle Linux. For instructions on creating a VM, see
  [Create your first Linux virtual machine in the Azure portal](../virtual-machines/linux/quick-create-portal.md)

## Sign in to Azure
Sign in to Azure at https://portal.azure.com.

## Onboard a virtual machine

There are many different methods to onboard a machine and enable DSC. This quickstart covers onboarding through an Automation account. You can learn more about different methods to onboard your machines to State Configuration by reading the [onboarding](https://docs.microsoft.com/azure/automation/automation-dsc-onboarding) article.

1. In the left pane of the Azure portal, select **Automation accounts**. If it is not visible in the left pane, click **All services** and search in the resulting view.
1. In the list, select an Automation account.
1. In the left pane of the Automation account, select **State configuration (DSC)**.
2. Click **Add** to open the VM select page.
3. Find the virtual machine for which to enable DSC. You can use the search field and filter options to find a specific virtual machine.
4. Click on the virtual machine, and then click **Connect**
5. Select the DSC settings appropriate for the virtual machine. If you have already prepared a configuration, you can specify it as `Node Configuration Name`. You can set the [configuration mode](https://docs.microsoft.com/powershell/scripting/dsc/managing-nodes/metaConfig) to control the configuration behavior for the machine.
6. Click **OK**. While the DSC extension is deployed to the virtual machine, the status shows up as `Connecting`.

![Onboarding an Azure VM to DSC](./media/automation-quickstart-dsc-configuration/dsc-onboard-azure-vm.png)

## Import modules

Modules contain DSC resources and many can be found in the [PowerShell Gallery](https://www.powershellgallery.com). Any resources that are used in your configurations must be imported to the Automation account before compiling. For this tutorial, the module named **nx** is required.

1. In the left pane of the Automation account, select **Modules Gallery** under **Shared Resources**.
1. Search for the module to import by typing part of its name: `nx`.
1. Click on the module to import.
1. Click **Import**.

![Importing a DSC Module](./media/automation-quickstart-dsc-configuration/dsc-import-module-nx.png)

## Import the configuration

This quickstart uses a DSC configuration that configures Apache HTTP Server, MySQL, and PHP on the machine. See [DSC configurations](https://docs.microsoft.com/powershell/scripting/dsc/configurations/configurations).

In a text editor, type the following and save it locally as **AMPServer.ps1**.

```powershell-interactive
configuration LAMPServer {
   Import-DSCResource -module "nx"

   Node localhost {

        $requiredPackages = @("httpd","mod_ssl","php","php-mysql","mariadb","mariadb-server")
        $enabledServices = @("httpd","mariadb")

        #Ensure packages are installed
        ForEach ($package in $requiredPackages){
            nxPackage $Package{
                Ensure = "Present"
                Name = $Package
                PackageManager = "yum"
            }
        }

        #Ensure daemons are enabled
        ForEach ($service in $enabledServices){
            nxService $service{
                Enabled = $true
                Name = $service
                Controller = "SystemD"
                State = "running"
            }
        }
   }
}
```

To import the configuration:

1. In the left pane of the Automation account, select **State configuration (DSC)** and then click the **Configurations** tab.
2. Click **+ Add**.
3. Select the configuration file that you saved in the prior step.
4. Click **OK**.

## Compile a configuration

You must compile a DSC configuration to a node configuration (MOF document) before it can be assigned to a node. Compilation validates the configuration and allows for the input of parameter values. To learn more about compiling a configuration, see [Compiling configurations in State Configuration](automation-dsc-compile.md).

1. In the left pane of the Automation account, select **State Configuration (DSC)** and then click the **Configurations** tab.
1. Select the configuration `LAMPServer`.
1. From the menu options, select **Compile** and then click **Yes**.
1. In the Configuration view, you see a new compilation job queued. When the job has completed successfully, you are ready to move on to the next step. If there are any failures, you can click on the compilation job for details.

## Assign a node configuration

You can assign a compiled node configuration to a DSC node. Assignment applies the configuration to the machine and monitors or auto-corrects for any drift from that configuration.

1. In the left pane of the Automation account, select **State Configuration (DSC)** and then click the **Nodes** tab.
1. Select the node to which to assign a configuration.
1. Click **Assign Node Configuration**
1. Select the node configuration `LAMPServer.localhost` and click **OK**. State Configuration now assigns the compiled configuration to the node, and the node status changes to `Pending`. On the next periodic check, the node retrieves the configuration, applies it, and reports status. It can take up to 30 minutes for the node to retrieve the configuration, depending on the node settings. 
1. To force an immediate check, you can run the following command locally on the Linux virtual machine:
   `sudo /opt/microsoft/dsc/Scripts/PerformRequiredConfigurationChecks.py`

![Assigning a Node Configuration](./media/automation-quickstart-dsc-configuration/dsc-assign-node-configuration.png)

## View node status

You can view the status of all State Configuration-managed nodes in your Automation account. The information is displayed by choosing **State Configuration (DSC)** and clicking the **Nodes** tab. You can filter the display by status, node configuration, or name search.

![DSC Node Status](./media/automation-quickstart-dsc-configuration/dsc-node-status.png)

## Next steps

In this quickstart, you onboarded a Linux VM to State Configuration, created a configuration for a LAMP stack, and deployed the configuration to the VM. To learn how you can use Azure Automation State Configuration to enable continuous deployment, continue to the article:

> [!div class="nextstepaction"]
> [Continuous deployment to a VM using DSC and Chocolatey](./automation-dsc-cd-chocolatey.md)

* To learn more about PowerShell DSC, see [PowerShell Desired State Configuration overview](https://docs.microsoft.com/powershell/scripting/dsc/overview/overview).
* To learn more about managing State Configuration from PowerShell, see [Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.automation/).
* To learn how to forward DSC reports to Azure Monitor logs for reporting and alerting, see [Forwarding DSC reporting to Azure Monitor logs](automation-dsc-diagnostics.md).