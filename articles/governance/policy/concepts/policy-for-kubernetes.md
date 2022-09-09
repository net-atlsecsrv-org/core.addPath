---
title: Learn Azure Policy for Kubernetes
description: Learn how Azure Policy uses Rego and Open Policy Agent to manage clusters running Kubernetes in Azure or on-premises.
ms.date: 09/29/2020
ms.topic: conceptual
---
# Understand Azure Policy for Kubernetes clusters

Azure Policy extends [Gatekeeper](https://github.com/open-policy-agent/gatekeeper) v3, an _admission
controller webhook_ for [Open Policy Agent](https://www.openpolicyagent.org/) (OPA), to apply
at-scale enforcements and safeguards on your clusters in a centralized, consistent manner. Azure
Policy makes it possible to manage and report on the compliance state of your Kubernetes clusters
from one place. The add-on enacts the following functions:

- Checks with Azure Policy service for policy assignments to the cluster.
- Deploys policy definitions into the cluster as
  [constraint template](https://github.com/open-policy-agent/gatekeeper#constraint-templates) and
  [constraint](https://github.com/open-policy-agent/gatekeeper#constraints) custom resources.
- Reports auditing and compliance details back to Azure Policy service.

Azure Policy for Kubernetes supports the following cluster environments:

- [Azure Kubernetes Service (AKS)](../../../aks/intro-kubernetes.md)
- [Azure Arc enabled Kubernetes](../../../azure-arc/kubernetes/overview.md)
- [AKS Engine](https://github.com/Azure/aks-engine/blob/master/docs/README.md)

> [!IMPORTANT]
> The add-ons for AKS Engine and Arc enabled Kubernetes are in **preview**. Azure Policy for
> Kubernetes only supports Linux node pools and built-in policy definitions. Built-in policy
> definitions are in the **Kubernetes** category. The limited preview policy definitions with
> **EnforceOPAConstraint** and **EnforceRegoPolicy** effect and the related **Kubernetes Service**
> category are _deprecated_. Instead, use the effects _audit_ and _deny_ with Resource Provider mode
> `Microsoft.Kubernetes.Data`.

## Overview

To enable and use Azure Policy with your Kubernetes cluster, take the following actions:

1. Configure your Kubernetes cluster and install the add-on:
   - [Azure Kubernetes Service (AKS)](#install-azure-policy-add-on-for-aks)
   - [Azure Arc enabled Kubernetes](#install-azure-policy-add-on-for-azure-arc-enabled-kubernetes)
   - [AKS Engine](#install-azure-policy-add-on-for-aks-engine)

   > [!NOTE]
   > For common issues with installation, see
   > [Troubleshoot - Azure Policy Add-on](../troubleshoot/general.md#add-on-installation-errors).

1. [Understand the Azure Policy language for Kubernetes](#policy-language)

1. [Assign a built-in definition to your Kubernetes cluster](#assign-a-built-in-policy-definition)

1. [Wait for validation](#policy-evaluation)

## Limitations

The following general limitations apply to the Azure Policy Add-on for Kubernetes clusters:

- Azure Policy Add-on for Kubernetes is supported on Kubernetes version **1.14** or higher.
- Azure Policy Add-on for Kubernetes can only be deployed to Linux node pools
- Only built-in policy definitions are supported
- Maximum number of Non-compliant records per policy per cluster: **500**
- Maximum number of Non-compliant records per subscription: **1 million**
- Installations of Gatekeeper outside of the Azure Policy Add-on aren't supported. Uninstall any
  components installed by a previous Gatekeeper installation before enabling the Azure Policy
  Add-on.
- [Reasons for non-compliance](../how-to/determine-non-compliance.md#compliance-reasons) aren't
  available for the `Microsoft.Kubernetes.Data`
  [Resource Provider mode](./definition-structure.md#resource-provider-modes). Use
  [Component details](../how-to/determine-non-compliance.md#component-details-for-resource-provider-modes).
- [Exemptions](./exemption-structure.md) aren't supported for
  [Resource Provider modes](./definition-structure.md#resource-provider-modes).

The following limitations apply only to the Azure Policy Add-on for AKS:

- [AKS Pod security policy](../../../aks/use-pod-security-policies.md) and the Azure Policy Add-on
  for AKS can't both be enabled. For more information, see
  [AKS pod security limitation](../../../aks/use-pod-security-on-azure-policy.md#limitations).
- Namespaces automatically excluded by Azure Policy Add-on for evaluation: _kube-system_,
  _gatekeeper-system_, and _aks-periscope_.

## Recommendations

The following are general recommendations for using the Azure Policy Add-on:

- The Azure Policy Add-on requires 3 Gatekeeper components to run: 1 audit pod and 2 webhook pod
  replicas. These components consume more resources as the count of Kubernetes resources and policy
  assignments increases in the cluster which requires audit and enforcement operations.

  - For less than 500 pods in a single cluster with a max of 20 constraints: 2 vCPUs and 350 MB
    memory per component.
  - For more than 500 pods in a single cluster with a max of 40 constraints: 3 vCPUs and 600 MB
    memory per component.

- Windows pods
  [don't support security contexts](https://kubernetes.io/docs/concepts/security/pod-security-standards/#what-profiles-should-i-apply-to-my-windows-pods).
  Thus, some of the Azure Policy definitions, such as disallowing root privileges, can't be
  escalated in Windows pods and only apply to Linux pods.

The following recommendation applies only to AKS and the Azure Policy Add-on:

- Use system node pool with `CriticalAddonsOnly` taint to schedule Gatekeeper pods. For more
  information, see
  [Using system node pools](../../../aks/use-system-pools.md#system-and-user-node-pools).
- Secure outbound traffic from your AKS clusters. For more information, see
  [Control egress traffic for cluster nodes](../../../aks/limit-egress-traffic.md).
- If the cluster has `aad-pod-identity` enabled, Node Managed Identity (NMI) pods modify the nodes'
  iptables to intercept calls to the Azure Instance Metadata endpoint. This configuration means any
  request made to the Metadata endpoint is intercepted by NMI even if the pod doesn't use
  `aad-pod-identity`. AzurePodIdentityException CRD can be configured to inform `aad-pod-identity`
  that any requests to the Metadata endpoint originating from a pod that matches labels defined in
  CRD should be proxied without any processing in NMI. The system pods with
  `kubernetes.azure.com/managedby: aks` label in _kube-system_ namespace should be excluded in
  `aad-pod-identity` by configuring the AzurePodIdentityException CRD. For more information, see
  [Disable aad-pod-identity for a specific pod or application](https://github.com/Azure/aad-pod-identity/blob/master/docs/readmes/README.app-exception.md).
  To configure an exception, install the
  [mic-exception YAML](https://github.com/Azure/aad-pod-identity/blob/master/deploy/infra/mic-exception.yaml).

## Install Azure Policy Add-on for AKS

Before installing the Azure Policy Add-on or enabling any of the service features, your subscription
must enable the **Microsoft.ContainerService** and **Microsoft.PolicyInsights** resource providers.

1. You need the Azure CLI version 2.12.0 or later installed and configured. Run `az --version` to
   find the version. If you need to install or upgrade, see
   [Install the Azure CLI](/cli/azure/install-azure-cli).

1. Register the resource providers and preview features.

   - Azure portal:

     Register the **Microsoft.ContainerService** and **Microsoft.PolicyInsights** resource
     providers. For steps, see
     [Resource providers and types](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal).

   - Azure CLI:

     ```azurecli-interactive
     # Log in first with az login if you're not using Cloud Shell

     # Provider register: Register the Azure Kubernetes Service provider
     az provider register --namespace Microsoft.ContainerService

     # Provider register: Register the Azure Policy provider
     az provider register --namespace Microsoft.PolicyInsights
     ```

1. If limited preview policy definitions were installed, remove the add-on with the **Disable**
   button on your AKS cluster under the **Policies** page.

1. The AKS cluster must be version _1.14_ or higher. Use the following script to validate your AKS
   cluster version:

   ```azurecli-interactive
   # Log in first with az login if you're not using Cloud Shell

   # Look for the value in kubernetesVersion
   az aks list
   ```

1. Install version _2.12.0_ or higher of the Azure CLI. For more information, see
   [Install the Azure CLI](/cli/azure/install-azure-cli).

Once the above prerequisite steps are completed, install the Azure Policy Add-on in the AKS cluster
you want to manage.

- Azure portal

  1. Launch the AKS service in the Azure portal by selecting **All services**, then searching for
     and selecting **Kubernetes services**.

  1. Select one of your AKS clusters.

  1. Select **Policies** on the left side of the Kubernetes service page.

  1. In the main page, select the **Enable add-on** button.

     <a name="migrate-from-v1"></a>
     > [!NOTE]
     > If the **Disable add-on** button is enabled and a migration warning v2 message is displayed,
     > v1 add-on is installed and must be removed prior to assigning v2 policy definitions. The
     > _deprecated_ v1 add-on will automatically be replaced with the v2 add-on starting August 24,
     > 2020. New v2 versions of the policy definitions must then be assigned. To upgrade now, follow
     > these steps:
     >
     > 1. Validate your AKS cluster has the v1 add-on installed by visiting the **Policies** page on your AKS cluster and has the "The current cluster uses Azure Policy add-on v1..." message.
     > 1. [Remove the add-on](#remove-the-add-on-from-aks).
     > 1. Select the **Enable add-on** button to install the v2 version of the add-on.
     > 1. [Assign v2 versions of your v1 built-in policy definitions](#assign-a-built-in-policy-definition)

- Azure CLI

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks enable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

To validate that the add-on installation was successful and that the _azure-policy_ and _gatekeeper_
pods are running, run the following command:

```bash
# azure-policy pod is installed in kube-system namespace
kubectl get pods -n kube-system

# gatekeeper pod is installed in gatekeeper-system namespace
kubectl get pods -n gatekeeper-system
```

Lastly, verify that the latest add-on is installed by running this Azure CLI command, replacing
`<rg>` with your resource group name and `<cluster-name>` with the name of your AKS cluster:
`az aks show -g <rg> -n <cluster-name>`. The result should look similar to the following output and
**config.version** should be `v2`:

```output
"addonProfiles": {
    "azurepolicy": {
        "config": {
            "version": "v2"
        },
        "enabled": true,
        "identity": null
    },
}
```

## <a name="install-azure-policy-add-on-for-azure-arc-enabled-kubernetes"></a>Install Azure Policy Add-on for Azure Arc enabled Kubernetes (preview)

Before installing the Azure Policy Add-on or enabling any of the service features, your subscription
must enable the **Microsoft.PolicyInsights** resource provider and create a role assignment for the
cluster service principal.

1. You need the Azure CLI version 2.12.0 or later installed and configured. Run `az --version` to
   find the version. If you need to install or upgrade, see
   [Install the Azure CLI](/cli/azure/install-azure-cli).

1. To enable the resource provider, follow the steps in
   [Resource providers and types](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)
   or run either the Azure CLI or Azure PowerShell command:

   - Azure CLI

     ```azurecli-interactive
     # Log in first with az login if you're not using Cloud Shell

     # Provider register: Register the Azure Policy provider
     az provider register --namespace 'Microsoft.PolicyInsights'
     ```

   - Azure PowerShell

     ```azurepowershell-interactive
     # Log in first with Connect-AzAccount if you're not using Cloud Shell

     # Provider register: Register the Azure Policy provider
     Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
     ```

1. The Kubernetes cluster must be version _1.14_ or higher.

1. Install [Helm 3](https://v3.helm.sh/docs/intro/install/).

1. Your Kubernetes cluster enabled for Azure Arc. For more information, see
   [onboarding a Kubernetes cluster to Azure Arc](../../../azure-arc/kubernetes/connect-cluster.md).

1. Have the fully qualified Azure Resource ID of the Azure Arc enabled Kubernetes cluster.

1. Open ports for the add-on. The Azure Policy Add-on uses these domains and ports to fetch policy
   definitions and assignments and report compliance of the cluster back to Azure Policy.

   |Domain |Port |
   |---|---|
   |`gov-prod-policy-data.trafficmanager.net` |`443` |
   |`raw.githubusercontent.com` |`443` |
   |`login.windows.net` |`443` |
   |`dc.services.visualstudio.com` |`443` |

1. Assign 'Policy Insights Data Writer (Preview)' role assignment to the Azure Arc enabled
   Kubernetes cluster. Replace `<subscriptionId>` with your subscription ID, `<rg>` with the Azure
   Arc enabled Kubernetes cluster's resource group, and `<clusterName>` with the name of the Azure
   Arc enabled Kubernetes cluster. Keep track of the returned values for _appId_, _password_, and
   _tenant_ for the installation steps.

   - Azure CLI

     ```azurecli-interactive
     az ad sp create-for-rbac --role "Policy Insights Data Writer (Preview)" --scopes "/subscriptions/<subscriptionId>/resourceGroups/<rg>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"
     ```

   - Azure PowerShell

     ```azurepowershell-interactive
     $sp = New-AzADServicePrincipal -Role "Policy Insights Data Writer (Preview)" -Scope "/subscriptions/<subscriptionId>/resourceGroups/<rg>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"

     @{ appId=$sp.ApplicationId;password=[System.Runtime.InteropServices.Marshal]::PtrToStringAuto([System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($sp.Secret));tenant=(Get-AzContext).Tenant.Id } | ConvertTo-Json
     ```

   Sample output of the above commands:

   ```json
   {
       "appId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
       "password": "bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb",
       "tenant": "cccccccc-cccc-cccc-cccc-cccccccccccc"
   }
   ```

Once the above prerequisite steps are completed, install the Azure Policy Add-on in your Azure Arc
enabled Kubernetes cluster:

1. Add the Azure Policy Add-on repo to Helm:

   ```bash
   helm repo add azure-policy https://raw.githubusercontent.com/Azure/azure-policy/master/extensions/policy-addon-kubernetes/helm-charts
   ```

1. Install the Azure Policy Add-on using Helm Chart:

   ```bash
   # In below command, replace the following values with those gathered above.
   #    <AzureArcClusterResourceId> with your Azure Arc enabled Kubernetes cluster resource Id. For example: /subscriptions/<subscriptionId>/resourceGroups/<rg>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>
   #    <ServicePrincipalAppId> with app Id of the service principal created during prerequisites.
   #    <ServicePrincipalPassword> with password of the service principal created during prerequisites.
   #    <ServicePrincipalTenantId> with tenant of the service principal created during prerequisites.
   helm install azure-policy-addon azure-policy/azure-policy-addon-arc-clusters \
       --set azurepolicy.env.resourceid=<AzureArcClusterResourceId> \
       --set azurepolicy.env.clientid=<ServicePrincipalAppId> \
       --set azurepolicy.env.clientsecret=<ServicePrincipalPassword> \
       --set azurepolicy.env.tenantid=<ServicePrincipalTenantId>
   ```

   For more information about what the add-on Helm Chart installs, see the
   [Azure Policy Add-on Helm Chart definition](https://github.com/Azure/azure-policy/tree/master/extensions/policy-addon-kubernetes/helm-charts/azure-policy-addon-arc-clusters)
   on GitHub.

To validate that the add-on installation was successful and that the _azure-policy_ and _gatekeeper_
pods are running, run the following command:

```bash
# azure-policy pod is installed in kube-system namespace
kubectl get pods -n kube-system

# gatekeeper pod is installed in gatekeeper-system namespace
kubectl get pods -n gatekeeper-system
```

## <a name="install-azure-policy-add-on-for-aks-engine"></a>Install Azure Policy Add-on for AKS Engine (preview)

Before installing the Azure Policy Add-on or enabling any of the service features, your subscription
must enable the **Microsoft.PolicyInsights** resource provider and create a role assignment for the
cluster service principal.

1. You need the Azure CLI version 2.0.62 or later installed and configured. Run `az --version` to
   find the version. If you need to install or upgrade, see [Install the Azure CLI](/cli/azure/install-azure-cli).

1. To enable the resource provider, follow the steps in
   [Resource providers and types](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)
   or run either the Azure CLI or Azure PowerShell command:

   - Azure CLI

     ```azurecli-interactive
     # Log in first with az login if you're not using Cloud Shell

     # Provider register: Register the Azure Policy provider
     az provider register --namespace 'Microsoft.PolicyInsights'
     ```

   - Azure PowerShell

     ```azurepowershell-interactive
     # Log in first with Connect-AzAccount if you're not using Cloud Shell

     # Provider register: Register the Azure Policy provider
     Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
     ```

1. Create a role assignment for the cluster service principal.

   - If you don't know the cluster service principal app ID, look it up with the following command.

     ```bash
     # Get the kube-apiserver pod name
     kubectl get pods -n kube-system

     # Find the aadClientID value
     kubectl exec <kube-apiserver pod name> -n kube-system cat /etc/kubernetes/azure.json
     ```

   - Assign 'Policy Insights Data Writer (Preview)' role assignment to the cluster service principal
     app ID (value _aadClientID_ from previous step) with Azure CLI. Replace `<subscriptionId>` with
     your subscription ID and `<aks engine cluster resource group>` with the resource group the AKS
     Engine self-managed Kubernetes cluster is in.

     ```azurecli-interactive
     az role assignment create --assignee <cluster service principal app ID> --scope "/subscriptions/<subscriptionId>/resourceGroups/<aks engine cluster resource group>" --role "Policy Insights Data Writer (Preview)"
     ```

Once the above prerequisite steps are completed, install the Azure Policy Add-on. The installation
can be during the creation or update cycle of an AKS Engine or as an independent action on an
existing cluster.

- Install during creation or update cycle

  To enable the Azure Policy Add-on during the creation of a new self-managed cluster or as an
  update to an existing cluster, include the
  [addons](https://github.com/Azure/aks-engine/tree/master/examples/addons/azure-policy) property
  cluster definition for AKS Engine.

  ```json
  "addons": [{
      "name": "azure-policy",
      "enabled": true
  }]
  ```

  For more information about, see the external guide
  [AKS Engine cluster definition](https://github.com/Azure/aks-engine/blob/master/docs/topics/clusterdefinitions.md).

- Install in existing cluster with Helm Charts

  Use the following steps to prepare the cluster and install the add-on:

  1. Install [Helm 3](https://v3.helm.sh/docs/intro/install/).

  1. Add the Azure Policy repo to Helm.

     ```bash
     helm repo add azure-policy https://raw.githubusercontent.com/Azure/azure-policy/master/extensions/policy-addon-kubernetes/helm-charts
     ```

     For more information, see
     [Helm Chart - Quickstart Guide](https://helm.sh/docs/using_helm/#quickstart-guide).

  1. Install the add-on with a Helm Chart. Replace `<subscriptionId>` with your subscription ID and
     `<aks engine cluster resource group>` with the resource group the AKS Engine self-managed
     Kubernetes cluster is in.

     ```bash
     helm install azure-policy-addon azure-policy/azure-policy-addon-aks-engine --set azurepolicy.env.resourceid="/subscriptions/<subscriptionId>/resourceGroups/<aks engine cluster resource group>"
     ```

     For more information about what the add-on Helm Chart installs, see the
     [Azure Policy Add-on Helm Chart definition](https://github.com/Azure/azure-policy/tree/master/extensions/policy-addon-kubernetes/helm-charts/azure-policy-addon-aks-engine)
     on GitHub.

     > [!NOTE]
     > Because of the relationship between Azure Policy Add-on and the resource group id, Azure
     > Policy supports only one AKS Engine cluster for each resource group.

To validate that the add-on installation was successful and that the _azure-policy_ and _gatekeeper_
pods are running, run the following command:

```bash
# azure-policy pod is installed in kube-system namespace
kubectl get pods -n kube-system

# gatekeeper pod is installed in gatekeeper-system namespace
kubectl get pods -n gatekeeper-system
```

## Policy language

The Azure Policy language structure for managing Kubernetes follows that of existing policy
definitions. With a [Resource Provider mode](./definition-structure.md#resource-provider-modes) of
`Microsoft.Kubernetes.Data`, the effects [audit](./effects.md#audit) and [deny](./effects.md#deny)
are used to manage your Kubernetes clusters. _Audit_ and _deny_ must provide **details** properties
specific to working with
[OPA Constraint Framework](https://github.com/open-policy-agent/frameworks/tree/master/constraint)
and Gatekeeper v3.

As part of the _details.constraintTemplate_ and _details.constraint_ properties in the policy
definition, Azure Policy passes the URIs of these
[CustomResourceDefinitions](https://github.com/open-policy-agent/gatekeeper#constraint-templates)
(CRD) to the add-on. Rego is the language that OPA and Gatekeeper support to validate a request to
the Kubernetes cluster. By supporting an existing standard for Kubernetes management, Azure Policy
makes it possible to reuse existing rules and pair them with Azure Policy for a unified cloud
compliance reporting experience. For more information, see
[What is Rego?](https://www.openpolicyagent.org/docs/latest/policy-language/#what-is-rego).

## Assign a built-in policy definition

To assign a policy definition to your Kubernetes cluster, you must be assigned the appropriate
Azure role-based access control (Azure RBAC) policy assignment operations. The Azure built-in roles **Resource
Policy Contributor** and **Owner** have these operations. To learn more, see
[Azure RBAC permissions in Azure Policy](../overview.md#azure-rbac-permissions-in-azure-policy).

Find the built-in policy definitions for managing your cluster using the Azure portal with the
following steps:

1. Start the Azure Policy service in the Azure portal. Select **All services** in the left pane and
   then search for and select **Policy**.

1. In the left pane of the Azure Policy page, select **Definitions**.

1. From the Category drop-down list box, use **Select all** to clear the filter and then select
   **Kubernetes**.

1. Select the policy definition, then select the **Assign** button.

1. Set the **Scope** to the management group, subscription, or resource group of the Kubernetes
   cluster where the policy assignment will apply.

   > [!NOTE]
   > When assigning the Azure Policy for Kubernetes definition, the **Scope** must include the
   > cluster resource. For an AKS Engine cluster, the **Scope** must be the resource group of the
   > cluster.

1. Give the policy assignment a **Name** and **Description** that you can use to identify it easily.

1. Set the [Policy enforcement](./assignment-structure.md#enforcement-mode) to one of the values
   below.

   - **Enabled** - Enforce the policy on the cluster. Kubernetes admission requests with violations
     are denied.

   - **Disabled** - Don't enforce the policy on the cluster. Kubernetes admission requests with
     violations aren't denied. Compliance assessment results are still available. When rolling out
     new policy definitions to running clusters, _Disabled_ option is helpful for testing the policy
     definition as admission requests with violations aren't denied.

1. Select **Next**.

1. Set **parameter values**

   - To exclude Kubernetes namespaces from policy evaluation, specify the list of namespaces in
     parameter **Namespace exclusions**. It's recommended to exclude: _kube-system_,
     _gatekeeper-system_, and _azure-arc_.

1. Select **Review + create**.

Alternately, use the [Assign a policy - Portal](../assign-policy-portal.md) quickstart to find and
assign a Kubernetes policy. Search for a Kubernetes policy definition instead of the sample 'audit
vms'.

> [!IMPORTANT]
> Built-in policy definitions are available for Kubernetes clusters in category **Kubernetes**. For
> a list of built-in policy definitions, see
> [Kubernetes samples](../samples/built-in-policies.md#kubernetes).

## Policy evaluation

The add-on checks in with Azure Policy service for changes in policy assignments every 15 minutes.
During this refresh cycle, the add-on checks for changes. These changes trigger creates, updates, or
deletes of the constraint templates and constraints.

In a Kubernetes cluster, if a namespace has either of the following labels, the admission requests
with violations aren't denied. Compliance assessment results are still available.

- `control-plane`
- `admission.policy.azure.com/ignore`

> [!NOTE]
> While a cluster admin may have permission to create and update constraint templates and
> constraints resources install by the Azure Policy Add-on, these aren't supported scenarios as
> manual updates are overwritten. Gatekeeper continues to evaluate policies that existed prior to
> installing the add-on and assigning Azure Policy policy definitions.

Every 15 minutes, the add-on calls for a full scan of the cluster. After gathering details of the
full scan and any real-time evaluations by Gatekeeper of attempted changes to the cluster, the
add-on reports the results back to Azure Policy for inclusion in
[compliance details](../how-to/get-compliance-data.md) like any Azure Policy assignment. Only
results for active policy assignments are returned during the audit cycle. Audit results can also be
seen as [violations](https://github.com/open-policy-agent/gatekeeper#audit) listed in the status
field of the failed constraint. For details on _Non-compliant_ resources, see
[Component details for Resource Provider modes](../how-to/determine-non-compliance.md#component-details-for-resource-provider-modes).

> [!NOTE]
> Each compliance report in Azure Policy for your Kubernetes clusters include all violations within
> the last 45 minutes. The timestamp indicates when a violation occurred.

## Logging

As a Kubernetes controller/container, both the the _azure-policy_ and _gatekeeper_ pods keep logs in
the Kubernetes cluster. The logs can be exposed in the **Insights** page of the Kubernetes cluster.
For more information, see
[Monitor your Kubernetes cluster performance with Azure Monitor for containers](../../../azure-monitor/insights/container-insights-analyze.md).

To view the add-on logs, use `kubectl`:

```bash
# Get the azure-policy pod name installed in kube-system namespace
kubectl logs <azure-policy pod name> -n kube-system

# Get the gatekeeper pod name installed in gatekeeper-system namespace
kubectl logs <gatekeeper pod name> -n gatekeeper-system
```

For more information, see
[Debugging Gatekeeper](https://github.com/open-policy-agent/gatekeeper#debugging) in the Gatekeeper
documentation.

## Remove the add-on

### Remove the add-on from AKS

To remove the Azure Policy Add-on from your AKS cluster, use either the Azure portal or Azure CLI:

- Azure portal

  1. Launch the AKS service in the Azure portal by selecting **All services**, then searching for
     and selecting **Kubernetes services**.

  1. Select your AKS cluster where you want to disable the Azure Policy Add-on.

  1. Select **Policies** on the left side of the Kubernetes service page.

  1. In the main page, select the **Disable add-on** button.

- Azure CLI

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks disable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

### Remove the add-on from Azure Arc enabled Kubernetes

To remove the Azure Policy Add-on and Gatekeeper from your Azure Arc enabled Kubernetes cluster, run
the following Helm command:

```bash
helm uninstall azure-policy-addon
```

### Remove the add-on from AKS Engine

To remove the Azure Policy Add-on and Gatekeeper from your AKS Engine cluster, use the method that
aligns with how the add-on was installed:

- If installed by setting the **addons** property in the cluster definition for AKS Engine:

  Redeploy the cluster definition to AKS Engine after changing the **addons** property for
  _azure-policy_ to false:


  ```json
  "addons": [{
      "name": "azure-policy",
      "enabled": false
  }]
  ```

  For more information, see
  [AKS Engine - Disable Azure Policy Add-on](https://github.com/Azure/aks-engine/blob/master/examples/addons/azure-policy/README.md#disable-azure-policy-add-on).

- If installed with Helm Charts, run the following Helm command:

  ```bash
  helm uninstall azure-policy-addon
  ```

## Diagnostic data collected by Azure Policy Add-on

The Azure Policy Add-on for Kubernetes collects limited cluster diagnostic data. This diagnostic
data is vital technical data related to software and performance. It's used in the following ways:

- Keep Azure Policy Add-on up to date
- Keep Azure Policy Add-on secure, reliable, performant
- Improve Azure Policy Add-on - through the aggregate analysis of the use of the add-on

The information collected by the add-on isn't personal data. The following details are currently
collected:

- Azure Policy Add-on agent version
- Cluster type
- Cluster region
- Cluster resource group
- Cluster resource ID
- Cluster subscription ID
- Cluster OS (Example: Linux)
- Cluster city (Example: Seattle)
- Cluster state or province (Example: Washington)
- Cluster country or region (Example: United States)
- Exceptions/errors encountered by Azure Policy Add-on during agent installation on policy
  evaluation
- Number of Gatekeeper policy definitions not installed by Azure Policy Add-on

## Next steps

- Review examples at [Azure Policy samples](../samples/index.md).
- Review the [Policy definition structure](definition-structure.md).
- Review [Understanding policy effects](effects.md).
- Understand how to [programmatically create policies](../how-to/programmatically-create.md).
- Learn how to [get compliance data](../how-to/get-compliance-data.md).
- Learn how to [remediate non-compliant resources](../how-to/remediate-resources.md).
- Review what a management group is with [Organize your resources with Azure management groups](../../management-groups/overview.md).