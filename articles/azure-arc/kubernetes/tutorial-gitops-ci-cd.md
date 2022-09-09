---
title: 'Tutorial: Implement CI/CD with GitOps using Azure Arc-enabled Kubernetes clusters'
description: This tutorial walks through setting up a CI/CD solution using GitOps with Azure Arc enabled Kubernetes clusters. For a conceptual take on this workflow, see the CI/CD Workflow using GitOps - Azure Arc enabled Kubernetes article.
author: tcare
ms.author: tcare
ms.service: azure-arc
ms.topic: tutorial
ms.date: 03/03/2021
ms.custom: template-tutorial
---
# Tutorial: Implement CI/CD with GitOps using Azure Arc-enabled Kubernetes clusters


In this tutorial, you'll set up a CI/CD solution using GitOps with Azure Arc enabled Kubernetes clusters. Using the sample Azure Vote app, you'll:

> [!div class="checklist"]
> * Create an Azure Arc enabled Kubernetes cluster.
> * Connect your application and GitOps repos to Azure Repos.
> * Import CI/CD pipelines.
> * Connect your Azure Container Registry (ACR) to Azure DevOps and Kubernetes.
> * Create environment variable groups.
> * Deploy the `dev` and `stage` environments.
> * Test the application environments.

If you don’t have an Azure subscription, create a [free account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) before you begin.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## Before you begin

This tutorial assumes familiarity with Azure DevOps, Azure Repos and Pipelines, and Azure CLI.

