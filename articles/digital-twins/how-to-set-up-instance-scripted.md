---
# Mandatory fields.
title: Set up an instance and authentication (scripted)
titleSuffix: Azure Digital Twins
description: See how to set up an instance of the Azure Digital Twins service, by running an automated deployment script
author: baanders
ms.author: baanders # Microsoft employees only
ms.date: 7/23/2020
ms.topic: how-to
ms.service: digital-twins

# Optional fields. Don't forget to remove # if you need a field.
# ms.custom: can-be-multiple-comma-separated
# ms.reviewer: MSFT-alias-of-reviewer
# manager: MSFT-alias-of-manager-or-PM-counterpart
---

# Set up an Azure Digital Twins instance and authentication (scripted)

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

This article covers the steps to **set up a new Azure Digital Twins instance**, including creating the instance and setting up authentication. After completing this article, you will have an Azure Digital Twins instance ready to start programming against.

This version of this article completes these steps by running an [**automated deployment script** sample](/samples/azure-samples/digital-twins-samples/digital-twins-samples/) that streamlines the process. 
* To view the manual CLI steps that the script runs through behind the scenes, see the CLI version of this article: [*How-to: Set up an instance and authentication (CLI)*](how-to-set-up-instance-cli.md).
* To view the manual steps according to the Azure portal, see the portal version of this article: [*How-to: Set up an instance and authentication (portal)*](how-to-set-up-instance-portal.md).

[!INCLUDE [digital-twins-setup-steps.md](../../includes/digital-twins-setup-steps.md)]
[!INCLUDE [digital-twins-setup-permissions.md](../../includes/digital-twins-setup-permissions.md)]

## Prerequisites: Download the script

The sample script is written in PowerShell. It is part of the [**Azure Digital Twins end-to-end samples**](/samples/azure-samples/digital-twins-samples/digital-twins-samples/), which you can download to your machine by navigating to that sample link and selecting the *Download ZIP* button underneath the title.

This will download the sample project to your machine as _**Azure_Digital_Twins_end_to_end_samples.zip**_. Navigate to the folder on your machine and unzip it to extract the files.

In the unzipped folder, the deployment script is located at _Azure_Digital_Twins_end_to_end_samples > scripts > **deploy.ps1**_.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## Run the deployment script

This article uses an Azure Digital Twins code sample to deploy an Azure Digital Twins instance and the required authentication semi-automatically. It can also be used as a starting point for writing your own scripted interactions.

