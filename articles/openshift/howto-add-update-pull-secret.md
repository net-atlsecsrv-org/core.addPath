---
title: Add or update your Red Hat pull secret on an Azure Red Hat OpenShift 4 cluster
description: Add or update your Red Hat pull secret on existing 4.x ARO clusters
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 05/21/2020
keywords: pull secret, aro, openshift, red hat
#Customer intent: As a customer, I want to add or update my pull secret on an existing 4.x ARO cluster.
---

# Add or update your Red Hat pull secret on an Azure Red Hat OpenShift 4 cluster

This guide covers adding or updating your Red Hat pull secret for an existing Azure Red Hat OpenShift 4.x cluster.

If you're creating a cluster for the first time, then you can add your pull secret when you create your cluster. For more information about creating an ARO cluster with a Red Hat pull secret, see [Create an Azure Red Hat OpenShift 4 cluster](tutorial-create-cluster.md#get-a-red-hat-pull-secret-optional).

## Before you begin

This guide assumes you have an existing Azure Red Hat OpenShift 4 cluster. Ensure that you have administrator access to your cluster.

## Prepare your pull secret
When you create an ARO cluster without adding a Red Hat pull secret, a pull secret is still created on your cluster automatically. However, this pull secret isn't fully populated.

This section walks through updating that pull secret with additional values from your Red Hat pull secret.

1. Fetch the secret named `pull-secret` in the openshift-config namespace and save it to a separate file by running the following command: 

    ```console
    oc get secrets pull-secret -n openshift-config -o template='{{index .data ".dockerconfigjson"}}' | base64 -d > pull-secret.json
    ```

    Your output should be similar to the following (note that the actual secret value has been removed):

    ```json
    {
        "auths": {
            "arosvc.azurecr.io": {
                "auth": "<my-aroscv.azurecr.io-secret>"
            }
        }
    }
    ```

2. Navigate to your [Red Hat OpenShift cluster manager portal](https://cloud.redhat.com/openshift/install/azure/aro-provisioned) and click **Click Download pull secret.** Your Red Hat pull secret will look like the following (note that the actual secret values have been removed):

    ```json
    {
        "auths": {
            "cloud.openshift.com": {
                "auth": "<my-crc-secret>",
                "email": "klamenzo@redhat.com"
            },
            "quay.io": {
                "auth": "<my-quayio-secret>",
                "email": "klamenzo@redhat.com"
            },
            "registry.connect.redhat.com": {
                "auth": "<my-registry.connect.redhat.com-secret>",
                "email": "klamenzo@redhat.com"
            },
            "registry.redhat.io": {
                "auth": "<my-registry.redhat.io-secret>",
                "email": "klamenzo@redhat.com"
            }
        }
    }
    ```

3. Edit the pull secret file you got from your cluster by adding in the entries found in your Red Hat pull secret. 

    > [!IMPORTANT]
    > Including the the `cloud.openshift.com` entry from your Red Hat pull secret will cause your cluster to start sending telemetry data to Red Hat. Only include this section if you would like to send telemetry data. Otherwise, leave the following section out.
    > ```json
    > {
    >         "cloud.openshift.com": {
    >             "auth": "<my-crc-secret>",
    >             "email": "klamenzo@redhat.com"
    >         }
    > ```

    > [!CAUTION]
    > Do not remove or alter your the `arosvc.azurecr.io` entry from your pull secret. This section is needed for your cluster to function properly.
    ```json
    "arosvc.azurecr.io": {
                "auth": "<my-aroscv.azurecr.io-secret>"
            }
    ```

    Your final file should look like the following (note that the actual secret values have been removed):

    ```json
    {
        "auths": {
            "cloud.openshift.com": {
                "auth": "<my-crc-secret>",
                "email": "klamenzo@redhat.com"
            },
            "quay.io": {
                "auth": "<my-quayio-secret>",
                "email": "klamenzo@redhat.com"
            },
            "registry.connect.redhat.com": {
                "auth": "<my-registry.connect.redhat.com-secret>",
                "email": "klamenzo@redhat.com"
            },
            "registry.redhat.io": {
                "auth": "<my-registry.redhat.io-secret>",
                "email": "klamenzo@redhat.com"
            },
            "arosvc.azurecr.io": {
                "auth": "<my-aroscv.azurecr.io-secret>"
            }
        }
    }
    ```

4. Ensure the file is valid json. There are many ways to validate your json. The following example uses jq:
    ```json
    cat pull-secret.json | jq
    ```

    > [!NOTE]
    > If an error is in the file it can be seen `parse error`.

## Add your pull secret to your cluster

Run the following command to update your pull secret:

> [!NOTE]
> Running this command will cause your cluster nodes to restart one by one as they update. 

```console
oc set data secret/pull-secret -n openshift-config --from-file=.dockerconfigjson=./pull-secret.json
```

Once the secret is set, you are ready to enable Red Hat certified operators.

### Modify the configuration files

Modify the following objects to enable Red Hat Operators.

First, modify the Samples Operator configuration file. Then, you can run the following command to edit the configuration file:

```
oc edit configs.samples.operator.openshift.io/cluster -o yaml
```

Change the `spec.architectures.managementState` and the `status.architecture.managementState` values from `Removed` to `Managed`. 

The following YAML snippet shows only the relevant sections of the edited yaml file.

```yaml
apiVersion: samples.operator.openshift.io/v1
kind: Config
metadata:
  
  ...
  
spec:
  architectures:
  - x86_64
  managementState: Managed
status:
  architectures:

  ...

  managementState: Managed
  version: 4.3.27
```

Second, run the following command to edit the operator hub configuration file:  

```console
oc edit operatorhub cluster -o yaml
```

Change the `Spec.Sources.Disabled` and the `Status.Sources.Disabled` values from `true` to `false` for any sources you want enabled.

The following YAML snippet shows only the relevant sections of the edited yaml file.

```yaml
Name:         cluster

...
                 dd3310b9-e520-4a85-98e5-8b4779ee0f61
Spec:
  Sources:
    Disabled:  false
    Name:      certified-operators
    Disabled:  false
    Name:      redhat-operators
Status:
  Sources:
    Disabled:  false
    Name:      certified-operators
    Status:    Success
    Disabled:  false
    Name:      community-operators
    Status:    Success
    Disabled:  false
    Name:      redhat-operators
    Status:    Success
Events:        <none>
```

Save the file to apply your edits.

## Validate that your secret is working

After adding your pull secret and modifying the correct configuration files, your cluster can take several minutes to update. To check that your cluster has been updated, run the following command to show the Certified Operators and Red Hat Operators sources available:

```console
$ oc get catalogsource -A
NAMESPACE               NAME                  DISPLAY               TYPE   PUBLISHER   AGE
openshift-marketplace   certified-operators   Certified Operators   grpc   Red Hat     10s
openshift-marketplace   community-operators   Community Operators   grpc   Red Hat     18h
openshift-marketplace   redhat-operators      Red Hat Operators     grpc   Red Hat     11s
```

If you don't see the Certified Operators and Red Hat Operators, wait a few minutes and try again.

To ensure your pull secret has been updated and is working correctly, open OperatorHub and check for any Red Hat verified operator. For example, check to see if the OpenShift Container Storage operator is available, and see if you have permissions to install.

## Next steps
To learn more about Red Hat pull secrets, see [Using image pull secrets](https://docs.openshift.com/container-platform/4.5/openshift_images/managing_images/using-image-pull-secrets.html).

To learn more about Red Hat OpenShift 4, see [Azure Red Hat OpenShift 4](https://docs.openshift.com/aro/4/welcome/index.html).