---
title: Troubleshooting the Start/Stop VMs during off hours solution
description: This article provides information on troubleshooting the Start/Stop VM solution.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
---
# Troubleshoot the Start/Stop VMs during off hours solution

This article provides information on troubleshooting issues that arise while working with Start/Stop VMs during off hours solution.

>[!NOTE]
>This article has been updated to use the new Azure PowerShell Az module. You can still use the AzureRM module, which will continue to receive bug fixes until at least December 2020. To learn more about the new Az module and AzureRM compatibility, see [Introducing the new Azure PowerShell Az module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). For Az module installation instructions on your Hybrid Runbook Worker, see [Install the Azure PowerShell Module](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). For your Automation account, you can update your modules to the latest version using [How to update Azure PowerShell modules in Azure Automation](../automation-update-azure-modules.md).

## <a name="deployment-failure"></a>Scenario: The Start/Stop VMs during off hours solution fails to properly deploy

### Issue

When deploying the [Start/Stop VMs during off hours solution](../automation-solution-vm-management.md), you receive one of the following errors:

```error
Account already exists in another resourcegroup in a subscription. ResourceGroupName: [MyResourceGroup].
```

```error
Resource 'StartStop_VM_Notification' was disallowed by policy. Policy identifiers: '[{\\\"policyAssignment\\\":{\\\"name\\\":\\\"[MyPolicyName]".
```

```error
The subscription is not registered to use namespace 'Microsoft.OperationsManagement'.
```

```error
The subscription is not registered to use namespace 'Microsoft.Insights'.
```

```error
The scope '/subscriptions/000000000000-0000-0000-0000-00000000/resourcegroups/<ResourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<WorkspaceName>/views/StartStopVMView' cannot perform write operation because following scope(s) are locked: '/subscriptions/000000000000-0000-0000-0000-00000000/resourceGroups/<ResourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<WorkspaceName>/views/StartStopVMView'. Please remove the lock and try again
```

```error
A parameter cannot be found that matches parameter name 'TagName'
```

```error
Start-AzureRmVm : Run Login-AzureRmAccount to login
```

### Cause

Deployments can fail because of one of the following reasons:

1. There is already an Automation account with the same name in the region selected.
2. A policy is disallowing the deployment of the Start/Stop VMs during off hours solution.
3. The `Microsoft.OperationsManagement`, `Microsoft.Insights`, or `Microsoft.Automation` resource type is not registered.
4. Your Log Analytics workspace is locked.
5. You have an outdated version of the AzureRM modules or the Start/Stop VMs during off hours solution.

### Resolution

Review the following fixes for potential solutions to your problem:

* Automation accounts need to be unique within an Azure region, even if they are in different resource groups. Check your existing Automation accounts in the target region.
* An existing policy prevents a resource that is required for the Start/Stop VMs during off hours solution to be deployed. Go to your policy assignments in the Azure portal and check whether you have a policy assignment that disallows the deployment of this resource. To learn more about this, see [RequestDisallowedByPolicy](../../azure-resource-manager/templates/error-policy-requestdisallowedbypolicy.md).
* To deploy the Start/Stop VMs solution, your subscription needs to be registered to the following Azure resource namespaces:

    * `Microsoft.OperationsManagement`
    * `Microsoft.Insights`
    * `Microsoft.Automation`

   See [Resolve errors for resource provider registration](../../azure-resource-manager/templates/error-register-resource-provider.md) to learn more about errors when registering providers.
