---
title: Rotate certificates in Azure Kubernetes Service (AKS)
description: Learn how to rotate your certificates in an Azure Kubernetes Service (AKS) cluster.
services: container-service
author: zr-msft

ms.service: container-service
ms.topic: article
ms.date: 11/15/2019
ms.author: zarhoads
---

# Rotate certificates in Azure Kubernetes Service (AKS)

Azure Kubernetes Service (AKS) uses certificates for authentication with many of its components. Periodically, you may need to rotate those certificates for security or policy reasons. For example, you may have a policy to rotate all your certificates every 90 days.

This article shows you how to rotate the certificates in your AKS cluster.

## Before you begin

This article requires that you are running the Azure CLI version 2.0.76 or later. Run `az --version` to find the version. If you need to install or upgrade, see [Install Azure CLI][azure-cli-install].


### Install aks-preview CLI extension

To use this feature, you need the *aks-preview* CLI extension version 0.4.21 or higher. Install the *aks-preview* Azure CLI extension using the [az extension add][az-extension-add] command, then check for any available updates using the [az extension update][az-extension-update] command:

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## AKS certificates, Certificate Authorities, and Service Accounts

AKS generates and uses the following certificates, Certificate Authorities, and Service Accounts:

* The AKS API server creates a Certificate Authority (CA) called the Cluster CA.
* The API server has a Cluster CA, which signs certificates for one-way communication from the API server to kubelets.
* Each kubelet also creates a Certificate Signing Request (CSR), which is signed by the Cluster CA, for communication from the kubelet to the API server.
* The etcd key value store has a certificate signed by the Cluster CA for communication from etcd to the API server.
* The etcd key value store creates a CA that signs certificates to authenticate and authorize data replication between etcd replicas in the AKS cluster.
* The API aggregator uses the Cluster CA to issue certificates for communication with other APIs, such as Open Service Broker for Azure. The API aggregator can also have its own CA for issuing those certificates, but it currently uses the Cluster CA.
* Each node uses a Service Account (SA) token, which is signed by the Cluster CA.
* The `kubectl` client has a certificate for communicating with the AKS cluster.

> [!NOTE]
> AKS clusters created prior to March 2019 have certificates that expire after two years. Any cluster created after March 2019 or any cluster that has its certificates rotated have certificates that expire after 30 years.

## Rotate your cluster certificates

> [!WARNING]
> Rotating your certificates using `az aks rotate-certs` can cause up to 30 minutes of downtime for your AKS cluster.

Use [az aks get-credentials][az-aks-get-credentials] to sign in to your AKS cluster. This command also downloads and configures the `kubectl` client certificate on your local machine.

```console
az aks get-credentials -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME
```

Use `az aks rotate-certs` to rotate all certificates, CAs, and SAs on your cluster.

```console
az aks rotate-certs -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME
```

> [!IMPORTANT]
> It may take up to 30 minutes for `az aks rotate-certs` to complete. If the command fails before completing, use `az aks show` to verify the status of the cluster is *Certificate Rotating*. If the cluster is in a failed state, rerun `az aks rotate-certs` to rotate your certificates again.

Verify that the old certificates are no longer valid by running a `kubectl` command. Since you have not updated the certificates used by `kubectl`, you will see an error.  For example:

```console
$ kubectl get no
Unable to connect to the server: x509: certificate signed by unknown authority (possibly because of "crypto/rsa: verification error" while trying to verify candidate authority certificate "ca")
```

Update the certificate used by `kubectl` by running `az aks get-credentials`.

```console
az aks get-credentials -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME --overwrite-existing
```

Verify the certificates have been updated by running a `kubectl` command, which will now succeed. For example:

```console
kubectl get no
```

## Next steps

This article showed you how to automatically rotate your cluster's certificates, CAs, and SAs. You can see [Best practices for cluster security and upgrades in Azure Kubernetes Service (AKS)][aks-best-practices-security-upgrades] for more information on AKS security best practices.


[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[aks-best-practices-security-upgrades]: operator-best-practices-cluster-security.md
