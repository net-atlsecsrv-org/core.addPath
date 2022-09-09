---
title: Continuous integration and continuous deployment to Azure IoT Edge devices (classic editor) - Azure IoT Edge
description: Set up continuous integration and continuous deployment using the classic editor - Azure IoT Edge with Azure DevOps, Azure Pipelines
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/26/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
---

# Continuous integration and continuous deployment to Azure IoT Edge devices (classic editor)

You can easily adopt DevOps with your Azure IoT Edge applications with the built-in Azure IoT Edge tasks in Azure Pipelines. This article demonstrates how you can use the continuous integration and continuous deployment features of Azure Pipelines to build, test, and deploy applications quickly and efficiently to your Azure IoT Edge using the classic editor. Alternatively, you can [use YAML](how-to-continuous-integration-continuous-deployment.md).

![Diagram - CI and CD branches for development and production](./media/how-to-continuous-integration-continuous-deployment-classic/model.png)

In this article, you learn how to use the built-in [Azure IoT Edge tasks](https://docs.microsoft.com/azure/devops/pipelines/tasks/build/azure-iot-edge) for Azure Pipelines to create build and release pipelines for your IoT Edge solution. Each Azure IoT Edge task added to your pipeline implements one of the following four actions:

 | Action | Description |
 | --- | --- |
 | Build module images | Takes your IoT Edge solution code and builds the container images.|
 | Push module images | Pushes module images to the container registry you specified. |
 | Generate deployment manifest | Takes a deployment.template.json file and the variables, then generates the final IoT Edge deployment manifest file. |
 | Deploy to IoT Edge devices | Creates IoT Edge deployments to one or more IoT Edge devices. |

Unless otherwise specified, the procedures in this article do not explore all the functionality available through task parameters. For more information, see the following:

* [Task version](https://docs.microsoft.com/azure/devops/pipelines/process/tasks?view=azure-devops&tabs=classic#task-versions)
* **Advanced** - If applicable, specify modules that you do not want built.
* [Control Options](https://docs.microsoft.com/azure/devops/pipelines/process/tasks?view=azure-devops&tabs=classic#task-control-options)
* [Environment Variables](https://docs.microsoft.com/azure/devops/pipelines/process/variables?view=azure-devops&tabs=yaml%2Cbatch#environment-variables)
* [Output variables](https://docs.microsoft.com/azure/devops/pipelines/process/variables?view=azure-devops&tabs=yaml%2Cbatch#use-output-variables-from-tasks)

## Prerequisites

* An Azure Repos repository. If you don't have one, you can [Create a new Git repo in your project](https://docs.microsoft.com/azure/devops/repos/git/create-new-repo?view=vsts&tabs=new-nav). For this article, we created a repository called **IoTEdgeRepo**.
* An IoT Edge solution committed and pushed to your repository. If you want to create a new sample solution for testing this article, follow the steps in [Develop and debug modules in Visual Studio Code](how-to-vs-code-develop-module.md) or [Develop and debug C# modules in Visual Studio](how-to-visual-studio-develop-csharp-module.md). For this article, we created a solution in our repository called **IoTEdgeSolution**, which has the code for a module named **filtermodule**.

   For this article, all you need is the solution folder created by the IoT Edge templates in either Visual Studio Code or Visual Studio. You don't need to build, push, deploy, or debug this code before proceeding. You'll set up those processes in Azure Pipelines.

   If you're creating a new solution, clone your repository locally first. Then, when you create the solution you can choose to create it directly in the repository folder. You can easily commit and push the new files from there.

* A container registry where you can push module images. You can use [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) or a third-party registry.
* An active Azure [IoT hub](../iot-hub/iot-hub-create-through-portal.md) with at least two IoT Edge devices for testing the separate test and production deployment stages. You can follow the quickstart articles to create an IoT Edge device on [Linux](quickstart-linux.md) or [Windows](quickstart.md)

## Create a build pipeline for continuous integration

In this section, you create a new build pipeline. You configure the pipeline to run automatically when you check in any changes to the sample IoT Edge solution and to publish build logs.

1. Sign in to your Azure DevOps organization (`https://dev.azure.com/{your organization}`) and open the project that contains your IoT Edge solution repository.

    ![Open your DevOps project](./media/how-to-continuous-integration-continuous-deployment-classic/initial-project.png)

2. From the left pane menu in your project, select **Pipelines**. Select **Create Pipeline** at the center of the page. Or, if you already have build pipelines, select the **New pipeline** button in the top right.

    ![Create a new build pipeline](./media/how-to-continuous-integration-continuous-deployment-classic/add-new-pipeline.png)

3. At the bottom of the **Where is your code?** page, select **Use the classic editor**. If you wish to use YAML to create your project's build pipelines, see the [YAML guide](how-to-continuous-integration-continuous-deployment.md).

    ![Select Use the classic editor](./media/how-to-continuous-integration-continuous-deployment-classic/create-without-yaml.png)

4. Follow the prompts to create your pipeline.

   1. Provide the source information for your new build pipeline. Select **Azure Repos Git** as the source, then select the project, repository, and branch where your IoT Edge solution code is located. Then, select **Continue**.

      ![Select your pipeline source](./media/how-to-continuous-integration-continuous-deployment-classic/pipeline-source.png)

   2. Select **Empty job** instead of a template.

      ![Start with an empty job for your build pipeline](./media/how-to-continuous-integration-continuous-deployment-classic/start-with-empty-build-job.png)

5. Once your pipeline is created, you are taken to the pipeline editor. Here, you can change the pipeline's name, agent pool, and agent specification.

   You can select a Microsoft-hosted pool, or a self-hosted pool that you manage.

   In your pipeline description, choose the correct agent specification based on your target platform:

   * If you would like to build your modules in platform amd64 for Linux containers, choose **ubuntu-16.04**

   * If you would like to build your modules in platform amd64 for Windows 1809 containers, you need to [set up self-hosted agent on Windows](https://docs.microsoft.com/azure/devops/pipelines/agents/v2-windows?view=vsts).

   * If you would like to build your modules in platform arm32v7 or arm64 for Linux containers, you need to [set up self-hosted agent on Linux](https://devblogs.microsoft.com/iotdev/setup-azure-iot-edge-ci-cd-pipeline-with-arm-agent).

    ![Configure build agent specification](./media/how-to-continuous-integration-continuous-deployment-classic/configure-env.png)

6. Your pipeline comes preconfigured with a job called **Agent job 1**. Select the plus sign (**+**) to add four tasks to the job: **Azure IoT Edge** twice, **Copy Files** once, and **Publish Build Artifacts** once. Search for each task and hover over the task's name to see the **Add** button.

   ![Add Azure IoT Edge task](./media/how-to-continuous-integration-continuous-deployment-classic/add-iot-edge-task.png)

   When all four tasks are added, your Agent job looks like the following example:

   ![Four tasks in the build pipeline](./media/how-to-continuous-integration-continuous-deployment-classic/add-tasks.png)

7. Select the first **Azure IoT Edge** task to edit it. This task builds all modules in the solution with the target platform that you specify. Edit the task with the following values:

    | Parameter | Description |
    | --- | --- |
    | Display name | The display name is automatically updated when the Action field changes. |
    | Action | Select **Build module images**. |
    | .template.json file | Select the ellipsis (**...**) and navigate to the **deployment.template.json** file in the repository that contains your IoT Edge solution. |
    | Default platform | Select the appropriate operating system for your modules based on your targeted IoT Edge device. |
    | Output variables | Provide a reference name to associate with the file path where your deployment.json file generates, such as **edge**. |

   These configurations use the image repository and tag that are defined in the `module.json` file to name and tag the module image. **Build module images** also helps replace the variables with the exact value you define in the `module.json` file. In Visual Studio or Visual Studio Code, you are specifying the actual value in a `.env` file. In Azure Pipelines, you set the value on the **Pipeline Variables** tab. Select the **Variables** tab on the pipeline editor menu and configure the name and value as following:

    * **ACR_ADDRESS**: Your Azure Container Registry **Login server** value. You can retrieve the Login server from the Overview page of your container registry in the Azure portal.

    If you have other variables in your project, you can specify the name and value on this tab. **Build module images** recognizes only variables that are in `${VARIABLE}` format. Make sure you use this format in your `**/module.json` files.

8. Select the second **Azure IoT Edge** task to edit it. This task pushes all module images to the container registry that you select.

    | Parameter | Description |
    | --- | --- |
    | Display name | The display name is automatically updated when the Action field changes. |
    | Action | Select **Push module images**. |
    | Container registry type | Use the default type: `Azure Container Registry`. |
    | Azure subscription | Choose your subscription. |
    | Azure Container Registry | Select the type of container registry that you use to store your module images. Depending on which registry type you choose, the form changes. If you choose **Azure Container Registry**, use the dropdown lists to select the Azure subscription and the name of your container registry. If you choose **Generic Container Registry**, select **New** to create a registry service connection. |
    | .template.json file | Select the ellipsis (**...**) and navigate to the **deployment.template.json** file in the repository that contains your IoT Edge solution. |
    | Default platform | Select the appropriate operating system for your modules based on your targeted IoT Edge device. |
    | Add registry credential to deployment manifest | Specify true to add the registry credential for pushing docker images to deployment manifest. |

   If you have multiple container registries to host your module images, you need to duplicate this task, select different container registry, and use **Bypass module(s)** in the **Advanced** settings to bypass the images that are not for this specific registry.

9. Select the **Copy Files** task to edit it. Use this task to copy files to the artifact staging directory.

    | Parameter | Description |
    | --- | --- |
    | Display name | Use the default name or customize |
    | Source folder | The folder with the files to be copied. |
    | Contents | Add two lines: `deployment.template.json` and `**/module.json`. These two files serve as inputs to generate the IoT Edge deployment manifest. |
    | Target Folder | Specify the variable `$(Build.ArtifactStagingDirectory)`. See [Build variables](https://docs.microsoft.com/azure/devops/pipelines/build/variables?view=azure-devops&tabs=yaml#build-variables) to learn about the description. |

10. Select the **Publish Build Artifacts** task to edit it. Provide artifact staging directory path to the task so that the path can be published to release pipeline.

    | Parameter | Description |
    | --- | --- |
    | Display name | Use the default name or customize. |
    | Path to publish | Specify the variable `$(Build.ArtifactStagingDirectory)`. See [Build variables](https://docs.microsoft.com/azure/devops/pipelines/build/variables?view=azure-devops&tabs=yaml#build-variables) to learn more. |
    | Artifact name | Use the default name: **drop** |
    | Artifact publish location | Use the default location: **Azure Pipelines** |

11. Open the **Triggers** tab and check the box to **Enable continuous integration**. Make sure the branch containing your code is included.

    ![Turn on continuous integration trigger](./media/how-to-continuous-integration-continuous-deployment-classic/configure-trigger.png)

12. Select **Save** from the **Save & queue** dropdown.

This pipeline is now configured to run automatically when you push new code to your repo. The last task, publishing the pipeline artifacts, triggers a release pipeline. Continue to the next section to build the release pipeline.

[!INCLUDE [iot-edge-create-release-pipeline-for-continuous-deployment](../../includes/iot-edge-create-release-pipeline-for-continuous-deployment.md)]

>[!NOTE]
>If you wish to use **layered deployments** in your pipeline, layered deployments are not yet supported in Azure IoT Edge tasks in Azure DevOps.
>
>However, you can use an [Azure CLI task in Azure DevOps](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-cli) to create your deployment as a layered deployment. For the **Inline Script** value, you can use the [az iot edge deployment create command](/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment):
>
>   ```azurecli-interactive
>   az iot edge deployment create -d {deployment_name} -n {hub_name} --content modules_content.json --layered true
>   ```

[!INCLUDE [iot-edge-verify-iot-edge-continuous-integration-continuous-deployment](../../includes/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment.md)]

## Next steps

* IoT Edge DevOps best practices sample in [Azure DevOps Starter for IoT Edge](how-to-devops-starter.md)
* Understand the IoT Edge deployment in [Understand IoT Edge deployments for single devices or at scale](module-deployment-monitoring.md)
* Walk through the steps to create, update, or delete a deployment in [Deploy and monitor IoT Edge modules at scale](how-to-deploy-at-scale.md).
