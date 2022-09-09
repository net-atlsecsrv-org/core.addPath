---
title: "Deploy configurations using GitOps on Arc enabled Kubernetes cluster (Preview)"
services: azure-arc
ms.service: azure-arc
#ms.subservice: azure-arc-kubernetes coming soon
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: "Use GitOps for an Azure Arc-enabled cluster configuration (Preview)"
keywords: "GitOps, Kubernetes, K8s, Azure, Arc, Azure Kubernetes Service, containers"
---

# Deploy configurations using GitOps on Arc enabled Kubernetes cluster (Preview)

GitOps is the practice of the declaring the desired state of Kubernetes configuration (deployments, namespaces, and so on) in a Git repository followed by a polling and pull based deployment of these configurations to the cluster using an operator. This document covers the setup of such workflows on Azure Arc enabled Kubernetes clusters.

The connection between your cluster and one or more Git repositories is tracked in Azure Resource Manager as a `sourceControlConfiguration` extension resource. The `sourceControlConfiguration` resource properties represent where and how Kubernetes resources should flow from Git to your cluster. The `sourceControlConfiguration` data is stored encrypted at rest in an Azure Cosmos DB database to ensure data confidentiality.

The `config-agent` running in your cluster is responsible for watching for new or updated `sourceControlConfiguration` extension resources on the Azure Arc enabled Kubernetes resource, deploying a flux operator to watch the Git repository, and propagating any updates made to the `sourceControlConfiguration`. It is even possible to create multiple `sourceControlConfiguration` resources with `namespace` scope on the same Azure Arc enabled Kubernetes cluster to achieve multi-tenancy. In such a case, each operator can only deploy configurations to its respective namespace.

The Git repository can contain any valid Kubernetes resources, including Namespaces, ConfigMaps, Deployments, DaemonSets, etc.  It may also contain Helm charts for deploying applications. A common set of scenarios includes defining a baseline configuration for your organization, which might include common Azure roles and bindings, monitoring or logging agents, or cluster-wide services.

The same pattern can be used to manage a larger collection of clusters, which may be deployed across heterogeneous environments. For example, you may have one repository that defines the baseline configuration for your organization and apply that to tens of Kubernetes clusters at once. [Azure policy can automate](use-azure-policy.md) the creation of a `sourceControlConfiguration` with a specific set of parameters on all Azure Arc enabled Kubernetes resources under a scope (subscription or resource group).

This getting started guide will walk you through applying a set of configurations with cluster-admin scope.

## Before you begin

This article assumes that you have an existing Azure Arc enabled Kubernetes connected cluster. If you need a connected cluster, see the [connect a cluster quickstart](./connect-cluster.md).

## Create a configuration

