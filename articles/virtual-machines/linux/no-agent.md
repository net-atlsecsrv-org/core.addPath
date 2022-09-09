---
title: Create Linux images without a provisioning agent 
description: Create generalized Linux images without a provisioning agent in Azure.
author: danielsollondon
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 07/06/2020
ms.author: danis
ms.reviewer: cynthn
---


# Creating generalized images without a provisioning agent

Microsoft Azure provides provisioning agents for Linux VMs in the form of the [walinuxagent](https://github.com/Azure/WALinuxAgent) or [cloud-init](https://github.com/canonical/cloud-init) (recommended). But there could be a scenario when you don't want to use either of these applications for your provisioning agent, such as:

- Your Linux distro/version does not support cloud-init/Linux Agent.
- You require specific VM properties to be set, such as hostname.

> [!NOTE] 
>
> If you do not require any properties to be set or any form of provisioning to happen you should consider creating a specialized image.

This article shows how you can setup your VM image to satisfy the Azure platform requirements and set the hostname, without installing a provisioning agent.

## Networking and reporting ready

In order to have your Linux VM communicating with Azure components, you will require a DHCP client to retrieve a host IP from the virtual network, as well as DNS resolution and route management. Most distros ship with these utilities out-of-the-box. Tools that have been tested on Azure by Linux distro vendors include `dhclient`, `network-manager`, `systemd-networkd` and others.

> [!NOTE]
>
> Currently creating generalized images without a provisioning agent only supports DHCP-enabled VMs.

After networking has been setup and configured, you must "report ready". This will tell Azure that the VM has been successfully provisioning.

> [!IMPORTANT]
>
> Failing to report ready to Azure will result in your VM being rebooted!

## Demo/sample

This demo will show how you can take an existing Marketplace image (in this case, a Debian Buster VM) and remove the Linux Agent (walinuxagent), but also creating the most basic process to report to Azure that the VM is "ready".

### Create the resource group and base VM:

```bash
$ az group create --location eastus --name demo1
```

Create the base VM:

```bash
$ az vm create \
    --resource-group demo1 \
    --name demo1 \
    --location eastus \
    --ssh-key-value <ssh_pub_key_path> \
    --public-ip-address-dns-name demo1 \
    --image "debian:debian-10:10:latest"
```

### Remove the image provisioning Agent

Once the VM is provisioning, you can SSH into it and remove the Linux Agent:

```bash
$ sudo apt purge -y waagent
$ sudo rm -rf /var/lib/waagent /etc/waagent.conf /var/log/waagent.log
```

### Add required code to the VM

Also inside the VM, because we've removed the Azure Linux Agent we need to provide a mechanism to report ready. 

#### Python script

```python
import http.client
import sys
from xml.etree import ElementTree

wireserver_ip = '168.63.129.16'
wireserver_conn = http.client.HTTPConnection(wireserver_ip)

print('Retrieving goal state from the Wireserver')
wireserver_conn.request(
    'GET',
    '/machine?comp=goalstate',
    headers={'x-ms-version': '2012-11-30'}
)

resp = wireserver_conn.getresponse()

if resp.status != 200:
    print('Unable to connect with wireserver')
    sys.exit(1)

wireserver_goalstate = resp.read().decode('utf-8')

xml_el = ElementTree.fromstring(wireserver_goalstate)

container_id = xml_el.findtext('Container/ContainerId')
instance_id = xml_el.findtext('Container/RoleInstanceList/RoleInstance/InstanceId')
print(f'ContainerId: {container_id}')
print(f'InstanceId: {instance_id}')

# Construct the XML response we need to send to Wireserver to report ready.
health = ElementTree.Element('Health')
goalstate_incarnation = ElementTree.SubElement(health, 'GoalStateIncarnation')
goalstate_incarnation.text = '1'
container = ElementTree.SubElement(health, 'Container')
container_id_el = ElementTree.SubElement(container, 'ContainerId')
container_id_el.text = container_id
role_instance_list = ElementTree.SubElement(container, 'RoleInstanceList')
role = ElementTree.SubElement(role_instance_list, 'Role')
instance_id_el = ElementTree.SubElement(role, 'InstanceId')
instance_id_el.text = instance_id
health_second = ElementTree.SubElement(role, 'Health')
state = ElementTree.SubElement(health_second, 'State')
state.text = 'Ready'

out_xml = ElementTree.tostring(
    health,
    encoding='unicode',
    method='xml'
)
print('Sending the following data to Wireserver:')
print(out_xml)

wireserver_conn.request(
    'POST',
    '/machine?comp=health',
    headers={
        'x-ms-version': '2012-11-30',
        'Content-Type': 'text/xml;charset=utf-8',
        'x-ms-agent-name': 'custom-provisioning'
    },
    body=out_xml
)

resp = wireserver_conn.getresponse()
print(f'Response: {resp.status} {resp.reason}')

wireserver_conn.close()
```

#### Generic steps (without using Python)

If your VM doesn't have Python installed or available, you can programmatically reproduce this above script logic with the following steps:

1. Retrieve the `ContainerId` and `InstanceId` by parsing the response from the WireServer: `curl -X GET -H 'x-ms-version: 2012-11-30' http://$168.63.129.16/machine?comp=goalstate`.

2. Construct the following XML data, injecting the parsed `ContainerId` and `InstanceId` from the above step:
   ```xml
   <Health>
     <GoalStateIncarnation>1</GoalStateIncarnation>
     <Container>
       <ContainerId>CONTAINER_ID</ContainerId>
       <RoleInstanceList>
         <Role>
           <InstanceId>INSTANCE_ID</InstanceId>
           <Health>
             <State>Ready</State>
           </Health>
         </Role>
       </RoleInstanceList>
     </Container>
   </Health>
   ```

3. Post this data to WireServer: `curl -X POST -H 'x-ms-version: 2012-11-30' -H "x-ms-agent-name: WALinuxAgent" -H "Content-Type: text/xml;charset=utf-8" -d "$REPORT_READY_XML" http://168.63.129.16/machine?comp=health`

### Automating running the code at first boot

This demo uses systemd, which is the most common init system in modern Linux distros. So the easiest and most native way to ensure this report ready mechanism runs at the right time is to create a systemd service unit. You can add the following unit file to `/etc/systemd/system` (this example names the unit file `azure-provisioning.service`):

```
[Unit]
Description=Azure Provisioning

[Service]
Type=oneshot
ExecStart=/usr/bin/python3 /usr/local/azure-provisioning.py
ExecStart=/bin/bash -c "hostnamectl set-hostname $(curl \
    -H 'metadata: true' \
    'http://169.254.169.254/metadata/instance/compute/name?api-version=2019-06-01&format=text')"
ExecStart=/usr/bin/systemctl disable azure-provisioning.service

[Install]
WantedBy=multi-user.target
```

This systemd service does three things for basic provisioning:

1. Reports ready to Azure (to indicate that it came up successfully).
1. Renames the VM based off of the user-supplied VM name by pulling this data from IMDS.
1. Disables itself so that it only runs on first boot and not on subsequent reboots.

With the unit on the filesystem, run the following to enable it:

```
$ sudo systemctl enable azure-provisioning.service
```

Now the VM is ready to be generalized and have an image created from it. 

#### Completing the preparation of the image

Back on your development machine, run the following to prepare for image creation from the base VM:

```
$ az vm deallocate --resource-group demo1 --name demo1
$ az vm generalize --resource-group demo1 --name demo1
```

And create the image from this VM:

```
$ az image create \
    --resource-group demo1 \
    --source demo1 \
    --location eastus \
    --name demo1img
```

Now we are ready to create a new VM (or multiple VMs) from the image:

```
$ IMAGE_ID=$(az image show -g demo1 -n demo1img --query id -o tsv)
$ az vm create \
    --resource-group demo12 \
    --name demo12 \
    --location eastus \
    --ssh-key-value <ssh_pub_key_path> \
    --public-ip-address-dns-name demo12 \
    --image "$IMAGE_ID" 
    --enable-agent false
```

> [!NOTE]
>
> It is important to set `--enable-agent` to `false` because walinuxagent doesn't exist on this VM that is going to be created from the image.

This VM should provisioning successfully. Logging into the newly-provisioning VM, you should be able to see the output of the report ready systemd service:

```
$ sudo journalctl -u azure-provisioning.service
-- Logs begin at Thu 2020-06-11 20:28:45 UTC, end at Thu 2020-06-11 20:31:24 UTC. --
Jun 11 20:28:49 thstringnopa systemd[1]: Starting Azure Provisioning...
Jun 11 20:28:54 thstringnopa python3[320]: Retrieving goal state from the Wireserver
Jun 11 20:28:54 thstringnopa python3[320]: ContainerId: 7b324f53-983a-43bc-b919-1775d6077608
Jun 11 20:28:54 thstringnopa python3[320]: InstanceId: fbb84507-46cd-4f4e-bd78-a2edaa9d059b._thstringnopa2
Jun 11 20:28:54 thstringnopa python3[320]: Sending the following data to Wireserver:
Jun 11 20:28:54 thstringnopa python3[320]: <Health><GoalStateIncarnation>1</GoalStateIncarnation><Container><ContainerId>7b324f53-983a-43bc-b919-1775d6077608</ContainerId><RoleInstanceList><Role><InstanceId>fbb84507-46cd-4f4e-bd78-a2edaa9d059b._thstringnopa2</InstanceId><Health><State>Ready</State></Health></Role></RoleInstanceList></Container></Health>
Jun 11 20:28:54 thstringnopa python3[320]: Response: 200 OK
Jun 11 20:28:56 thstringnopa bash[472]:   % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
Jun 11 20:28:56 thstringnopa bash[472]:                                  Dload  Upload   Total   Spent    Left  Speed
Jun 11 20:28:56 thstringnopa bash[472]: [158B blob data]
Jun 11 20:28:56 thstringnopa2 systemctl[475]: Removed /etc/systemd/system/multi-user.target.wants/azure-provisioning.service.
Jun 11 20:28:56 thstringnopa2 systemd[1]: azure-provisioning.service: Succeeded.
Jun 11 20:28:56 thstringnopa2 systemd[1]: Started Azure Provisioning.
```

## Support

If you implement your own provisioning code/agent, then you own the support of this code, Microsoft support will only investigate issues relating to the provisioning interfaces not being available. We are continually making improvements and changes in this area, so you must monitor for changes in cloud-init and Azure Linux Agent for provisioning API changes.

## Next steps

For more information, see [Linux provisioning](provisioning.md).