* Sign into [Azure DevOps Services](https://dev.azure.com/).
* Complete the [previous tutorial](https://docs.microsoft.com/azure/azure-arc/kubernetes/tutorial-use-gitops-connected-cluster) to learn how to deploy GitOps for your CI/CD environment.
* Understand the [benefits and architecture](https://docs.microsoft.com/azure/azure-arc/kubernetes/conceptual-configurations) of this feature.
* Verify you have:
  * A [connected Azure Arc enabled Kubernetes cluster](https://docs.microsoft.com/azure/azure-arc/kubernetes/quickstart-connect-cluster#connect-an-existing-kubernetes-cluster) named **arc-cicd-cluster**.
  * A connected Azure Container Registry (ACR) with either [AKS integration](https://docs.microsoft.com/azure/aks/cluster-container-registry-integration) or [non-AKS cluster authentication](https://docs.microsoft.com/azure/container-registry/container-registry-auth-kubernetes).
  * "Build Admin" and "Project Admin" permissions for [Azure Repos](https://docs.microsoft.com/azure/devops/repos/get-started/what-is-repos) and [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/pipelines-get-started).
* Install the following Azure Arc enabled Kubernetes CLI extensions of versions >= 1.0.0:

  ```azurecli
  az extension add --name connectedk8s
  az extension add --name k8s-configuration
  ```
  * To update these extensions to the latest version, run the following commands:

    ```azurecli
    az extension update --name connectedk8s
    az extension update --name k8s-configuration
    ```

## Import application and GitOps repos into Azure Repos

Import an [application repo](https://docs.microsoft.com/azure/azure-arc/kubernetes/conceptual-gitops-cicd#application-repo) and a [GitOps repo](https://docs.microsoft.com/azure/azure-arc/kubernetes/conceptual-gitops-cicd#gitops-repo) into Azure Repos. For this tutorial, use the following example repos:

* **arc-cicd-demo-src** application repo
   * URL: https://github.com/Azure/arc-cicd-demo-src
   * Contains the example Azure Vote App that you will deploy using GitOps.
* **arc-cicd-demo-gitops** GitOps repo
   * URL: https://github.com/Azure/arc-cicd-demo-gitops
   * Works as a base for your cluster resources that house the Azure Vote App.

Learn more about [importing Git repos](https://docs.microsoft.com/azure/devops/repos/git/import-git-repository).

>[!NOTE]
> Importing and using two separate repositories for application and GitOps repos can improve security and simplicity. The application and GitOps repositories' permissions and visibility can be tuned individually.
> For example, the cluster administrator may not find the changes in application code relevant to the desired state of the cluster. Conversely, an application developer doesn't need to know the specific parameters for each environment - a set of test values that provide coverage for the parameters may be sufficient.

## Connect the GitOps repo

To continuously deploy your app, connect the application repo to your cluster using GitOps. Your **arc-cicd-demo-gitops** GitOps repo contains the basic resources to get your app up and running on your **arc-cicd-cluster** cluster.

The initial GitOps repo contains only a [manifest](https://github.com/Azure/arc-cicd-demo-gitops/blob/master/arc-cicd-cluster/manifests/namespaces.yml) that creates the **dev** and **stage** namespaces corresponding to the deployment environments.

The GitOps connection that you create will automatically:
* Sync the manifests in the manifest directory.
* Update the cluster state.

The CI/CD workflow will populate the manifest directory with extra manifests to deploy the app.


1. [Create a new GitOps connection](https://docs.microsoft.com/azure/azure-arc/kubernetes/tutorial-use-gitops-connected-cluster) to your newly imported **arc-cicd-demo-gitops** repo in Azure Repos.

   ```azurecli
   az k8sconfiguration create \
      --name cluster-config \
      --cluster-name arc-cicd-cluster \
      --resource-group myResourceGroup \
      --operator-instance-name cluster-config \
      --operator-namespace cluster-config \
      --repository-url https://dev.azure.com/<Your organization>/arc-cicd-demo-gitops \
      --https-user <Azure Repos username> \
      --https-key <Azure Repos PAT token> \
      --scope cluster \
      --cluster-type connectedClusters \
      --operator-params='--git-readonly --git-path=arc-cicd-cluster/manifests'
   ```

1. Ensure that Flux *only* uses the `arc-cicd-cluster/manifests` directory as the base path. Define the path by using the following operator parameter:

   `--git-path=arc-cicd-cluster/manifests`

   > [!NOTE]
   > If you are using an HTTPS connection string and are having connection problems, ensure you omit the username prefix in the URL. For example, `https://alice@dev.azure.com/contoso/arc-cicd-demo-gitops` must have `alice@` removed. The `--https-user` specifies the user instead, for example `--https-user alice`.

1. Check the state of the deployment in Azure portal.
   * If successful, you'll see both `dev` and `stage` namespaces created in your cluster.

## Import the CI/CD pipelines

Now that you've synced a GitOps connection, you'll need to import the CI/CD pipelines that create the manifests.

The application repo contains a `.pipeline` folder with the pipelines you'll use for PRs, CI, and CD. Import and rename the three pipelines provided in the sample repo:

| Pipeline file name | Description |
| ------------- | ------------- |
| [`.pipelines/az-vote-pr-pipeline.yaml`](https://github.com/Azure/arc-cicd-demo-src/blob/master/.pipelines/az-vote-pr-pipeline.yaml)  | The application PR pipeline, named **arc-cicd-demo-src PR** |
| [`.pipelines/az-vote-ci-pipeline.yaml`](https://github.com/Azure/arc-cicd-demo-src/blob/master/.pipelines/az-vote-ci-pipeline.yaml) | The application CI pipeline, named **arc-cicd-demo-src CI** |
| [`.pipelines/az-vote-cd-pipeline.yaml`](https://github.com/Azure/arc-cicd-demo-src/blob/master/.pipelines/az-vote-cd-pipeline.yaml) | The application CD pipeline, named **arc-cicd-demo-src CD** |



## Connect your ACR
Both your pipelines and cluster will be utilizing ACR to store and retrieve Docker images.

### Connect ACR to Azure DevOps
During the CI process, you'll deploy your application containers to a registry. Start by creating an Azure service connection:

1. In Azure DevOps, open the **Service connections** page from the project settings page. In TFS, open the **Services** page from the **settings** icon in the top menu bar.
2. Choose **+ New service connection** and select the type of service connection you need.
3. Fill in the parameters for the service connection. For this tutorial:
   * Name the service connection **arc-demo-acr**. 
   * Select **myResourceGroup** as the resource group.
4. Select the **Grant access permission to all pipelines**. 
   * This option authorizes YAML pipeline files for service connections. 
5. Choose **OK** to create the connection.

### Connect ACR to Kubernetes
Enable your Kubernetes cluster to pull images from your ACR. If it's private, authentication will be required.

#### Connect ACR to existing AKS clusters

Integrate an existing ACR with existing AKS clusters using the following command:

```azurecli
az aks update -n arc-cicd-cluster -g myResourceGroup --attach-acr arc-demo-acr
```

#### Create an image pull secret

To connect non-AKS and local clusters to your ACR, create an image pull secret. Kubernetes uses image pull secrets to store information needed to authenticate your registry.

Create an image pull secret with the following `kubectl` command. Repeat for both the `dev` and `stage` namespaces.
```console
kubectl create secret docker-registry <secret-name> \
    --namespace <namespace> \
    --docker-server=<container-registry-name>.azurecr.io \
    --docker-username=<service-principal-ID> \
    --docker-password=<service-principal-password>
```

> [!TIP]
> To avoid having to set an imagePullSecret for every Pod, consider adding the imagePullSecret to the Service account in the `dev` and `stage` namespaces. See the [Kubernetes tutorial](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/#add-imagepullsecrets-to-a-service-account) for more information.

## Create environment variable groups

### App repo variable group
[Create a variable group](https://docs.microsoft.com/azure/devops/pipelines/library/variable-groups) named **az-vote-app-dev**. Set the following values:

| Variable | Value |
| -------- | ----- |
| AZ_ACR_NAME | (your ACR instance, for example. azurearctest.azurecr.io) |
| AZURE_SUBSCRIPTION | (your Azure Service Connection, which should be **arc-demo-acr** from earlier in the tutorial) |
| AZURE_VOTE_IMAGE_REPO | The full path to the Azure Vote App repo, for example azurearctest.azurecr.io/azvote |
| ENVIRONMENT_NAME | Dev |
| MANIFESTS_BRANCH | `master` |
| MANIFESTS_REPO | The Git connection string for your GitOps repo |
| PAT | A [created PAT token](https://docs.microsoft.com/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate?#create-a-pat) with Read/Write source permissions. Save it to use later when creating the `stage` variable group. |
| SRC_FOLDER | `azure-vote` | 
| TARGET_CLUSTER | `arc-cicd-cluster` |
| TARGET_NAMESPACE | `dev` |

> [!IMPORTANT]
> Mark your PAT as a secret type. In your applications, consider linking secrets from an [Azure KeyVault](https://docs.microsoft.com/azure/devops/pipelines/library/variable-groups#link-secrets-from-an-azure-key-vault).
>
### Stage environment variable group

1. Clone the **az-vote-app-dev** variable group.
1. Change the name to **az-vote-app-stage**.
1. Ensure the following values for the corresponding variables:

| Variable | Value |
| -------- | ----- |
| ENVIRONMENT_NAME | Stage |
| TARGET_NAMESPACE | `stage` |

You're now ready to deploy to the `dev` and `stage` environments.

## Deploy the dev environment for the first time
With the CI and CD pipelines created, run the CI pipeline to deploy the app for the first time.

### CI pipeline

During the initial CI pipeline run, you may get a resource authorization error in reading the service connection name.
1. Verify the variable being accessed is AZURE_SUBSCRIPTION.
1. Authorize the use.
1. Rerun the pipeline.

The CI pipeline:
* Ensures the application change passes all automated quality checks for deployment.
* Does any extra validation that couldn't be completed in the PR pipeline.
    * Specific to GitOps, the pipeline also publishes the artifacts for the commit that will be deployed by the CD pipeline.
* Verifies the Docker image has changed and the new image is pushed.

### CD pipeline
The successful CI pipeline run triggers the CD pipeline to complete the deployment process. You'll deploy to each environment incrementally.

> [!TIP]
> If the CD pipeline does not automatically trigger:
> 1. Verify the name matches the branch trigger in [`.pipelines/az-vote-cd-pipeline.yaml`](https://github.com/Azure/arc-cicd-demo-src/blob/master/.pipelines/az-vote-cd-pipeline.yaml)
>    * It should be `arc-cicd-demo-src CI`.
> 1. Rerun the CI pipeline.

Once the template and manifest changes to the GitOps repo have been generated, the CD pipeline will create a commit, push it, and create a PR for approval.
1. Open the PR link given in the `Create PR` task output.
1. Verify the changes to the GitOps repo. You should see:
   * High-level Helm template changes.
   * Low-level Kubernetes manifests that show the underlying changes to the desired state. Flux deploys these manifests.
1. If everything looks good, approve and complete the PR.

1. After a few minutes, Flux picks up the change and starts the deployment.
1. Forward the port locally using `kubectl` and ensure the app works correctly using:

   `kubectl port-forward -n dev svc/azure-vote-front 8080:80`

1. View the Azure Vote app in your browser at `http://localhost:8080/`.

1. Vote for your favorites and get ready to make some changes to the app.

## Set up environment approvals
Upon app deployment, you can not only make changes to the code or templates, but you can also unintentionally put the cluster into a bad state.

If the dev environment reveals a break after deployment, keep it from going to later environments using environment approvals.

1. In your Azure DevOps project, go to the environment that needs to be protected.
1. Navigate to **Approvals and Checks** for the resource.
1. Select **Create**.
1. Provide the approvers and an optional message.
1. Select **Create** again to complete the addition of the manual approval check.

For more details, see the [Define approval and checks](https://docs.microsoft.com/azure/devops/pipelines/process/approvals) tutorial.

Next time the CD pipeline runs, the pipeline will pause after the GitOps PR creation. Verify the change has been synced properly and passes basic functionality. Approve the check from the pipeline to let the change flow to the next environment.

## Make an application change

With this baseline set of templates and manifests representing the state on the cluster, you'll make a small change to the app.

1. In the **arc-cicd-demo-src** repo, edit [`azure-vote/src/azure-vote-front/config_file.cfg`](https://github.com/Azure/arc-cicd-demo-src/blob/master/azure-vote/src/azure-vote-front/config_file.cfg) file.

2. Since "Cats vs Dogs" isn't getting enough votes, change it to "Tabs vs Spaces" to drive up the vote count.

3. Commit the change in a new branch, push it, and create a pull request.
   * This is the typical developer flow that will start the CI/CD lifecycle.

## PR validation pipeline

The PR pipeline is the first line of defense against a faulty change. Usual application code quality checks include linting and static analysis. From a GitOps perspective, you also need to assure the same quality for the resulting infrastructure to be deployed.

The application's Dockerfile and Helm charts can use linting in a similar way to the application.

Errors found during linting range from:
* Incorrectly formatted YAML files, to
* Best practice suggestions, such as setting CPU and Memory limits for your application.

> [!NOTE]
> To get the best coverage from Helm linting in a real application, you will need to substitute values that are reasonably similar to those used in a real environment.

Errors found during pipeline execution appear in the test results section of the run. From here, you can:
* Track the useful statistics on the error types.
* Find the first commit on which they were detected.
* Stack trace style links to the code sections that caused the error.

Once the pipeline run has finished, you have assured the quality of the application code and the template that will deploy it. You can now approve and complete the PR. The CI will run again, regenerating the templates and manifests, before triggering the CD pipeline.

> [!TIP]
> In a real environment, don't forget to set branch policies to ensure the PR passes your quality checks. For more information, see the [Set branch policies](https://docs.microsoft.com/azure/devops/repos/git/branch-policies) article.

## CD process approvals

A successful CI pipeline run triggers the CD pipeline to complete the deployment process. Similar to the first time you can the CD pipeline, you'll deploy to each environment incrementally. This time, the pipeline requires you to approve each deployment environment.

1. Approve the deployment to the `dev` environment.
1. Once the template and manifest changes to the GitOps repo have been generated, the CD pipeline will create a commit, push it, and create a PR for approval.
1. Open the PR link given in the task.
1. Verify the changes to the GitOps repo. You should see:
   * High-level Helm template changes.
   * Low-level Kubernetes manifests that show the underlying changes to the desired state.
1. If everything looks good, approve and complete the PR.
1. Wait for the deployment to complete.
1. As a basic smoke test, navigate to the application page and verify the voting app now displays Tabs vs Spaces.
   * Forward the port locally using `kubectl` and ensure the app works correctly using:
   `kubectl port-forward -n dev svc/azure-vote-front 8080:80`
   * View the Azure Vote app in your browser at http://localhost:8080/ and verify the voting choices have changed to Tabs vs Spaces. 
1. Repeat steps 1-7 for the `stage` environment.

Your deployment is now complete. This ends the CI/CD workflow.

## Clean up resources

If you're not going to continue to use this application, delete any resources with the following steps:

1. Delete the Azure Arc GitOps configuration connection:
   ```azurecli
   az k8sconfiguration delete \
   --name cluster-config \
   --cluster-name arc-cicd-cluster \
   --resource-group myResourceGroup \
   --cluster-type connectedClusters
   ```

2. Remove the `dev` namespace:
   * `kubectl delete namespace dev`

3. Remove the `stage` namespace:
   * `kubectl delete namespace stage`

## Next steps

In this tutorial, you have set up a full CI/CD workflow that implements DevOps from application development through deployment. Changes to the app automatically trigger validation and deployment, gated by manual approvals.

Advance to our conceptual article to learn more about GitOps and configurations with Azure Arc enabled Kubernetes.

> [!div class="nextstepaction"]
> [CI/CD Workflow using GitOps - Azure Arc enabled Kubernetes](https://docs.microsoft.com/azure/azure-arc/kubernetes/conceptual-gitops-cicd)