* If you have a lock on your Log Analytics workspace, go to your workspace in the Azure portal and remove any locks on the resource.
* If the resolutions above do not solve your issue, follow the instructions under [Update the Solution](../automation-solution-vm-management.md#update-the-solution) to re-deploy the Start/Stop solution.

## <a name="all-vms-fail-to-startstop"></a>Scenario: All VMs fail to start or stop

### Issue

You've configured the Start/Stop VMs during off hours solution but it doesn't start or stop all the VMs.

### Cause

This error can be caused by one of the following reasons:

1. A schedule isn't configured correctly.
2. The Run As account might not be configured correctly.
3. A runbook might have run into errors.
4. The VMs might have been excluded.

### Resolution

Review the following list for potential solutions to your problem:

* Check that you've properly configured a schedule for the Start/Stop VMs during off hours solution. To learn how to configure a schedule, see the [Schedules](../automation-schedules.md) article.

* Check the [job streams](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) to look for any errors. Look for jobs from one of the following runbooks:

  * **AutoStop_CreateAlert_Child**
  * **AutoStop_CreateAlert_Parent**
  * **AutoStop_Disable**
  * **AutoStop_VM_Child**
  * **ScheduledStartStop_Base_Classic**
  * **ScheduledStartStop_Child_Classic**
  * **ScheduledStartStop_Child**
  * **ScheduledStartStop_Parent**
  * **SequencedStartStop_Parent**

* Verify that your [Run As account](../manage-runas-account.md) has proper permissions to the VMs you're trying to start or stop. To learn how to check the permissions on a resource, see [Quickstart: View roles assigned to a user using the Azure portal](../../role-based-access-control/check-access.md). You'll need to provide the application ID for the service principal used by the Run As account. You can retrieve this value by going to your Automation account in the Azure portal, selecting **Run as accounts** under **Account Settings**, and clicking the appropriate Run As account.

* VMs may not be started or stopped if they're being explicitly excluded. Excluded VMs are set in the `External_ExcludeVMNames` variable in the Automation account to which the solution is deployed. The following example shows how you can query that value with PowerShell.

  ```powershell-interactive
  Get-AzAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

## <a name="some-vms-fail-to-startstop"></a>Scenario: Some of my VMs fail to start or stop

### Issue

You've configured the Start/Stop VMs during off hours solution but it doesn't start or stop some of the VMs configured.

### Cause

This error can be caused by one of the following reasons:

1. In the sequence scenario, a tag might be missing or incorrect.
2. The VM might be excluded.
3. The Run As account might not have enough permissions on the VM.
4. The VM can have an issue that stopped it from starting or stopping.

### Resolution

Review the following list for potential solutions to your problem or places to look:

* When using the [sequence scenario](../automation-solution-vm-management.md) of the Start/Stop VMs during off hours solution, you must make sure that each VM you want to start or stop has the proper tag. Make sure the VMs that you want to start have the `sequencestart` tag and the VMs you want to stop have the `sequencestop` tag. Both tags require a positive integer value. You can use a query similar to the following example to look for all the VMs with the tags and their values.

  ```powershell-interactive
  Get-AzResource | ? {$_.Tags.Keys -contains "SequenceStart" -or $_.Tags.Keys -contains "SequenceStop"} | ft Name,Tags
  ```

* VMs might not be started or stopped if they're being explicitly excluded. Excluded VMs are set in the `External_ExcludeVMNames` variable in the Automation account to which the solution is deployed. The following example shows how you can query that value with PowerShell.

  ```powershell-interactive
  Get-AzAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

* To start and stop VMs, the Run As account for the Automation account must have appropriate permissions to the VM. To learn how to check the permissions on a resource, see [Quickstart: View roles assigned to a user using the Azure portal](../../role-based-access-control/check-access.md). You'll need to provide the application ID for the service principal used by the Run As account. You can retrieve this value by going to your Automation account in the Azure portal, selecting **Run as accounts** under **Account Settings** and clicking the appropriate Run As account.

* If the VM is having a problem starting or deallocating, there might be an issue on the VM itself. For example, an update is being applied when the VM is trying to shut down, a service hangs, and more. Navigate to your VM resource and check the **Activity Logs** to see if there are any errors in the logs. You might also attempt to log into the VM to see if there are any errors in the event logs. To learn more about troubleshooting your VM, see [Troubleshooting Azure virtual machines](../../virtual-machines/troubleshooting/index.yml)

* Check the [job streams](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) to look for any errors. In the portal, go to your Automation account and select **Jobs** under **Process Automation**.

## <a name="custom-runbook"></a>Scenario: My custom runbook fails to start or stop my VMs

### Issue

You've authored a custom runbook or downloaded one from the PowerShell Gallery and it isn't working properly.

### Cause

There can be many causes for the failure. Go to your Automation account in the Azure portal and select **Jobs** under **Process Automation**. From the Jobs page, look for jobs from your runbook to view any job failures.

### Resolution

It's recommended to:

* Use the [Start/Stop VMs during off hours solution](../automation-solution-vm-management.md) to start and stop VMs in Azure Automation. This solution is authored by Microsoft. 

* Be aware that Microsoft doesn't support custom runbooks. You might find a solution for your custom runbook from [runbook troubleshooting](runbooks.md). Check the [job streams](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) to look for any errors. 

## <a name="dont-start-stop-in-sequence"></a>Scenario: VMs don't start or stop in the correct sequence

### Issue

The VMs that you've configured in the solution don't start or stop in the correct sequence.

### Cause

This issue is caused by incorrect tagging on the VMs.

### Resolution

Take the following steps to ensure that the solution is configured correctly.

1. Ensure all VMs to be started or stopped have a `sequencestart` or `sequencestop` tag, depending on your situation. These tags need a positive integer as the value. VMs are processed in ascending order based on this value.
2. Make sure the resource groups for the VMs to be started or stopped are in the `External_Start_ResourceGroupNames` or `External_Stop_ResourceGroupNames` variables, depending on your situation.
3. Test your changes by executing the `SequencedStartStop_Parent` runbook with the `WHATIF` parameter set to True to preview your changes.
4. For more about using the solution to start and stop VMs in sequence, see [Start/Stop VMs in sequence](../automation-solution-vm-management.md).

## <a name="403"></a>Scenario: Start/Stop VMs during off hours job fails with 403 forbidden error

### Issue

You find jobs that failed with a `403 forbidden` error for Start/Stop VMs during off hours solution runbooks.

### Cause

This issue can be caused by an improperly configured or expired Run As account. It might also be because of inadequate permissions to the VM resources by the Run As account.

### Resolution

To verify that your Run As account is properly configured, go to your Automation account in the Azure portal and select **Run as accounts** under **Account Settings**. If a Run As account is improperly configured or expired, the status shows the condition.

If your Run As account is misconfigured, you should delete and recreate your Run As account. See [Manage Azure Automation Run As accounts](../manage-runas-account.md).

If the certificate is expired for your Run As account, see steps in [Self-signed certificate renewal](../manage-runas-account.md#cert-renewal) to renew the certificate.

If there are missing permissions, see [Quickstart: View roles assigned to a user using the Azure portal](../../role-based-access-control/check-access.md). You must provide the application ID for the service principal used by the Run As account. You can retrieve this value by going to your Automation account in the Azure portal, selecting **Run as accounts** under **Account Settings**, and clicking the appropriate Run As account.

## <a name="other"></a>Scenario: My problem isn't listed above

### Issue

You experience an issue or unexpected result when using the Start/Stop VMs during off hours solution that isn't listed on this page.

### Cause

Many times errors can be caused by using an old and outdated version of the solution.

> [!NOTE]
> The Start/Stop VMs during off hours solution has been tested with the Azure modules that are imported into your Automation account when you deploy the solution. The solution currently doesn't work with newer versions of the Azure module. This only affects the Automation account that you use to run the Start/Stop VMs during off hours solution. You can still use newer versions of the Azure module in your other Automation accounts, as described in [How to update Azure PowerShell modules in Azure Automation](../automation-update-azure-modules.md)

### Resolution

To resolve many errors, it's recommended to remove and [update the Start/Stop VMs during off hours solution](../automation-solution-vm-management.md#update-the-solution). Additionally, you can check the [job streams](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) to look for any errors. 

## Next steps

If you don't see your problem above or can't resolve your issue, try one of the following channels for additional support:

* Get answers from Azure experts through [Azure Forums](https://azure.microsoft.com/support/forums/).
* Connect with [@AzureSupport](https://twitter.com/azuresupport), the official Microsoft Azure account for improving customer experience by connecting the Azure community to the right resources: answers, support, and experts.
* File an Azure support incident. Go to the [Azure support site](https://azure.microsoft.com/support/options/) and select **Get Support**.