---
title: Continuous deployment - Azure App Service | Microsoft Docs
description: Learn how to enable continuous deployment to Azure App Service.
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler

ms.assetid: 6adb5c84-6cf3-424e-a336-c554f23b4000
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/03/2018
ms.author: cephalin;dariagrigoriu
ms.custom: seodec18

---
# Continuous deployment to Azure App Service
This article shows you how to configure continuous deployment for [Azure App Service](overview.md). App Service enables continuous deployment from BitBucket, GitHub, and [Azure DevOps Services](https://www.visualstudio.com/team-services/) by pulling in the most recent updates from your existing repository in one of these services.

To find out how to configure continuous deployment manually from a cloud repository not listed by the Azure portal (such as [GitLab](https://gitlab.com/)), see
[Setting up continuous deployment using manual steps](https://github.com/projectkudu/kudu/wiki/Continuous-deployment#setting-up-continuous-deployment-using-manual-steps).

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

Publish your prepared repository to one of the supported services. For more information on publishing your project to these services, see [Create a repo (GitHub)], [Create a repo (BitBucket)], and [Get started with Azure DevOps Services].

## Deploy continuously from GitHub

To enable continuous deployment with GitHub, navigate to your App Service app page in the [Azure portal](https://portal.azure.com).

In the left menu, click **Deployment Center** > **GitHub** > **Authorize**. Follow the authorization prompts. 

![](media/app-service-continuous-deployment/github-choose-source.png)

You only need to authorize with GitHub once. If you're already authorized, just click **Continue**. You can change the authorized GitHub account by clicking **Change account**.

![](media/app-service-continuous-deployment/github-continue.png)

In the **Build provider** page, choose the build provider and click > **Continue**.

### Option 1: use App Service Kudu build server

In the **Configure** page, select the organization, repository, and branch from which you want to deploy continuously. When finished, click **Continue**.

To deploy from a repository in a GitHub organization, browse to GitHub and go to **Settings** > **Applications** > **Authorized OAuth Apps**. Then click "Azure App Service".

![Settings > Applications > Authorized OAuth Apps > Azure App Service](media/app-service-continuous-deployment/github-settings-navigation.png)

In the next page, grant App Service access to your organization's repositories by clicking the "Grant" button on the right-hand side.

![Click "Grant" to grant App Service access to the organization's repositories](media/app-service-continuous-deployment/grant-access.png)

Your organization should now show in the "Organization" list in the **Configure** page of the Deployment Center.

### Option 2: use Azure Pipelines (preview)

> [!NOTE]
> For App Service to create the necessary Azure Pipelines in your Azure DevOps Services organization, your Azure account must have the role of **Owner** in your Azure subscription.
>

In the **Configure** page, in the **Code** section, select the organization, repository, and branch from which you want to deploy continuously. When finished, click **Continue**.

In the **Configure** page, in the **Build** section, configure a new Azure DevOps Services organization or specify an existing organization. When finished, click **Continue**.

> [!NOTE]
> If you want to use an existing Azure DevOps Services organization that is not listed, you need to [link the Azure DevOps Services organization to your Azure subscription](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App).

In the **Test** page, choose whether to enable load tests, then click **Continue**.

Depending on the [pricing tier](https://azure.microsoft.com/pricing/details/app-service/plans/) of your App Service plan, you may also see a **Deploy to staging** page. Choose whether to [enable deployment slots](deploy-staging-slots.md), then click **Continue**.

### Finish configuration

In the **Summary** page, verify your options and click **Finish**.

When configuration completes, new commits in the selected repository are deployed continuously into your App Service app.

![](media/app-service-continuous-deployment/github-finished.png)

## Deploy continuously from BitBucket

To enable continuous deployment with BitBucket, navigate to your App Service app page in the [Azure portal](https://portal.azure.com).

In the left menu, click **Deployment Center** > **BitBucket** > **Authorize**. Follow the authorization prompts. 

![](media/app-service-continuous-deployment/bitbucket-choose-source.png)

You only need to authorize with BitBucket once. If you're already authorized, just click **Continue**. You can change the authorized BitBucket account by clicking **Change account**.

![](media/app-service-continuous-deployment/bitbucket-continue.png)

In the **Configure** page, select the repository and branch from which you want to deploy continuously. When finished, click **Continue**.

In the **Summary** page, verify your options and click **Finish**.

When configuration completes, new commits in the selected repository are deployed continuously into your App Service app.

## Deploy continuously from Azure Repos (DevOps Services)

To enable continuous deployment with from [Azure Repos](https://docs.microsoft.com/azure/devops/repos/index), navigate to your App Service app page in the [Azure portal](https://portal.azure.com).

In the left menu, click **Deployment Center** > **Azure Repos** > **Continue**. 

![](media/app-service-continuous-deployment/vsts-choose-source.png)

In the **Build provider** page, choose the build provider and click > **Continue**.

> [!NOTE]
> If you want to use an existing Azure DevOps Services organization that is not listed, you need to [link the Azure DevOps Services organization to your Azure subscription](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App).

### Option 1: use App Service Kudu build server

In the **Configure** page, select the Azure DevOps Services organization, project, repository, and branch from which you want to deploy continuously. When finished, click **Continue**.

### Option 2: use Azure DevOps Services continuous delivery

> [!NOTE]
> For App Service to create the necessary Azure Pipelines in your Azure DevOps Services organization, your Azure account must have the role of **Owner** in your Azure subscription.
>

In the **Configure** page, in the **Code** section, select the Azure DevOps Services organization, project, repository, and branch from which you want to deploy continuously. When finished, click **Continue**.

In the **Configure** page, in the **Build** section, specify the language framework that Azure DevOps Services should use to run the build tasks for your selected repository. When finished, click **Continue**.

In the **Test** page, choose whether to enable load tests, then click **Continue**.

Depending on the [pricing tier](https://azure.microsoft.com/pricing/details/app-service/plans/) of your App Service plan, you may also see a **Deploy to staging** page. Choose whether to [enable deployment slots](deploy-staging-slots.md), then click **Continue**. DevOps does not allow continous delivery to the production slot. This is by design to prevent accidental deployments to production. You should set up continuous delivery to a staging slot, verify the changes there, and swap the slots when you are ready.

### Finish configuration

In the **Summary** page, verify your options and click **Finish**.

When configuration completes, new commits in the selected repository are deployed continuously into your App Service app.

## Disable continuous deployment

To disable continuous deployment, navigate to your App Service app page in the [Azure portal](https://portal.azure.com).

In the left menu, click **Deployment Center** > **GitHub** or **Azure DevOps Services** or **BitBucket** > **Disconnect**.

![](media/app-service-continuous-deployment/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## Additional Resources

* [How to investigate common issues with continuous deployment](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment)
* [How to use PowerShell for Azure]
* [Git documentation]
* [Project Kudu](https://github.com/projectkudu/kudu/wiki)
* [Use Azure to automatically generate a CI/CD pipeline to deploy an ASP.NET 4 app](https://www.visualstudio.com/docs/build/get-started/aspnet-4-ci-cd-azure-automatic)

[Azure portal]: https://portal.azure.com
[Azure DevOps portal]: https://azure.microsoft.com/services/devops/
[Installing Git]: https://git-scm.com/book/en/Getting-Started-Installing-Git
[How to use PowerShell for Azure]: /powershell/azureps-cmdlets-docs
[Git Documentation]: https://git-scm.com/documentation

[Create a repo (GitHub)]: https://help.github.com/articles/create-a-repo
[Create a repo (BitBucket)]: https://confluence.atlassian.com/get-started-with-bitbucket/create-a-repository-861178559.html
[Get started with Azure DevOps Services]: https://docs.microsoft.com/azure/devops/user-guide/devops-alm-overview
