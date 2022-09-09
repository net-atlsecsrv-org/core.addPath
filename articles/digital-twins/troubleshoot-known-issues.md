---
title: Known issues - Azure Digital Twins
description: Get help recognizing and mitigating known issues with Azure Digital Twins.
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.service: digital-twins
ms.date: 07/14/2020
ms.custom: contperf-fy21q3
---

# Known issues in Azure Digital Twins

This article provides information about known issues associated with Azure Digital Twins.

## "400 Client Error: Bad Request" in Cloud Shell

**Issue description:** Commands in Cloud Shell running at *https://shell.azure.com* may intermittently fail with the error "400 Client Error: Bad Request for url: http://localhost:50342/oauth2/token", followed by full stack trace.

| Does this affect me? | Cause | Resolution |
| --- | --- | --- |
| In&nbsp;Azure&nbsp;Digital&nbsp;Twins, this affects the following command groups:<br><br>`az dt route`<br><br>`az dt model`<br><br>`az dt twin` | This is the result of a known issue in Cloud Shell: [*Getting token from Cloud Shell intermittently fails with 400 Client Error: Bad Request*](https://github.com/Azure/azure-cli/issues/11749).<br><br>This presents a problem with Azure Digital Twins instance auth tokens and the Cloud Shell's default [managed identity](../active-directory/managed-identities-azure-resources/overview.md) based authentication. <br><br>This doesn't affect Azure Digital Twins commands from the `az dt` or `az dt endpoint` command groups, because they use a different type of authentication token (based on Azure Resource Manager), which doesn't have an issue with the Cloud Shell's managed identity authentication. | One way to resolve this is to rerun the `az login` command in Cloud Shell and completing subsequent login steps. This will switch your session out of managed identity authentication, which avoids the root problem. After this, you should be able to rerun the command.<br><br>Alternatively, you can open the Cloud Shell pane in the Azure portal and complete your Cloud Shell work from there.<br>:::image type="content" source="media/troubleshoot-known-issues/portal-launch-icon.png" alt-text="Image of the Cloud Shell icon in the Azure portal icon bar" lightbox="media/troubleshoot-known-issues/portal-launch-icon.png":::<br><br>Finally, another solution is to [install the Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) on your machine so you can run Azure CLI commands locally. The local CLI does not experience this issue. |


## Missing role assignment after scripted setup

**Issue description:** Some users may experience issues with the role assignment portion of [*How-to: Set up an instance and authentication (scripted)*](how-to-set-up-instance-scripted.md). The script does not indicate failure, but the *Azure Digital Twins Data Owner* role is not successfully assigned to the user, and this issue will impact ability to create other resources down the road.

[!INCLUDE [digital-twins-role-rename-note.md](../../includes/digital-twins-role-rename-note.md)]

| Does this affect me? | Cause | Resolution |
| --- | --- | --- |
| To determine whether your role assignment was successfully set up after running the script, follow the instructions in the [*Verify user role assignment*](how-to-set-up-instance-scripted.md#verify-user-role-assignment) section of the setup article. If your user is not shown with this role, this issue affects you. | For users logged in with a personal [Microsoft account (MSA)](https://account.microsoft.com/account), your user's Principal ID that identifies you in commands like this may be different from your user's sign-in email, making it difficult for the script to discover and use to assign the role properly. | To resolve, you can set up your role assignment manually using either the [CLI instructions](how-to-set-up-instance-cli.md#set-up-user-access-permissions) or [Azure portal instructions](how-to-set-up-instance-portal.md#set-up-user-access-permissions). |

## Issue with interactive browser authentication

**Issue description:** When writing authentication code in your Azure Digital Twins applications using version **1.2.0** of the **[Azure.Identity](/dotnet/api/azure.identity?view=azure-dotnet&preserve-view=true) library**, you may experience issues with the [InteractiveBrowserCredential](/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet&preserve-view=true) method. This presents as an error response of "Azure.Identity.AuthenticationFailedException" when trying to authenticate in a browser window. The browser window may fail to start up completely, or appear to authenticate the user successfully, while the client application still fails with the error.

| Does this affect me? | Cause | Resolution |
| --- | --- | --- |
| The&nbsp;affected&nbsp;method&nbsp;is&nbsp;used&nbsp;in&nbsp;the&nbsp;following articles:<br><br>[*Tutorial: Code a client app*](tutorial-code.md)<br><br>[*How-to: Write app authentication code*](how-to-authenticate-client.md)<br><br>[*How-to: Use the Azure Digital Twins APIs and SDKs*](how-to-use-apis-sdks.md) | Some users have had this issue with version **1.2.0** of the `Azure.Identity` library. | To resolve, update your applications to use the [latest version](https://www.nuget.org/packages/Azure.Identity) of `Azure.Identity`. After updating the library version, the browser should load and authenticate as expected. |

## Next steps

Read more about security and permissions on Azure Digital Twins:
* [*Concepts: Security for Azure Digital Twins solutions*](concepts-security.md)