Here are the steps to run the deployment script in Cloud Shell.
1. Go to an [Azure Cloud Shell](https://shell.azure.com/) window in your browser. Sign in using this command:
    ```azurecli
    az login
    ```
    If the CLI can open your default browser, it will do so and load an Azure sign-in page. Otherwise, open a browser page at *https://aka.ms/devicelogin* and enter the authorization code displayed in your terminal.
 
2. In the Cloud Shell icon bar, make sure your Cloud Shell is set to run the PowerShell version.

    :::image type="content" source="media/how-to-set-up-instance/cloud-shell/cloud-shell-powershell.png" alt-text="Cloud Shell window showing selection of the PowerShell version":::

1. Select the "Upload/Download files" icon and choose "Upload".

    :::image type="content" source="media/how-to-set-up-instance/cloud-shell/cloud-shell-upload.png" alt-text="Cloud Shell window showing selection of the Upload icon":::

    Navigate to the _**deploy.ps1**_ file on your machine (in _Azure_Digital_Twins_end_to_end_samples > scripts > **deploy.ps1**_) and hit "Open." This will upload the file to Cloud Shell so that you can run it in the Cloud Shell window.

4. Run the script by sending the `./deploy.ps1` command in the Cloud Shell window. You can copy the command below (recall that to paste into Cloud Shell, you can use **Ctrl+Shift+V** on Windows and Linux, or **Cmd+Shift+V** on macOS. You can also use the right-click menu).

    ```azurecli
    ./deploy.ps1
    ```

    The script will create an Azure Digital Twins instance, and assign your Azure user the *Azure Digital Twins Owner (Preview)* role on the instance.

    As the script runs through the automated setup steps, you will be asked to pass in the following values:
    * For the instance: the *subscription ID* of your Azure subscription to use
    * For the instance: a *location* where you'd like to deploy the instance. To see what regions support Azure Digital Twins, visit [*Azure products available by region*](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins).
    * For the instance: a *resource group* name. You can use an existing resource group, or enter a new name of one to create.
    * For the instance: a *name* for your Azure Digital Twins instance. The name of the new instance must be unique within the region for your subscription (meaning that if your subscription has another Azure Digital Twins instance in the region that's already using the name you choose, you'll be asked to pick a different name).

Here is an excerpt of the output log from the script:

:::image type="content" source="media/how-to-set-up-instance/cloud-shell/deployment-script-output.png" alt-text="Cloud Shell window showing log of input and output through the run of the deploy script" lightbox="media/how-to-set-up-instance/cloud-shell/deployment-script-output.png":::

If the script completes successfully, the final printout will say `Deployment completed successfully`. Otherwise, address the error message, and re-run the script. It will bypass the steps that you've already completed and start requesting input again at the point where you left off.

> [!NOTE]
> The script currently assigns the required management role within Azure Digital Twins (*Azure Digital Twins Owner (Preview)*) to the same user that runs the script from Cloud Shell. If you need to assign this role to someone else who will be managing the instance, you can do this now via the Azure portal ([instructions](how-to-set-up-instance-portal.md#set-up-user-access-permissions)) or CLI ([instructions](how-to-set-up-instance-cli.md#set-up-user-access-permissions)).

>[!NOTE]
>There is currently a **known issue** with scripted setup, in which some users (especially users on personal [Microsoft accounts (MSAs)](https://account.microsoft.com/account)) may find the **role assignment to _Azure Digital Twins Owner (Preview)_ was not created**.
>
>You can verify the role assignment with the [*Verify user role assignment*](#verify-user-role-assignment) section later in this article, and—if needed—set up the role assignment manually using the [Azure portal](how-to-set-up-instance-portal.md#set-up-user-access-permissions) or [CLI](how-to-set-up-instance-cli.md#set-up-user-access-permissions).
>
>For more detail on this issue, see [*Troubleshooting: Known issues in Azure Digital Twins*](troubleshoot-known-issues.md#missing-role-assignment-after-scripted-setup).

## Verify success and collect important values

To verify the creation of your resources and permissions set up by the script, you can look at them in the [Azure portal](https://portal.azure.com) with the instructions below. If you are unable to verify the success of any step, retry the step. You can perform the steps individually using the [Azure portal](how-to-set-up-instance-portal.md) or [CLI](how-to-set-up-instance-cli.md) instructions.

This section also shows you how to find important values from the resources set up by the script that you may need as you continue working with your Azure Digital Twins instance. You should save these values in a safe place, or return to this section to find them later when you need them. If other users will be programming against the instance, you should share these values with them as well.

### Verify instance

To verify that your instance was created, go to the [Azure Digital Twins page](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.DigitalTwins%2FdigitalTwinsInstances) in the Azure portal. You can get to this page yourself by searching for *Azure Digital Twins* in the portal search bar.

This page lists all your Azure Digital Twins instances. Look for the name of your newly-created instance in the list.

If verification was unsuccessful, you can retry creating an instance using the [portal](how-to-set-up-instance-portal.md#create-the-azure-digital-twins-instance) or [CLI](how-to-set-up-instance-cli.md#create-the-azure-digital-twins-instance).

### Collect instance values

Select the name of your instance from the [Azure Digital Twins page](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.DigitalTwins%2FdigitalTwinsInstances) to open up the instance's *Overview* page. Note its *Name*, *Resource group*, and *Host name*. You may need these later to identify and connect to your instance.

:::image type="content" source="media/how-to-set-up-instance/portal/instance-important-values.png" alt-text="Highlighting the important values from the instance's Overview page":::

### Verify user role assignment

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

> [!NOTE]
> Recall that the script currently assigns this required role to the same user that runs the script from Cloud Shell. If you need to assign this role to someone else who will be managing the instance, you can do this now via the Azure portal ([instructions](how-to-set-up-instance-portal.md#set-up-user-access-permissions)) or CLI ([instructions](how-to-set-up-instance-cli.md#set-up-user-access-permissions)).

If verification was unsuccessful, you can also redo your own role assignment using the [portal](how-to-set-up-instance-portal.md#set-up-user-access-permissions) or [CLI](how-to-set-up-instance-cli.md#set-up-user-access-permissions).

## Next steps

Test out individual REST API calls on your instance using the Azure Digital Twins CLI commands: 
* [az dt reference](/cli/azure/ext/azure-iot/dt?preserve-view=true&view=azure-cli-latest)
* [*How-to: Use the Azure Digital Twins CLI*](how-to-use-cli.md)

Or, see how to connect a client application to your instance with authentication code:
* [*How-to: Write app authentication code*](how-to-authenticate-client.md)