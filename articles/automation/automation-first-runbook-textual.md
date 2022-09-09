---
title: My first PowerShell Workflow runbook in Azure Automation
description: Tutorial that walks you through the creation, testing, and publishing of a simple text runbook using PowerShell Workflow.
keywords: powershell workflow, powershell workflow examples, workflow powershell
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 09/24/2018
ms.topic: conceptual
manager: carmonm
---
# My first PowerShell Workflow runbook

> [!div class="op_single_selector"]
> * [Graphical](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [PowerShell Workflow](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)

This tutorial walks you through the creation of a [PowerShell Workflow runbook](automation-runbook-types.md#powershell-workflow-runbooks) in Azure Automation. You start with a simple runbook that you test and publish while explaining how to track the status of the runbook job. Then you modify the runbook to actually manage Azure resources, in this case starting an Azure virtual machine. Lastly you make the runbook more robust by adding runbook parameters.

## Prerequisites

To complete this tutorial, you need the following:

* Azure subscription. If you don't have one yet, you can [activate your MSDN subscriber benefits](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) or sign up for a [free account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Automation account](automation-offering-get-started.md) to hold the runbook and authenticate to Azure resources.  This account must have permission to start and stop the virtual machine.
* An Azure virtual machine. you stop and start this machine so it shouldn't be a production VM.

## Step 1 - Create new runbook

You start by creating a simple runbook that outputs the text *Hello World*.

1. In the Azure portal, open your Automation account.

   The Automation account page gives you a quick view of the resources in this account. You should already have some assets. Most of those assets are the modules that are automatically included in a new Automation account. You should also have the Credential asset that's mentioned in the [prerequisites](#prerequisites).

1. Click **Runbooks** under **Process Automation** to open the list of runbooks.
1. Create a new runbook by clicking on the **+ Add a runbook** button and then **Create a new runbook**.
1. Give the runbook the name *MyFirstRunbook-Workflow*.
1. In this case, you're going to create a [PowerShell Workflow runbook](automation-runbook-types.md#powershell-workflow-runbooks) so select **Powershell Workflow** for **Runbook type**.
1. Click **Create** to create the runbook and open the textual editor.

## Step 2 - Add code to the runbook

You can either type code directly into the runbook, or you can select cmdlets, runbooks, and assets from the Library control and have them added to the runbook with any related parameters. For this walkthrough, you type directly into the runbook.

1. Your runbook is currently empty with only the required *workflow* keyword, the name of your runbook, and the braces that encases the entire workflow.

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   }
   ```

1. Type *Write-Output "Hello World."* between the braces.

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   Write-Output "Hello World"
   }
   ```

1. Save the runbook by clicking **Save**.

## Step 3 - Test the runbook

Before you publish the runbook to make it available in production, you want to test it to make sure that it works properly. When you test a runbook, you run its **Draft** version and view its output interactively.

1. Click **Test pane** to open the Test pane.
1. Click **Start** to start the test. This option should be the only enabled option.
1. A [runbook job](automation-runbook-execution.md) is created and its status displayed.

   The job status will start as *Queued* indicating that it's waiting for a runbook worker in the cloud to come available. It moves to *Starting* when a worker claims the job, and then *Running* when the runbook actually starts running.

1. When the runbook job completes, its output is displayed. In your case, you should see *Hello World*.

   ![Hello World](media/automation-first-runbook-textual/test-output-hello-world.png)

1. Close the Test pane to return to the canvas.

## Step 4 - Publish and start the runbook

The runbook that you created is still in Draft mode. You must publish it before you can run it in production. When you publish a runbook, you overwrite the existing Published version with the Draft version. In your case, you don't have a Published version yet because you just created the runbook.

1. Click **Publish** to publish the runbook and then **Yes** when prompted.
1. If you scroll left to view the runbook in the **Runbooks** pane now, it shows an **Authoring Status** of **Published**.
1. Scroll back to the right to view the pane for **MyFirstRunbook-Workflow**.
   The options across the top allow us to start the runbook, schedule it to start at some time in the future, or create a [webhook](automation-webhooks.md) so it can be started through an HTTP call.
1. you just want to start the runbook so click **Start** and then **Yes** when prompted.

   ![Start runbook](media/automation-first-runbook-textual/automation-runbook-controls-start.png)

1. A job pane is opened for the runbook job that you created. you can close this pane, but in this case you leave it open so you can watch the job's progress.
1. The job status is shown in **Job Summary** and matches the statuses that you saw when you tested the runbook.

   ![Job Summary](media/automation-first-runbook-textual/job-pane-status-blade-jobsummary.png)

1. Once the runbook status shows *Completed*, click **Output**. The Output pane is opened, and you can see your *Hello World*.

   ![Job Summary](media/automation-first-runbook-textual/job-pane-status-blade-outputtile.png)

1. Close the Output pane.
1. Click **All Logs** to open the Streams pane for the runbook job. you should only see *Hello World* in the output stream, but this view can show other streams for a runbook job such as Verbose and Error if the runbook writes to them.

   ![Job Summary](media/automation-first-runbook-textual/job-pane-status-blade-alllogstile.png)

1. Close the Streams page and the Job page to return to the MyFirstRunbook page.
1. Click **Jobs** to open the Jobs page for this runbook. This page lists all of the jobs created by this runbook. you should only see one job listed since you only ran the job once.

   ![Jobs](media/automation-first-runbook-textual/runbook-control-job-tile.png)

1. You can click on this job to open the same Job page that you viewed when you started the runbook. This action allows you to go back in time and view the details of any job that was created for a particular runbook.

## Step 5 - Add authentication to manage Azure resources

You've tested and published your runbook, but so far it doesn't do anything useful. You want to have it manage Azure resources. It can't do that though unless you've authenticated using the credentials that are referred to in the [prerequisites](#prerequisites). You do that with the **Connect-AzAccount** cmdlet.

1. Open the textual editor by clicking **Edit** on the MyFirstRunbook-Workflow pane.
2. you don't need the **Write-Output** line anymore, so go ahead and delete it.
3. Position the cursor on a blank line between the braces.
4. Type or copy and paste the following code that will handle the authentication with your Automation Run As account:

   ```powershell-interactive
   # Ensures you do not inherit an AzureRMContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $AzureContext = Select-AzSubscription -SubscriptionId $Conn.SubscriptionID
   ```

   > [!IMPORTANT]
   > **Add-AzAccount** and **Login-AzAccount** are now  aliases for **Connect-AzAccount**. If the **Connect-AzAccount** cmdlet does not exist, you can use **Add-AzAccount** or **Login-AzAccount**, or you can [update your modules](automation-update-azure-modules.md) in your Automation Account to the latest versions.

> [!NOTE]
> You might need to [update your modules](automation-update-azure-modules.md) even though you have just created a new automation account.

1. Click **Test pane** so that you can test the runbook.
1. Click **Start** to start the test. Once it completes, you should receive output similar to the following, displaying basic information from your account. This action confirms that the credential is valid.

   ![Authenticate](media/automation-first-runbook-textual/runbook-auth-output.png)

## Step 6 - Add code to start a virtual machine

Now that your runbook is authenticating to your Azure subscription, you can manage resources. you add a command to start a virtual machine. You can pick any virtual machine in your Azure subscription, and for now you're hardcoding that name in the runbook. If you're managing resources across multiple subscriptions, you need to use the **-AzContext** parameter along with [Get-AzContext](/powershell/module/az.accounts/get-azcontext).

1. After *Connect-AzAccount*, type *Start-AzVM -Name 'VMName' -ResourceGroupName 'NameofResourceGroup'* providing the name and Resource Group name of the virtual machine to start.

   ```powershell-interactive
   workflow MyFirstRunbook-Workflow
   {
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $AzureContext = Select-AzSubscription -SubscriptionId $Conn.SubscriptionID

   Start-AzVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName' -AzContext $AzureContext
   }
   ```

1. Save the runbook and then click **Test pane** so that you can test it.
1. Click **Start** to start the test. Once it completes, check that the virtual machine was started.

## Step 7 - Add an input parameter to the runbook

your runbook currently starts the virtual machine that you hardcoded in the runbook, but it would be more useful if you could specify the virtual machine when the runbook is started. You add input parameters to the runbook to provide that functionality.

1. Add parameters for *VMName* and *ResourceGroupName* to the runbook and use these variables with the **Start-AzVM** cmdlet as in the example below.

   ```powershell-interactive
   workflow MyFirstRunbook-Workflow
   {
    Param(
     [string]$VMName,
     [string]$ResourceGroupName
    )
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   Start-AzVM -Name $VMName -ResourceGroupName $ResourceGroupName
   }
   ```

2. Save the runbook and open the Test pane. You can now provide values for the two input variables that are in the test.
3. Close the Test pane.
4. Click **Publish** to publish the new version of the runbook.
5. Stop the virtual machine that you started in the previous step.
6. Click **Start** to start the runbook. Type in the **VMName** and **ResourceGroupName** for the virtual machine that you're going to start.

   ![Start Runbook](media/automation-first-runbook-textual/automation-pass-params.png)

7. When the runbook completes, check that the virtual machine was started.

## Next steps

* For more information on PowerShell, including language reference and learning modules, refer to the [PowerShell Docs](https://docs.microsoft.com/powershell/scripting/overview).
* To get started with Graphical runbooks, see [My first graphical runbook](automation-first-runbook-graphical.md)
* To get started with PowerShell runbooks, see [My first PowerShell runbook](automation-first-runbook-textual-powershell.md)
* To learn more about runbook types, their advantages and limitations, see [Azure Automation runbook types](automation-runbook-types.md)
* For more information on PowerShell script support feature, see [Native PowerShell script support in Azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)