The [example repository](https://github.com/Azure/arc-k8s-demo) used in this document is structured around the persona of a cluster operator who would like to provision a few namespaces, deploy a common workload, and provide some team-specific configuration. Using this repository creates the following resources on your cluster:

**Namespaces:** `cluster-config`, `team-a`, `team-b`
**Deployment:** `cluster-config/azure-vote`
**ConfigMap:** `team-a/endpoints`

The `config-agent` polls Azure for new or updated `sourceControlConfiguration` every 30 seconds, which is the maximum time taken by `config-agent` to pick up a new or updated configuration.
If you are associating a private repository with the `sourceControlConfiguration`, ensure that you also complete the steps in [Apply configuration from a private git repository](#apply-configuration-from-a-private-git-repository).

### Using Azure CLI

Using the Azure CLI extension for `k8sconfiguration`, let's link our connected cluster to an [example git repository](https://github.com/Azure/arc-k8s-demo). We will give this configuration a name `cluster-config`, instruct the agent to deploy the operator in the `cluster-config` namespace, and give the operator `cluster-admin` permissions.

```console
az k8sconfiguration create --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --operator-instance-name cluster-config --operator-namespace cluster-config --repository-url https://github.com/Azure/arc-k8s-demo --scope cluster --cluster-type connectedClusters
```

**Output:**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
{
  "complianceStatus": {
    "ComplianceStatus": 1,
    "clientAppliedTime": null,
    "level": 3,
    "message": "{\"OperatorMessage\":null,\"ClusterState\":null}"
  },
  "configKind": "Git",
  "configName": "cluster-config",
  "configOperator": {
    "operatorInstanceName": "cluster-config",
    "operatorNamespace": "cluster-config",
    "operatorParams": "--git-readonly",
    "operatorScope": "cluster",
    "operatorType": "flux"
  },
  "configType": "",
  "id": null,
  "name": null,
  "operatorInstanceName": null,
  "operatorNamespace": null,
  "operatorParams": null,
  "operatorScope": null,
  "operatorType": null,
  "providerName": "ConnectedClusters",
  "provisioningState": "Succeeded",
  "repositoryPublicKey": null,
  "repositoryUrl": null,
  "sourceControlConfiguration": {
    "repositoryPublicKey": "",
    "repositoryUrl": "git://github.com/Azure/arc-k8s-demo.git"
  },
  "type": null
}
```

#### repository-url Parameter

Here are the supported scenarios for the value of --repository-url parameter.

| Scenario | Format | Description |
| ------------- | ------------- | ------------- |
| Private GitHub repo - SSH | git@github.com:username/repo | SSH keypair generated by Flux.  User must add the public key to the GitHub account as Deploy Key. |
| Public GitHub repo | `http://github.com/username/repo` or git://github.com/username/repo   | Public Git repo  |

These scenarios are supported by Flux but not by sourceControlConfiguration yet. 

| Scenario | Format | Description |
| ------------- | ------------- | ------------- |
| Private GitHub repo - HTTPS | `https://github.com/username/repo` | Flux does not generate SSH keypair.  [Instructions](https://docs.fluxcd.io/en/1.17.0/guides/use-git-https.html) |
| Private Git host | user@githost:path/to/repo | [Instructions](https://docs.fluxcd.io/en/1.18.0/guides/use-private-git-host.html) |
| Private GitHub repo - SSH (bring your own key) | git@github.com:username/repo | [Use your own SSH keypair](https://docs.fluxcd.io/en/1.17.0/guides/provide-own-ssh-key.html) |


#### Additional Parameters

To customize the creation of configuration, here are a few additional parameters:

`--enable-helm-operator` : *Optional* switch to enable support for Helm chart deployments.

`--helm-operator-chart-values` : *Optional* chart values for Helm operator (if enabled).  For example, '--set helm.versions=v3'.

`--helm-operator-chart-version` : *Optional* chart version for Helm operator (if enabled). Default: '0.6.0'.

`--operator-namespace` : *Optional* name for the operator namespace. Default: 'default'

`--operator-params` : *Optional* parameters for operator. Must be given within single quotes. For example, ```--operator-params='--git-readonly --git-path=releases' ```

Options supported in  --operator-params

| Option | Description |
| ------------- | ------------- |
| --git-branch  | Branch of git repo to use for Kubernetes manifests. Default is 'master'. |
| --git-path  | Relative path within the Git repo for Flux to locate Kubernetes manifests. |
| --git-readonly | Git repo will be considered read-only; Flux will not attempt to write to it. |
| --manifest-generation  | If enabled, Flux will look for .flux.yaml and run Kustomize or other manifest generators. |
| --git-poll-interval  | Period at which to poll Git repo for new commits. Default is '5m' (5 minutes). |
| --sync-garbage-collection  | If enabled, Flux will delete resources that it created, but are no longer present in Git. |
| --git-label  | Label to keep track of sync progress, used to tag the Git branch.  Default is 'flux-sync'. |
| --git-user  | Username for git commit. |
| --git-email  | Email to use for git commit. |

* If '--git-user' or '--git-email' are not set (which means that you don't want Flux to write to the repo), then --git-readonly will automatically be set (if you have not already set it).

* If enableHelmOperator is true, then operatorInstanceName + operatorNamespace strings cannot exceed 47 characters combined.  If you fail to adhere to this limit, you will get the following error:

   ```console
   {"OperatorMessage":"Error: {failed to install chart from path [helm-operator] for release [<operatorInstanceName>-helm-<operatorNamespace>]: err [release name \"<operatorInstanceName>-helm-<operatorNamespace>\" exceeds max length of 53]} occurred while doing the operation : {Installing the operator} on the config","ClusterState":"Installing the operator"}
   ```

For more information, see [Flux documentation](https://aka.ms/FluxcdReadme).

> [!TIP]
> It is possible to create a sourceControlConfiguration on the Azure portal as well under the **Configurations** tab of the Azure Arc enabled Kubernetes resource blade.

## Validate the sourceControlConfiguration

Using the Azure CLI validate that the `sourceControlConfiguration` was successfully created.

```console
az k8sconfiguration show --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

Note that the `sourceControlConfiguration` resource is updated with compliance status, messages, and debugging information.

**Output:**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
{
  "complianceStatus": {
    "complianceState": "Installed",
    "lastConfigApplied": "2019-12-05T05:34:41.481000",
    "message": "...",
    "messageLevel": "3"
  },
  "id": "/subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1/providers/Microsoft.KubernetesConfiguration/sourceControlConfigurations/cluster-config",
  "name": "cluster-config",
  "operatorInstanceName": "cluster-config",
  "operatorNamespace": "cluster-config",
  "operatorParams": "--git-readonly",
  "operatorScope": "cluster",
  "operatorType": "Flux",
  "provisioningState": "Succeeded",
  "repositoryPublicKey": "...",
  "repositoryUrl": "git://github.com/Azure/arc-k8s-demo.git",
  "resourceGroup": "AzureArcTest",
  "type": "Microsoft.KubernetesConfiguration/sourceControlConfigurations"
}
```

When the `sourceControlConfiguration` is created, a few things happen under the hood:

1. The Azure Arc `config-agent` monitors Azure Resource Manager for new or updated configurations (`Microsoft.KubernetesConfiguration/sourceControlConfiguration`)
1. `config-agent` notices the new `Pending` configuration
1. `config-agent` reads the configuration properties and prepares to deploy a managed instance of `flux`
    * `config-agent` creates the destination namespace
    * `config-agent` prepares a Kubernetes Service Account with the appropriate permission (`cluster` or `namespace` scope)
    * `config-agent` deploys an instance of `flux`
    * `flux` generates an SSH key and logs the public key
1. `config-agent` reports status back to the `sourceControlConfiguration`

While the provisioning process happens, the `sourceControlConfiguration` will move through a few state changes. Monitor progress with the `az k8sconfiguration show ...` command above:

1. `complianceStatus` -> `Pending`: represents the initial and in-progress states
1. `complianceStatus` -> `Installed`: `config-agent` was able to successfully configure the cluster and deploy `flux` without error
1. `complianceStatus` -> `Failed`: `config-agent` encountered an error deploying `flux`, details should be available in `complianceStatus.message` response body

## Apply configuration from a private git repository

If you are using a private git repo, then you need to perform one more task to close the loop: add the public key generated by `flux` as a **Deploy key** in the repo.

**Get the public key using Azure CLI**

```console
$ az k8sconfiguration show --resource-group <resource group name> --cluster-name <connected cluster name> --name <configuration name> --query 'repositoryPublicKey'
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAREDACTED"
```

**Get the public key from the Azure portal**

1. In the Azure portal, navigate to the connected cluster resource.
2. In the resource page, select "Configurations" and see the list of configurations for this cluster.
3. Select the configuration that uses the private Git repository.
4. In the context window that opens, at the bottom of the window, copy the **Repository public key**.

**Add the public key as a deploy key to the git repo**

1. Open GitHub, navigate to your fork, to **Settings**, then to **Deploy keys**
2. Click  **Add deploy key**
3. Supply a Title
4. Check **Allow write access**
5. Paste the public key (minus any surrounding quotation marks)
6. Click **Add key**

See the GitHub docs for more info on how to manage these keys.

**If you are using an Azure DevOps repository, add the key to your SSH keys**

1. Under **User Settings** in the top right (next to the profile image), click **SSH public keys**
1. Select  **+ New Key**
1. Supply a name
1. Paste the public key without any surrounding quotes
1. Click **Add**

## Validate the Kubernetes configuration

After `config-agent` has installed the `flux` instance, resources held in the git repository should begin to flow to the cluster. Check to see that the namespaces, deployments, and resources have been created:

```console
kubectl get ns --show-labels
```

**Output:**

```console
NAME              STATUS   AGE    LABELS
azure-arc         Active   24h    <none>
cluster-config    Active   177m   <none>
default           Active   29h    <none>
itops             Active   177m   fluxcd.io/sync-gc-mark=sha256.9oYk8yEsRwWkR09n8eJCRNafckASgghAsUWgXWEQ9es,name=itops
kube-node-lease   Active   29h    <none>
kube-public       Active   29h    <none>
kube-system       Active   29h    <none>
team-a            Active   177m   fluxcd.io/sync-gc-mark=sha256.CS5boSi8kg_vyxfAeu7Das5harSy1i0gc2fodD7YDqA,name=team-a
team-b            Active   177m   fluxcd.io/sync-gc-mark=sha256.vF36thDIFnDDI2VEttBp5jgdxvEuaLmm7yT_cuA2UEw,name=team-b
```

We can see that `team-a`, `team-b`, `itops`, and `cluster-config` namespaces have been created.

The `flux` operator has been deployed to `cluster-config` namespace, as directed by our `sourceControlConfig`:

```console
kubectl -n cluster-config get deploy  -o wide
```

**Output:**

```console
NAME             READY   UP-TO-DATE   AVAILABLE   AGE   CONTAINERS   IMAGES                         SELECTOR
cluster-config   1/1     1            1           3h    flux         docker.io/fluxcd/flux:1.16.0   instanceName=cluster-config,name=flux
memcached        1/1     1            1           3h    memcached    memcached:1.5.15               name=memcached
```

## Further exploration

You can explore the other resources deployed as part of the configuration repository:

```console
kubectl -n team-a get cm -o yaml
kubectl -n itops get all
```

## Delete a configuration

Delete a `sourceControlConfiguration` using the Azure CLI or Azure portal.  After you initiate the delete command, the `sourceControlConfiguration` resource will be deleted immediately in Azure, but it can take up to 1 hour for full deletion of the associated objects from the cluster (we have a backlog item to reduce this time lag).

> [!NOTE]
> After a sourceControlConfiguration with namespace scope is created, it's possible for users with `edit` role binding on namespace to deploy workloads on this namespace. When this `sourceControlConfiguration` with namespace scope gets deleted, the namespace is left intact and will not be deleted to avoid breaking these other workloads.
> Any changes to the cluster that were the result of deployments from the tracked git repo are not deleted when the `sourceControlConfiguration` is deleted.

```console
az k8sconfiguration delete --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

**Output:**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
```

## Next steps

- [Use Helm with source control configuration](./use-gitops-with-helm.md)
- [Use Azure Policy to govern cluster configuration](./use-azure-policy.md)
