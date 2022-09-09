---
title: Connect to Azure Arc enabled SQL Managed Instance
description: Connect to Azure Arc enabled SQL Managed Instance
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: vin-yu
ms.author: vinsonyu
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
---
# Connect to Azure Arc enabled SQL Managed Instance

This article explains how you can connect to your Azure Arc enabled SQL Managed Instance. 

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## View Azure Arc enabled SQL Managed Instances

To view the Azure Arc enabled SQL Managed Instance and the external endpoints use the following command:

```console
azdata arc sql mi list
```

Output should look like this:

```console
Name    Replicas    ExternalEndpoint    State
------  ----------  ----------------  -------
sqldemo 1/1         10.240.0.4:32023  Ready
```

If you are using AKS or kubeadm or OpenShift etc., you can copy the external IP and port number from here and connect to it using your favorite tool for connecting to a SQL Sever/Azure SQL instance such as Azure Data Studio or SQL Server Management Studio.  However, if you are using the quick start VM, see below for special information about how to connect to that VM from outside of Azure. 

> [!NOTE]
> Your corporate policies may block access to the IP and port, especially if this is created in the public cloud.

## Connect 

Connect with Azure Data Studio, SQL Server Management Studio, or SQLCMD

Open Azure Data Studio and connect to your instance with the external endpoint IP address and port number above. If you are using an Azure VM you will need the _public_ IP address, which is identifiable using the [Special note about Azure virtual machine deployments](#special-note-about-azure-virtual-machine-deployments).

For example:

- Server: 52.229.9.30,30913
- Username: sa
- Password: your specified SQL password at provisioning time

> [!NOTE]
> You can use Azure Data Studio [view the SQL managed instance dashboards](azure-data-studio-dashboards.md#view-the-sql-managed-instance-dashboards).

To connect using SQLCMD or Linux or Windows you can use a command like this. Enter the SQL password when prompted:

```bash
sqlcmd -S 52.229.9.30,30913 -U sa
```

## Special note about Azure virtual machine deployments

If you are using an Azure virtual machine, then the endpoint IP address will not show the public IP address. To locate the external IP address, use the following command:

```azurecli
az network public-ip list -g azurearcvm-rg --query "[].{PublicIP:ipAddress}" -o table
```

You can then combine the public IP address with the port to make your connection.

You may also need to expose the port of the sql instance through the network security gateway (NSG). To allow traffic through the (NSG) you will need to add a rule which you can do using the following command.

To set a rule you will need to know the name of your NSG which you can find out using the command below:

```azurecli
az network nsg list -g azurearcvm-rg --query "[].{NSGName:name}" -o table
```

Once you have the name of the NSG, you can add a firewall rule using the following command. The example values here create an NSG rule for port 30913 and allows connection from **any** source IP address.  This is not a security best practice!  You can lock things down better by specifying a -source-address-prefixes value that is specific to your client IP address or an IP address range that covers your team's or organization's IP addresses.

Replace the value of the `--destination-port-ranges` parameter below with the port number you got from the `azdata sql instance list`F command above.

```azurecli
az network nsg rule create -n db_port --destination-port-ranges 30913 --source-address-prefixes '*' --nsg-name azurearcvmNSG --priority 500 -g azurearcvm-rg --access Allow --description 'Allow port through for db access' --destination-address-prefixes '*' --direction Inbound --protocol Tcp --source-port-ranges '*'
```

## Next steps

- [View the SQL managed instance dashboards](azure-data-studio-dashboards.md#view-the-sql-managed-instance-dashboards)
- [View SQL Managed Instance in the Azure portal](view-arc-data-services-inventory-in-azure-portal.md)
