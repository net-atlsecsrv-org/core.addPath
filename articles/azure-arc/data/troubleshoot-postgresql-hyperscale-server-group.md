---
title: Troubleshoot PostgreSQL Hyperscale server groups
description: Troubleshoot PostgreSQL Hyperscale server groups with a Jupyter Notebook
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
---

# Troubleshooting PostgreSQL Hyperscale server groups
This article describes some techniques you may use to troubleshoot your server group. In addition to this article you may want to read how to use [Kibana](monitor-grafana-kibana.md) to search the logs or use [Grafana](monitor-grafana-kibana.md) to visualize metrics about your server group. 

## Getting more details about the execution of an azdata command
You may add the parameter **--debug** to any azdata command you execute. Doing so will display to your console additional information about the execution of that command. You should find it useful to get details to help you understand the behavior of that command.
For example you could run
```console
azdata arc postgres server create -n postgres01 -w 2 --debug
```

or
```console
azdata arc postgres server edit -n postgres01 --extension SomeExtensionName --debug
```

In addition, you may use the parameter --help on any azdata command to display some help, list of parameters for a specific command. For example:
```console
azdata arc postgres server create --help
```


## Collecting logs of the data controller and your server groups
Read the article about [getting logs for Azure Arc enabled data services](troubleshooting-get-logs.md)



## Interactive troubleshooting with Jupyter notebooks in Azure Data Studio
Notebooks can document procedures by including markdown content to describe what to do/how to do it. It can also provide executable code to automate a procedure.  This pattern is useful for everything from standard operating procedures to troubleshooting guides.

For example, let's troubleshoot a PostgreSQL Hyperscale server group that might have some problems using Azure Data Studio.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

### Install tools

Install Azure Data Studio, `kubectl` and `azdata` on the client machine you are using to run the notebook in Azure Data Studio. To do this, please follow the instructions at [Install client tools](install-client-tools.md)

### Update the PATH environment variable

Make sure that these tools can be invoked from anywhere on this client machine. For example, on a Windows client machine, update the PATH system environment variable and add the folder in which you installed kubectl.

### Sign in with `azdata`

Sign in your Arc Data Controller from this client machine and before you launch Azure Data Studio. To do this, run a command like:

```console
azdata login --endpoint https://<IP address>:<port>
```

Replace `<IP address>` with the IP address of your Kubernetes cluster, and `<port>` the port on which Kubernetes is listening. You will be prompted for user name and password. To see more details, run:_

```console
azdata login --help
```

### Log into your Kubernetes cluster with kubectl

To do this, you may want to use the example commands provided in [this](https://blog.christianposta.com/kubernetes/logging-into-a-kubernetes-cluster-with-kubectl/) blog post.
You would run commands like:

```console
kubectl config view
kubectl config set-credentials kubeuser/my_kubeuser --username=<your Arc Data Controller Admin user name> --password=<password>
kubectl config set-cluster my_kubeuser --server=https://<IP address>:<port>
kubectl config set-context default/my_kubeuser/ArcDataControllerAdmin --user=ArcDataControllerAdmin/my_kubeuser --namespace=arc --cluster=my_kubeuser
kubectl config use-context default/my_kubeuser/ArcDataControllerAdmin
```

#### The troubleshooting notebook

Launch Azure Data Studio and open the troubleshooting notebook. 

Implement the steps described in  [033-manage-Postgres-with-AzureDataStudio.md](manage-postgresql-hyperscale-server-group-with-azure-data-studio.md) to:

1. Connect to your Arc Data Controller
2. Right select your Postgres instance and choose **[Manage]**
3. Select the **[Diagnose and solve problems] dashboard**
4. Select the **[Troubleshoot] link**

:::image type="content" source="media/postgres-hyperscale/ads-controller-postgres-troubleshooting-notebook.jpg" alt-text="Azure Data Studio - Open PostgreSQL troubleshooting Notebook":::

The **TSG100 - The Azure Arc enabled PostgreSQL Hyperscale troubleshooter notebook** opens up:
:::image type="content" source="media/postgres-hyperscale/ads-controller-postgres-troubleshooting-notebook2.jpg" alt-text="Azure Data Studio - Use PostgreSQL troubleshooting notebook":::

#### Run the scripts
Select the 'Run All' button at the top to execute the notebook all at once, or you can step through and execute each code cell one by one.

View the output from the execution of the code cells for any potential issues.

We'll add more details to the notebook over time about how to recognize common problems and how to solve them.

## Next step
- Read about [getting logs for Azure Arc enabled data services](troubleshooting-get-logs.md)
- Read about [searching logs with Kibana](monitor-grafana-kibana.md)
- Read about [monitoring with Grafana](monitor-grafana-kibana.md)
- Create your own notebooks
