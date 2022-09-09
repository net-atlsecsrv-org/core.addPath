---
title: Configure continuous deployment
description: Learn how to enable CI/CD to Azure App Service from GitHub, BitBucket, Azure Repos, or other repos. Select the build pipeline that fits your needs.
ms.assetid: 6adb5c84-6cf3-424e-a336-c554f23b4000
ms.topic: article
ms.date: 03/12/2021
ms.reviewer: dariac
ms.custom: seodec18
---

# Continuous deployment to Azure App Service

[Azure App Service](overview.md) enables continuous deployment from [GitHub](https://help.github.com/articles/create-a-repo), [BitBucket](https://confluence.atlassian.com/get-started-with-bitbucket/create-a-repository-861178559.html), and [Azure Repos](/azure/devops/repos/git/creatingrepo) repositories by pulling in the latest updates.

> [!NOTE]
> The **Development Center (Classic)** page in the Azure portal, which is the old deployment experience, will be deprecated in March, 2021. This change will not affect any existing deployment settings in your app, and you can continue to manage app deployment in the **Deployment Center** page.

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## Configure deployment source

1. In the [Azure portal](https://portal.azure.com), navigate to the management page for your App Service app.

1. From the left menu, click **Deployment Center** > **Settings**. 

1. In **Source**, select one of the CI/CD options.

    ![Shows how to choose deployment source in Deployment Center for Azure App Service](media/app-service-continuous-deployment/choose-source.png)

Choose the tab that corresponds to your selection for the steps.

# [GitHub](#tab/github)

4. [GitHub Actions](#how-the-github-actions-build-provider-works) is the default build provider. To change it, click **Change provider** > **App Service Build Service** (Kudu) > **OK**.

    > [!NOTE]
    > To use Azure Pipelines as the build provider for your App Service app, don't configure it in App Service. Instead, configure CI/CD directly from Azure Pipelines. The **Azure Pipelines** option just points you in the right direction.

1. If you're deploying from GitHub for the first time, click **Authorize** and follow the authorization prompts. If you want to deploy from a different user's repository, click **Change Account**.

1. Once you authorize your Azure account with GitHub, select the **Organization**, **Repository**, and **Branch** to configure CI/CD for.

1. When GitHub Actions is the chosen build provider, you can select the workflow file you want with the **Runtime stack** and **Version** dropdowns. Azure commits this workflow file into your selected GitHub repository to handle build and deploy tasks. To see the file before saving your changes, click **Preview file**.

    > [!NOTE]
    > App Service detects the [language stack setting](configure-common.md#configure-language-stack-settings) of your app and selects the most appropriate workflow template. If you choose a different template, it may deploy an app that doesn't run properly. For more information, see [How the GitHub Actions build provider works](#how-the-github-actions-build-provider-works).

1. Click **Save**.
   
    New commits in the selected repository and branch now deploy continuously into your App Service app. You can track the commits and deployments in the **Logs** tab.

# [BitBucket](#tab/bitbucket)

The BitBucket integration uses the App Service Build Services (Kudu) for build automation.

4. If you're deploying from BitBucket for the first time, click **Authorize** and follow the authorization prompts. If you want to deploy from a different user's repository, click **Change Account**.

1. For Bitbucket, select the Bitbucket **Team**, **Repository**, and **Branch** you want to deploy continuously.

1. Click **Save**.
   
    New commits in the selected repository and branch now deploy continuously into your App Service app. You can track the commits and deployments in the **Logs** tab.
   
# [Local Git](#tab/local)

See [Local Git deployment to Azure App Service](deploy-local-git.md).

# [Azure Repos](#tab/repos)

> [!NOTE]
> Azure Repos as a deployment source is support for Windows apps.
>

4. App Service Build Service (Kudu) is the default build provider.

    > [!NOTE]
    > To use Azure Pipelines as the build provider for your App Service app, don't configure it in App Service. Instead, configure CI/CD directly from Azure Pipelines. The **Azure Pipelines** option just points you in the right direction.

1. Select the **Azure DevOps Organization**, **Project**, **Repository**, and **Branch** you want to deploy continuously. 

    If your DevOps organization isn't listed, it's not yet linked to your Azure subscription. For more information, see [Create an Azure service connection](/azure/devops/pipelines/library/connect-to-azure).

-----

## Disable continuous deployment

1. In the [Azure portal](https://portal.azure.com), navigate to the management page for your App Service app.

1. From the left menu, click **Deployment Center** > **Settings** > **Disconnect**. 

    ![Shows how to disconnect your cloud folder sync with your App Service app in the Azure portal.](media/app-service-continuous-deployment/disable.png)

1. By default, the GitHub Actions workflow file is preserved in your repository, but it will continue to trigger deployment to your app. To delete it from your repository, select **Delete workflow file**.

1. Click **OK**.

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## How the GitHub Actions build provider works

The GitHub Actions build provider is an option for [CI/CD from GitHub](#configure-deployment-source), and does the following to set up CI/CD:

- Deposits a GitHub Actions workflow file into your GitHub repository to handle build and deploy tasks to App Service.
- Adds the publishing profile for your app as a GitHub secret. The workflow file uses this secret to authenticate with App Service.
- Captures information from the [workflow run logs](https://docs.github.com/actions/managing-workflow-runs/using-workflow-run-logs) and displays it in the **Logs** tab in your app's **Deployment Center**.

You can customize the GitHub Actions build provider in the following ways:

- Customize the workflow file after it's generated in your GitHub repository. For more information, see [Workflow syntax for GitHub Actions](https://docs.github.com/actions/reference/workflow-syntax-for-github-actions). Just make sure that the workflow deploys to App Service with the [azure/webapps-deploy](https://github.com/Azure/webapps-deploy) action.
- If the selected branch is protected, you can still preview the workflow file without saving the configuration, then manually add it into your repository. This method doesn't give you the log integration with the Azure portal.
- Instead of a publishing profile, deploy using a [service principal](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) in Azure Active Directory.

#### Authenticate with a service principal

This optional configuration replaces the default authentication with publishing profiles in the generated workflow file.

1. Generate a service principal with the [az ad sp create-for-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) command in the [Azure CLI](/cli/azure/). In the following example, replace *\<subscription-id>*, *\<group-name>*, and *\<app-name>* with your own values:

    ```azurecli-interactive
    az ad sp create-for-rbac --name "myAppDeployAuth" --role contributor \
                                --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> \
                                --sdk-auth
    ```
    
    > [!IMPORTANT]
    > For security, grant the minimum required access to the service principal. The scope in the previous example is limited to the specific App Service app and not the entire resource group.
    
1. Save the entire JSON output for the next step, including the top-level `{}`.

1. In [GitHub](https://github.com/), browse your repository, select **Settings > Secrets > Add a new secret**.

1. Paste the entire JSON output from the Azure CLI command into the secret's value field. Give the secret a name like `AZURE_CREDENTIALS`.

1. In the workflow file generated by the **Deployment Center**, revise the `azure/webapps-deploy` step with code like the following example (which is modified from a Node.js workflow file):

    ```yaml
    - name: Sign in to Azure 
    # Use the GitHub secret you added
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Deploy to Azure Web App
    # Remove publish-profile
    - uses: azure/webapps-deploy@v2
      with:
        app-name: '<app-name>'
        slot-name: 'production'
        package: .
    - name: Sign out of Azure
      run: |
        az logout
    ```
    
## Deploy from other repositories

For Windows apps, you can manually configure continuous deployment from a cloud Git or Mercurial repository that the portal doesn't directly support, such as [GitLab](https://gitlab.com/). You do it by choosing External Git in the **Source** dropdown. For more information, see [Set up continuous deployment using manual steps](https://github.com/projectkudu/kudu/wiki/Continuous-deployment#setting-up-continuous-deployment-using-manual-steps).

## More resources

* [Deploy from Azure Pipelines to Azure App Services](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps)
* [Investigate common issues with continuous deployment](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment)
* [Use Azure PowerShell](/powershell/azure/)
* [Project Kudu](https://github.com/projectkudu/kudu/wiki)
