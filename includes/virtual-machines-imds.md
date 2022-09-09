---
author: LittleBoxOfSunshine
manager: KumariSupriya
ms.service: virtual-machines
ms.subservice: monitoring
ms.topic: include
ms.date: 01/04/2021
ms.author: chhenk
ms.reviewer: azmetadatadev
ms.custom: references_regions
---

The Azure Instance Metadata Service (IMDS) provides information about currently running virtual machine instances. You can use it to manage and configure your virtual machines.
This information includes the SKU, storage, network configurations, and upcoming maintenance events. For a complete list of the data available, see the [Endpoint Categories Summary](#endpoint-categories).

IMDS is available for running instances of virtual machines (VMs) and virtual machine scale set instances. All endpoints support VMs created and managed by using [Azure Resource Manager](/rest/api/resources/). Only
the Attested category and Network portion of the Instance category support VMs created by using the classic deployment model. The Attested endpoint does so only to a limited extent.

IMDS is a REST API that's available at a well-known, non-routable IP address (`169.254.169.254`). You can only access it from within the VM. Communication between the VM and IMDS never leaves the host.
Have your HTTP clients bypass web proxies within the VM when querying IMDS, and treat `169.254.169.254` the same as [`168.63.129.16`](../articles/virtual-network/what-is-ip-address-168-63-129-16.md).

## Usage

### Access Azure Instance Metadata Service

To access IMDS, create a VM from [Azure Resource Manager](/rest/api/resources/) or the [Azure portal](https://portal.azure.com), and use the following samples.
For more examples, see [Azure Instance Metadata Samples](https://github.com/microsoft/azureimds).

Here's sample code to retrieve all metadata for an instance. To access a specific data source, see [Endpoint Categories](#endpoint-categories) for an overview of all available features.

**Request**

> [!IMPORTANT]
> This example bypasses proxies. You **must** bypass proxies when querying IMDS. See [Proxies](#proxies) for additional information.

#### [Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance?api-version=2020-09-01" | ConvertTo-Json -Depth 64
```

#### [Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance?api-version=2020-09-01" | jq
```

---

**Response**

> [!NOTE]
> The response is a JSON string. The following example response is pretty-printed for readability.

[!INCLUDE [virtual-machines-imds-full-instance-response](./virtual-machines-imds-full-instance-response.md)]

## Security and authentication

The Instance Metadata Service is only accessible from within a running virtual machine instance on a non-routable IP address. VMs are limited to interacting with metadata/functionality that pertains to themselves. The API is HTTP only and never leaves the host.

In order to ensure that requests are directly intended for IMDS and prevent unintended or unwanted redirection of requests, requests:
- **Must** contain the header `Metadata: true`
- Must **not** contain an `X-Forwarded-For` header

Any request that does not meet **both** of these requirements will be rejected by the service.

> [!IMPORTANT]
> IMDS is **not** a channel for sensitive data. The API is unauthenticated and open to all processes on the VM. Information exposed through this service should be considered as shared information to all applications running inside the VM.

## Proxies

IMDS is **not** intended to be used behind a proxy and doing so is unsupported. Most HTTP clients provide an option for you to disable proxies on your requests, and this functionality must be utilized when communicating with IMDS. Consult your client's documentation for details.

> [!IMPORTANT]
> Even if you don't know of any proxy configuration in your environment, **you still must override any default client proxy settings**. Proxy configurations can be automatically discovered, and failing to bypass such configurations exposes you to outage risks should the machine's configuration be changed in the future.

## Rate limiting

In general, requests to IMDS are limited to 5 requests per second. Requests exceeding this threshold will be rejected with 429 responses. Requests to the [Managed Identity](#managed-identity) category are limited to 20 requests per second and 5 concurrent requests.

## HTTP verbs

The following HTTP verbs are currently supported:

| Verb | Description |
|------|-------------|
| `GET` | Retrieve  the requested resource

## Parameters

Endpoints may support required and/or optional parameters. See [Schema](#schema) and the documentation for the specific endpoint in question for details.

### Query parameters

IMDS endpoints support HTTP query string parameters. For example: 

```
http://169.254.169.254/metadata/instance/compute?api-version=2019-06-04&format=json
```

Specifies the parameters:

| Name | Value |
|------|-------|
| `api-version` | `2019-06-04`
| `format` | `json`

Requests with duplicate query parameter names will be rejected.

### Route parameters

For some endpoints that return larger json blobs, we support appending route parameters to the request endpoint to filter down to a subset of the response: 

```
http://169.254.169.254/metadata/<endpoint>/[<filter parameter>/...]?<query parameters>
```
The parameters correspond to the indexes/keys that would be used to walk down the json object were you interacting with a parsed representation.

For example, `/metatadata/instance` returns the json object:
```json
{
    "compute": { ... },
    "network": {
        "interface": [
            {
                "ipv4": {
                   "ipAddress": [{
                        "privateIpAddress": "10.144.133.132",
                        "publicIpAddress": ""
                    }],
                    "subnet": [{
                        "address": "10.144.133.128",
                        "prefix": "26"
                    }]
                },
                "ipv6": {
                    "ipAddress": [
                     ]
                },
                "macAddress": "0011AAFFBB22"
            },
            ...
        ]
    }
}
```

If we want to filter the response down to just the compute property, we would send the request: 
```
http://169.254.169.254/metadata/instance/compute?api-version=<version>
```

Similarly, if we want to filter to a nested property or specific array element we keep appending keys: 
```
http://169.254.169.254/metadata/instance/network/interface/0?api-version=<version>
```
would filter to the first element from the `Network.interface` property and return:

```json
{
    "ipv4": {
       "ipAddress": [{
            "privateIpAddress": "10.144.133.132",
            "publicIpAddress": ""
        }],
        "subnet": [{
            "address": "10.144.133.128",
            "prefix": "26"
        }]
    },
    "ipv6": {
        "ipAddress": [
         ]
    },
    "macAddress": "0011AAFFBB22"
}
```

> [!NOTE]
> When filtering to a leaf node, `format=json` doesn't work. For these queries `format=text` needs to be explicitly specified since the default format is json.

## Schema

### Data format

By default, IMDS returns data in JSON format (`Content-Type: application/json`). However, endpoints that support response filtering (see [Route Parameters](#route-parameters)) also support the format `text`.

To access a non-default response format, specify the requested format as a query string parameter in the request. For example:

#### [Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text"
```

#### [Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text"
```

---

In json responses, all primitives will be of type `string`, and missing or inapplicable values are always included but will be set to an empty string.

### Versioning

IMDS is versioned and specifying the API version in the HTTP request is mandatory. The only exception to this requirement is the [versions](#versions) endpoint, which can be used to dynamically retrieve the available API versions.

As newer versions are added, older versions can still be accessed for compatibility if your scripts have dependencies on specific data formats.

When you don't specify a version, you get an error with a list of the newest supported versions:
```json
{
    "error": "Bad request. api-version was not specified in the request. For more information refer to aka.ms/azureimds",
    "newest-versions": [
        "2020-10-01",
        "2020-09-01",
        "2020-07-15"
    ]
}
```

#### Supported API versions
- 2017-03-01
- 2017-04-02
- 2017-08-01 
- 2017-10-01
- 2017-12-01 
- 2018-02-01
- 2018-04-02
- 2018-10-01
- 2019-02-01
- 2019-03-11
- 2019-04-30
- 2019-06-01
- 2019-06-04
- 2019-08-01
- 2019-08-15
- 2019-11-01
- 2020-06-01
- 2020-07-15
- 2020-09-01
- 2020-10-01
- 2020-12-01

### Swagger

A full Swagger definition for IMDS is available at: https://github.com/Azure/azure-rest-api-specs/blob/master/specification/imds/data-plane/readme.md

## Regional availability

The service is **generally available** in all Azure Clouds.

## Root endpoint

The root endpoint is `http://169.254.169.254/metadata`.

## Endpoint categories

The IMDS API contains multiple endpoint categories representing different data sources, each of which contains one or more endpoints. See each category for details.

| Category root | Description | Version introduced |
|---------------|-------------|--------------------|
| `/metadata/attested` | See [Attested Data](#attested-data) | 2018-10-01
| `/metadata/identity` | See [Managed Identity via IMDS](#managed-identity) | 2018-02-01
| `/metadata/instance` | See [Instance Metadata](#instance-metadata) | 2017-04-02
| `/metadata/loadbalancer` | See [Retrieve Load Balancer metadata via IMDS](#load-balancer-metadata) | 2020-10-01
| `/metadata/scheduledevents` | See [Scheduled Events via IMDS](#scheduled-events) | 2017-08-01
| `/metadata/versions` | See [Versions](#versions) | N/A

## Versions

> [!NOTE]
> This feature was released alongside version 2020-10-01, which is currently being rolled out and may not yet be available in every region.

### List API versions

Returns the set of supported API versions.

```
GET /metadata/versions
```

#### Parameters

None (this endpoint is unversioned).

#### Response

```json
{
  "apiVersions": [
    "2017-03-01",
    "2017-04-02",
    ...
  ]
}
```

## Instance metadata

### Get VM metadata

Exposes the important metadata for the VM instance, including compute, network, and storage. 

```
GET /metadata/instance
```

#### Parameters

| Name | Required/Optional | Description |
|------|-------------------|-------------|
| `api-version` | Required | The version used to service the request.
| `format` | Optional* | The format (`json` or `text`) of the response. *Note: May be required when using request parameters

This endpoint supports response filtering via [route parameters](#route-parameters).

#### Response

[!INCLUDE [virtual-machines-imds-full-instance-response](./virtual-machines-imds-full-instance-response.md)]

Schema breakdown:

**Compute**

| Data | Description | Version introduced |
|------|-------------|--------------------|
| `azEnvironment` | Azure Environment where the VM is running in | 2018-10-01
| `customData` | This feature is currently disabled. We will update this documentation when it becomes available | 2019-02-01
| `evictionPolicy` | Sets how a [Spot VM](../articles/virtual-machines/spot-vms.md) will be evicted. | 2020-12-01
| `isHostCompatibilityLayerVm` | Identifies if the VM runs on the Host Compatibility Layer | 2020-06-01
| `licenseType` | Type of license for [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit). This is only present for AHB-enabled VMs | 2020-09-01
| `location` | Azure Region the VM is running in | 2017-04-02
| `name` | Name of the VM | 2017-04-02
| `offer` | Offer information for the VM image and is only present for images deployed from Azure image gallery | 2017-04-02
| `osProfile.adminUsername` | Specifies the name of the admin account | 2020-07-15
| `osProfile.computerName` | Specifies the name of the computer | 2020-07-15
| `osProfile.disablePasswordAuthentication` | Specifies if password authentication is disabled. This is only present for Linux VMs | 2020-10-01
| `osType` | Linux or Windows | 2017-04-02
| `placementGroupId` | [Placement Group](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md) of your virtual machine scale set | 2017-08-01
| `plan` | [Plan](/rest/api/compute/virtualmachines/createorupdate#plan) containing name, product, and publisher for a VM if it is an Azure Marketplace Image | 2018-04-02
| `platformUpdateDomain` |  [Update domain](../articles/virtual-machines/availability.md) the VM is running in | 2017-04-02
| `platformFaultDomain` | [Fault domain](../articles/virtual-machines/availability.md) the VM is running in | 2017-04-02
| `priority` | Priority of the VM. Refer to [Spot VMs](../articles/virtual-machines/spot-vms.md) for more information | 2020-12-01
| `provider` | Provider of the VM | 2018-10-01
| `publicKeys` | [Collection of Public Keys](/rest/api/compute/virtualmachines/createorupdate#sshpublickey) assigned to the VM and paths | 2018-04-02
| `publisher` | Publisher of the VM image | 2017-04-02
| `resourceGroupName` | [Resource group](../articles/azure-resource-manager/management/overview.md) for your Virtual Machine | 2017-08-01
| `resourceId` | The [fully qualified](/rest/api/resources/resources/getbyid) ID of the resource | 2019-03-11
| `sku` | Specific SKU for the VM image | 2017-04-02
| `securityProfile.secureBootEnabled` | Identifies if UEFI secure boot is enabled on the VM | 2020-06-01
| `securityProfile.virtualTpmEnabled` | Identifies if the virtual Trusted Platform Module (TPM) is enabled on the VM | 2020-06-01
| `storageProfile` | See Storage Profile below | 2019-06-01
| `subscriptionId` | Azure subscription for the Virtual Machine | 2017-08-01
| `tags` | [Tags](../articles/azure-resource-manager/management/tag-resources.md) for your Virtual Machine  | 2017-08-01
| `tagsList` | Tags formatted as a JSON array for easier programmatic parsing  | 2019-06-04
| `version` | Version of the VM image | 2017-04-02
| `vmId` | [Unique identifier](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) for the VM | 2017-04-02
| `vmScaleSetName` | [Virtual machine scale set Name](../articles/virtual-machine-scale-sets/overview.md) of your virtual machine scale set | 2017-12-01
| `vmSize` | [VM size](../articles/virtual-machines/sizes.md) | 2017-04-02
| `zone` | [Availability Zone](../articles/availability-zones/az-overview.md) of your virtual machine | 2017-12-01

**Storage profile**

The storage profile of a VM is divided into three categories: image reference, OS disk, and data disks.

The image reference object contains the following information about the OS image:

| Data | Description |
|------|-------------|
| `id` | Resource ID
| `offer` | Offer of the platform or marketplace image
| `publisher` | Image publisher
| `sku` | Image sku
| `version` | Version of the platform or marketplace image

The OS disk object contains the following information about the OS disk used by the VM:

| Data | Description |
|------|-------------|
| `caching` | Caching requirements
| `createOption` | Information about how the VM was created
| `diffDiskSettings` | Ephemeral disk settings
| `diskSizeGB` | Size of the disk in GB
| `image`   | Source user image virtual hard disk
| `lun`     | Logical unit number of the disk
| `managedDisk` | Managed disk parameters
| `name`    | Disk name
| `vhd`     | Virtual hard disk
| `writeAcceleratorEnabled` | Whether or not writeAccelerator is enabled on the disk

The data disks array contains a list of data disks attached to the VM. Each data disk object contains the following information:

Data | Description |
-----|-------------|
| `caching` | Caching requirements
| `createOption` | Information about how the VM was created
| `diffDiskSettings` | Ephemeral disk settings
| `diskSizeGB` | Size of the disk in GB
| `encryptionSettings` | Encryption settings for the disk
| `image` | Source user image virtual hard disk
| `managedDisk` | Managed disk parameters
| `name` | Disk name
| `osType` | Type of OS included in the disk
| `vhd` | Virtual hard disk
| `writeAcceleratorEnabled` | Whether or not writeAccelerator is enabled on the disk

**Network**

| Data | Description | Version introduced |
|------|-------------|--------------------|
| `ipv4.privateIpAddress` | Local IPv4 address of the VM | 2017-04-02
| `ipv4.publicIpAddress` | Public IPv4 address of the VM | 2017-04-02
| `subnet.address` | Subnet address of the VM | 2017-04-02
| `subnet.prefix` | Subnet prefix, example 24 | 2017-04-02
| `ipv6.ipAddress` | Local IPv6 address of the VM | 2017-04-02
| `macAddress` | VM mac address | 2017-04-02


#### Sample 1: Tracking VM running on Azure

As a service provider, you may require to track the number of VMs running your software or have agents that need to track uniqueness of the VM. To be able to get a unique ID for a VM, use the `vmId` field from Instance Metadata Service.

**Request**

#### [Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-08-01&format=text"
```

#### [Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-08-01&format=text"
```

---

**Response**

```
5c08b38e-4d57-4c23-ac45-aca61037f084
```

#### Sample 2: Placement of different data replicas

For certain scenarios, placement of different data replicas is of prime importance. For example, [HDFS replica placement](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html#Replica_Placement:_The_First_Baby_Steps)
or container placement via an [orchestrator](https://kubernetes.io/docs/user-guide/node-selection/) might require you to know the `platformFaultDomain` and `platformUpdateDomain` the VM is running on.
You can also use [Availability Zones](../articles/availability-zones/az-overview.md) for the instances to make these decisions.
You can query this data directly via IMDS.

**Request**

#### [Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text"
```

#### [Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text"
```

---

**Response**

```
0
```

#### Sample 3: Get VM tags

VM tags are included the instance API under instance/compute/tags endpoint.
Tags may have been applied to your Azure VM to logically organize them into a taxonomy. The tags assigned to a VM can be retrieved by using the request below.

**Request**

#### [Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance/compute/tags?api-version=2017-08-01&format=text"
```

#### [Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text"
```

---

**Response**

```
Department:IT;ReferenceNumber:123456;TestStatus:Pending
```

The `tags` field is a string with the tags delimited by semicolons. This output can be a problem if semicolons are used in the tags themselves. If a parser is written to programmatically extract the tags, you should rely on the `tagsList` field. The `tagsList` field is a JSON array with no delimiters, and consequently, easier to parse. The tagsList assigned to a VM can be retrieved by using the request below.

**Request**

#### [Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance/compute/tagsList?api-version=2019-06-04" | ConvertTo-Json -Depth 64
```

#### [Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/tagsList?api-version=2019-06-04" | jq
```

---

**Response**

#### [Windows](#tab/windows/)

```json
{
    "value":  [
                  {
                      "name":  "Department",
                      "value":  "IT"
                  },
                  {
                      "name":  "ReferenceNumber",
                      "value":  "123456"
                  },
                  {
                      "name":  "TestStatus",
                      "value":  "Pending"
                  }
              ],
    "Count":  3
}
```

#### [Linux](#tab/linux/)

```json
[
  {
    "name": "Department",
    "value": "IT"
  },
  {
    "name": "ReferenceNumber",
    "value": "123456"
  },
  {
    "name": "TestStatus",
    "value": "Pending"
  }
]
```

---


#### Sample 4: Get more information about the VM during support case

As a service provider, you may get a support call where you would like to know more information about the VM. Asking the customer to share the compute metadata can provide basic information for the support professional to know about the kind of VM on Azure.

**Request**

#### [Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance/compute?api-version=2020-09-01" | ConvertTo-Json -Depth 64
```

#### [Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute?api-version=2020-09-01"
```

---

**Response**

> [!NOTE]
> The response is a JSON string. The following example response is pretty-printed for readability.

#### [Windows](#tab/windows/)
```json
{
    "azEnvironment": "AZUREPUBLICCLOUD",
    "isHostCompatibilityLayerVm": "true",
    "licenseType":  "Windows_Client",
    "location": "westus",
    "name": "examplevmname",
    "offer": "WindowsServer",
    "osProfile": {
        "adminUsername": "admin",
        "computerName": "examplevmname",
        "disablePasswordAuthentication": "true"
    },
    "osType": "Windows",
    "placementGroupId": "f67c14ab-e92c-408c-ae2d-da15866ec79a",
    "plan": {
        "name": "planName",
        "product": "planProduct",
        "publisher": "planPublisher"
    },
    "platformFaultDomain": "36",
    "platformUpdateDomain": "42",
    "publicKeys": [{
            "keyData": "ssh-rsa 0",
            "path": "/home/user/.ssh/authorized_keys0"
        },
        {
            "keyData": "ssh-rsa 1",
            "path": "/home/user/.ssh/authorized_keys1"
        }
    ],
    "publisher": "RDFE-Test-Microsoft-Windows-Server-Group",
    "resourceGroupName": "macikgo-test-may-23",
    "resourceId": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/virtualMachines/examplevmname",
    "securityProfile": {
        "secureBootEnabled": "true",
        "virtualTpmEnabled": "false"
    },
    "sku": "2019-Datacenter",
    "storageProfile": {
        "dataDisks": [{
            "caching": "None",
            "createOption": "Empty",
            "diskSizeGB": "1024",
            "image": {
                "uri": ""
            },
            "lun": "0",
            "managedDisk": {
                "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampledatadiskname",
                "storageAccountType": "Standard_LRS"
            },
            "name": "exampledatadiskname",
            "vhd": {
                "uri": ""
            },
            "writeAcceleratorEnabled": "false"
        }],
        "imageReference": {
            "id": "",
            "offer": "WindowsServer",
            "publisher": "MicrosoftWindowsServer",
            "sku": "2019-Datacenter",
            "version": "latest"
        },
        "osDisk": {
            "caching": "ReadWrite",
            "createOption": "FromImage",
            "diskSizeGB": "30",
            "diffDiskSettings": {
                "option": "Local"
            },
            "encryptionSettings": {
                "enabled": "false"
            },
            "image": {
                "uri": ""
            },
            "managedDisk": {
                "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampleosdiskname",
                "storageAccountType": "Standard_LRS"
            },
            "name": "exampleosdiskname",
            "osType": "Windows",
            "vhd": {
                "uri": ""
            },
            "writeAcceleratorEnabled": "false"
        }
    },
    "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "tags": "baz:bash;foo:bar",
    "version": "15.05.22",
    "vmId": "02aab8a4-74ef-476e-8182-f6d2ba4166a6",
    "vmScaleSetName": "crpteste9vflji9",
    "vmSize": "Standard_A3",
    "zone": ""
}
```

#### [Linux](#tab/linux/)
```json
{
    "azEnvironment": "AZUREPUBLICCLOUD",
    "isHostCompatibilityLayerVm": "true",
    "licenseType":  "Windows_Client",
    "location": "westus",
    "name": "examplevmname",
    "offer": "UbuntuServer",
    "osProfile": {
        "adminUsername": "admin",
        "computerName": "examplevmname",
        "disablePasswordAuthentication": "true"
    },
    "osType": "Linux",
    "placementGroupId": "f67c14ab-e92c-408c-ae2d-da15866ec79a",
    "plan": {
        "name": "planName",
        "product": "planProduct",
        "publisher": "planPublisher"
    },
    "platformFaultDomain": "36",
    "platformUpdateDomain": "42",
    "publicKeys": [{
            "keyData": "ssh-rsa 0",
            "path": "/home/user/.ssh/authorized_keys0"
        },
        {
            "keyData": "ssh-rsa 1",
            "path": "/home/user/.ssh/authorized_keys1"
        }
    ],
    "publisher": "Canonical",
    "resourceGroupName": "macikgo-test-may-23",
    "resourceId": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/virtualMachines/examplevmname",
    "securityProfile": {
        "secureBootEnabled": "true",
        "virtualTpmEnabled": "false"
    },
    "sku": "18.04-LTS",
    "storageProfile": {
        "dataDisks": [{
            "caching": "None",
            "createOption": "Empty",
            "diskSizeGB": "1024",
            "image": {
                "uri": ""
            },
            "lun": "0",
            "managedDisk": {
                "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampledatadiskname",
                "storageAccountType": "Standard_LRS"
            },
            "name": "exampledatadiskname",
            "vhd": {
                "uri": ""
            },
            "writeAcceleratorEnabled": "false"
        }],
        "imageReference": {
            "id": "",
            "offer": "UbuntuServer",
            "publisher": "Canonical",
            "sku": "16.04.0-LTS",
            "version": "latest"
        },
        "osDisk": {
            "caching": "ReadWrite",
            "createOption": "FromImage",
            "diskSizeGB": "30",
            "diffDiskSettings": {
                "option": "Local"
            },
            "encryptionSettings": {
                "enabled": "false"
            },
            "image": {
                "uri": ""
            },
            "managedDisk": {
                "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampleosdiskname",
                "storageAccountType": "Standard_LRS"
            },
            "name": "exampleosdiskname",
            "osType": "linux",
            "vhd": {
                "uri": ""
            },
            "writeAcceleratorEnabled": "false"
        }
    },
    "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "tags": "baz:bash;foo:bar",
    "version": "15.05.22",
    "vmId": "02aab8a4-74ef-476e-8182-f6d2ba4166a6",
    "vmScaleSetName": "crpteste9vflji9",
    "vmSize": "Standard_A3",
    "zone": ""
}
```

---

#### Sample 5: Get the Azure Environment where the VM is running

Azure has various sovereign clouds like [Azure Government](https://azure.microsoft.com/overview/clouds/government/). Sometimes you need the Azure Environment to make some runtime decisions. The following sample shows you how you can achieve this behavior.

**Request**

#### [Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance/compute/azEnvironment?api-version=2018-10-01&format=text"
```

#### [Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/azEnvironment?api-version=2018-10-01&format=text"
```

---

**Response**

```
AzurePublicCloud
```

The cloud and the values of the Azure environment are listed here.

| Cloud | Azure environment |
|-------|-------------------|
| [All generally available global Azure regions](https://azure.microsoft.com/regions/) | AzurePublicCloud
| [Azure Government](https://azure.microsoft.com/overview/clouds/government/) | AzureUSGovernmentCloud
| [Azure China 21Vianet](https://azure.microsoft.com/global-infrastructure/china/) | AzureChinaCloud
| [Azure Germany](https://azure.microsoft.com/overview/clouds/germany/) | AzureGermanCloud


#### Sample 6: Retrieve network information

**Request**

#### [Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance/network?api-version=2017-08-01" | ConvertTo-Json  -Depth 64
```

#### [Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/network?api-version=2017-08-01"
```

---

**Response**

```json
{
  "interface": [
    {
      "ipv4": {
        "ipAddress": [
          {
            "privateIpAddress": "10.1.0.4",
            "publicIpAddress": "X.X.X.X"
          }
        ],
        "subnet": [
          {
            "address": "10.1.0.0",
            "prefix": "24"
          }
        ]
      },
      "ipv6": {
        "ipAddress": []
      },
      "macAddress": "000D3AF806EC"
    }
  ]
}
```

#### Sample 7: Retrieve public IP address

#### [Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-08-01&format=text"
```

#### [Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-08-01&format=text"
```

---

## Attested data

### Get Attested data

IMDS helps to provide guarantees that the data provided is coming from Azure. Microsoft signs part of this information, so you can confirm that an image in Azure Marketplace is the one you are running on Azure.

```
GET /metadata/attested/document
```

#### Parameters

| Name | Required/Optional | Description |
|------|-------------------|-------------|
| `api-version` | Required | The version used to service the request.
| `nonce` | Optional | A 10-digit string that serves as a cryptographic nonce. If no value is provided, IMDS uses the current UTC timestamp.

#### Response

```json
{
    "encoding":"pkcs7",
    "signature":"MIIEEgYJKoZIhvcNAQcCoIIEAzCCA/8CAQExDzANBgkqhkiG9w0BAQsFADCBugYJKoZIhvcNAQcBoIGsBIGpeyJub25jZSI6IjEyMzQ1NjY3NjYiLCJwbGFuIjp7Im5hbWUiOiIiLCJwcm9kdWN0IjoiIiwicHVibGlzaGVyIjoiIn0sInRpbWVTdGFtcCI6eyJjcmVhdGVkT24iOiIxMS8yMC8xOCAyMjowNzozOSAtMDAwMCIsImV4cGlyZXNPbiI6IjExLzIwLzE4IDIyOjA4OjI0IC0wMDAwIn0sInZtSWQiOiIifaCCAj8wggI7MIIBpKADAgECAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBBAUAMCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tMB4XDTE4MTEyMDIxNTc1N1oXDTE4MTIyMDIxNTc1NlowKzEpMCcGA1UEAxMgdGVzdHN1YmRvbWFpbi5tZXRhZGF0YS5henVyZS5jb20wgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAML/tBo86ENWPzmXZ0kPkX5dY5QZ150mA8lommszE71x2sCLonzv4/UWk4H+jMMWRRwIea2CuQ5RhdWAHvKq6if4okKNt66fxm+YTVz9z0CTfCLmLT+nsdfOAsG1xZppEapC0Cd9vD6NCKyE8aYI1pliaeOnFjG0WvMY04uWz2MdAgMBAAGjYDBeMFwGA1UdAQRVMFOAENnYkHLa04Ut4Mpt7TkJFfyhLTArMSkwJwYDVQQDEyB0ZXN0c3ViZG9tYWluLm1ldGFkYXRhLmF6dXJlLmNvbYIQZ8VuSofHbJRAQNBNpiASdDANBgkqhkiG9w0BAQQFAAOBgQCLSM6aX5Bs1KHCJp4VQtxZPzXF71rVKCocHy3N9PTJQ9Fpnd+bYw2vSpQHg/AiG82WuDFpPReJvr7Pa938mZqW9HUOGjQKK2FYDTg6fXD8pkPdyghlX5boGWAMMrf7bFkup+lsT+n2tRw2wbNknO1tQ0wICtqy2VqzWwLi45RBwTGB6DCB5QIBATA/MCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBCwUAMA0GCSqGSIb3DQEBAQUABIGAld1BM/yYIqqv8SDE4kjQo3Ul/IKAVR8ETKcve5BAdGSNkTUooUGVniTXeuvDj5NkmazOaKZp9fEtByqqPOyw/nlXaZgOO44HDGiPUJ90xVYmfeK6p9RpJBu6kiKhnnYTelUk5u75phe5ZbMZfBhuPhXmYAdjc7Nmw97nx8NnprQ="
}
```

> [!NOTE]
> Due to IMDS's caching mechanism, a previously cached nonce value may be returned.

The signature blob is a [pkcs7](https://aka.ms/pkcs7)-signed version of document. It contains the certificate used for signing along with certain VM-specific details.

For VMs created by using Azure Resource Manager, the document includes `vmId`, `sku`, `nonce`, `subscriptionId`, `timeStamp` for creation and expiry of the document, and the plan information about the image. The plan information is only populated for Azure Marketplace images.

For VMs created by using the classic deployment model, only the `vmId` is guaranteed to be populated. You can extract the certificate from the response, and use it to confirm that the response is valid and is coming from Azure.

The decoded document contains the following fields:

| Data | Description | Version introduced |
|------|-------------|--------------------|
| `licenseType` | Type of license for [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit). This is only present for AHB-enabled VMs. | 2020-09-01
| `nonce` | A string that can be optionally provided with the request. If no `nonce` was supplied, the current Coordinated Universal Time timestamp is used. | 2018-10-01
| `plan` | The [Azure Marketplace Image plan](/rest/api/compute/virtualmachines/createorupdate#plan). Contains the plan ID (name), product image or offer (product), and publisher ID (publisher). | 2018-10-01
| `timestamp.createdOn` | The UTC timestamp for when the signed document was created | 2018-20-01
| `timestamp.expiresOn` | The UTC timestamp for when the signed document expires | 2018-10-01
| `vmId` | [Unique identifier](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) for the VM | 2018-10-01
| `subscriptionId` | Azure subscription for the Virtual Machine | 2019-04-30
| `sku` | Specific SKU for the VM image | 2019-11-01

> [!NOTE]
> For Classic (non-Azure Resource Manager) VMs, only the vmId is guaranteed to be populated.

Example document:
```json
{
   "nonce":"20201130-211924",
   "plan":{
      "name":"planName",
      "product":"planProduct",
      "publisher":"planPublisher"
   },
   "sku":"Windows-Server-2012-R2-Datacenter",
   "subscriptionId":"8d10da13-8125-4ba9-a717-bf7490507b3d",
   "timeStamp":{
      "createdOn":"11/30/20 21:19:19 -0000",
      "expiresOn":"11/30/20 21:19:24 -0000"
   },
   "vmId":"02aab8a4-74ef-476e-8182-f6d2ba4166a6"
}
```

#### Sample 1: Validate that the VM is running in Azure

Vendors in Azure Marketplace want to ensure that their software is licensed to run only in Azure. If someone copies the VHD to an on-premises environment, the vendor needs to be able to detect that. Through IMDS, these vendors can get signed data that guarantees response only from Azure.

> [!NOTE]
> This sample requires the jq utility to be installed.

**Validation**

#### [Windows](#tab/windows/)

```powershell
# Get the signature
$attestedDoc = Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri http://169.254.169.254/metadata/attested/document?api-version=2020-09-01
# Decode the signature
$signature = [System.Convert]::FromBase64String($attestedDoc.signature)
```

Verify that the signature is from Microsoft Azure and check the certificate chain for errors.

```powershell
# Get certificate chain
$cert = [System.Security.Cryptography.X509Certificates.X509Certificate2]($signature)
$chain = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Chain
$chain.Build($cert)
# Print the Subject of each certificate in the chain
foreach($element in $chain.ChainElements)
{
    Write-Host $element.Certificate.Subject
}

# Get the content of the signed document
Add-Type -AssemblyName System.Security
$signedCms = New-Object -TypeName System.Security.Cryptography.Pkcs.SignedCms
$signedCms.Decode($signature);
$content = [System.Text.Encoding]::UTF8.GetString($signedCms.ContentInfo.Content)
Write-Host "Attested data: " $content
$json = $content | ConvertFrom-Json
# Do additional validation here
```

#### [Linux](#tab/linux/)

```bash
# Get the signature
curl --silent -H Metadata:True --noproxy "*" "http://169.254.169.254/metadata/attested/document?api-version=2020-09-01" | jq -r '.["signature"]' > signature
# Decode the signature
base64 -d signature > decodedsignature
# Get PKCS7 format
openssl pkcs7 -in decodedsignature -inform DER -out sign.pk7
# Get Public key out of pkc7
openssl pkcs7 -in decodedsignature -inform DER  -print_certs -out signer.pem
# Get the intermediate certificate
curl -s -o intermediate.cer "$(openssl x509 -in signer.pem -text -noout | grep " CA Issuers -" | awk -FURI: '{print $2}')"
openssl x509 -inform der -in intermediate.cer -out intermediate.pem
# Verify the contents
openssl smime -verify -in sign.pk7 -inform pem -noverify
```

**Results**

```json
Verification successful
{
  "nonce": "20181128-001617",
  "plan":
    {
      "name": "",
      "product": "",
      "publisher": ""
    },
  "timeStamp":
    {
      "createdOn": "11/28/18 00:16:17 -0000",
      "expiresOn": "11/28/18 06:16:17 -0000"
    },
  "vmId": "d3e0e374-fda6-4649-bbc9-7f20dc379f34",
  "licenseType": "Windows_Client",  
  "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
  "sku": "RS3-Pro"
}
```

Verify that the signature is from Microsoft Azure, and check the certificate chain for errors.

```bash
# Verify the subject name for the main certificate
openssl x509 -noout -subject -in signer.pem
# Verify the issuer for the main certificate
openssl x509 -noout -issuer -in signer.pem
#Validate the subject name for intermediate certificate
openssl x509 -noout -subject -in intermediate.pem
# Verify the issuer for the intermediate certificate
openssl x509 -noout -issuer -in intermediate.pem
# Verify the certificate chain, for Azure China 21Vianet the intermediate certificate will be from DigiCert Global Root CA
openssl verify -verbose -CAfile /etc/ssl/certs/Baltimore_CyberTrust_Root.pem -untrusted intermediate.pem signer.pem
```

---

> [!NOTE]
> Due to IMDS's caching mechanism, a previously cached `nonce` value might be returned.

The `nonce` in the signed document can be compared if you provided a `nonce` parameter in the initial request.

> [!NOTE]
> The certificate for the public cloud and each sovereign cloud will be different.

| Cloud | Certificate |
|-------|-------------|
| [All generally available global Azure regions](https://azure.microsoft.com/regions/) | *.metadata.azure.com
| [Azure Government](https://azure.microsoft.com/overview/clouds/government/) | *.metadata.azure.us
| [Azure China 21Vianet](https://azure.microsoft.com/global-infrastructure/china/) | *.metadata.azure.cn
| [Azure Germany](https://azure.microsoft.com/overview/clouds/germany/) | *.metadata.microsoftazure.de

> [!NOTE]
> The certificates might not have an exact match of `metadata.azure.com` for the public cloud. For this reason, the certification validation should allow a common name from any `.metadata.azure.com` subdomain.

In cases where the intermediate certificate can't be downloaded due to network constraints during validation, you can pin the intermediate certificate. Azure rolls over the certificates, which is standard PKI practice. You must update the pinned certificates when rollover happens. Whenever a change to update the intermediate certificate is planned, the Azure blog is updated, and Azure customers are notified. 

You can find the intermediate certificates in the [PKI repository](https://www.microsoft.com/pki/mscorp/cps/default.htm). The intermediate certificates for each of the regions can be different.

> [!NOTE]
> The intermediate certificate for Azure China 21Vianet will be from DigiCert Global Root CA, instead of Baltimore.
If you pinned the intermediate certificates for Azure China as part of a root chain authority change, the intermediate certificates must be updated.


## Managed identity

A managed identity, assigned by the system, can be enabled on the VM. You can also assign one or more user-assigned managed identities to the VM.
You can then request tokens for managed identities from IMDS. Use these tokens to authenticate with other Azure services, such as Azure Key Vault.

For detailed steps to enable this feature, see [Acquire an access token](../articles/active-directory/managed-identities-azure-resources/how-to-use-vm-token.md).

## Load Balancer Metadata
When you place virtual machine or virtual machine set instances behind an Azure Standard Load Balancer, you can use IMDS to retrieve metadata related to the load balancer and the instances. For more information, see [Retrieve load balancer information](../articles/load-balancer/instance-metadata-service-load-balancer.md).

## Scheduled events
You can obtain the status of the scheduled events by using IMDS. Then the user can specify a set of actions to run upon these events. For more information, see [Scheduled events for Linux](../articles/virtual-machines/linux/scheduled-events.md) or [Scheduled events for Windows](../articles/virtual-machines/windows/scheduled-events.md).


## Sample code in different languages

The following table lists samples of calling IMDS by using different languages inside the VM:

| Language | Example |
|----------|---------|
| Bash | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.sh
| C# | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.cs
| Go | https://github.com/Microsoft/azureimds/blob/master/imdssample.go
| Java | https://github.com/Microsoft/azureimds/blob/master/imdssample.java
| NodeJS | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.js
| Perl | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.pl
| PowerShell | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.ps1
| Puppet | https://github.com/keirans/azuremetadata
| Python | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.py
| Ruby | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.rb

## Errors and debugging

If there is a data element not found or a malformed request, the Instance Metadata Service returns standard HTTP errors. For example:

| HTTP status code | Reason |
|------------------|--------|
| `200 OK` | The request was successful.
| `400 Bad Request` | Missing `Metadata: true` header or missing parameter `format=json` when querying a leaf node
| `404 Not Found` | The requested element doesn't exist
| `405 Method Not Allowed` | The HTTP method (verb) is not supported on the endpoint.
| `410 Gone` | Retry after some time for a max of 70 seconds
| `429 Too Many Requests` | API [Rate Limits](#rate-limiting) has been exceeded
| `500 Service Error` | Retry after some time

## Frequently asked questions

- I am getting the error `400 Bad Request, Required metadata header not specified`. What does this mean?
  - IMDS requires the header `Metadata: true` to be passed in the request. Passing this header in the REST call allows access to IMDS.

- Why am I not getting compute information for my VM?
  - Currently, IMDS only supports instances created with Azure Resource Manager.

- I created my VM through Azure Resource Manager some time ago. Why am I not seeing compute metadata information?
  - If you created your VM after September 2016, add a [tag](../articles/azure-resource-manager/management/tag-resources.md) to start seeing compute metadata. If you created your VM before September 2016, add or remove extensions or data disks to the VM instance to refresh metadata.

- Why am I not seeing all data populated for a new version?
  - If you created your VM after September 2016, add a [tag](../articles/azure-resource-manager/management/tag-resources.md) to start seeing compute metadata. If you created your VM before September 2016, add or remove extensions or data disks to the VM instance to refresh metadata.

- Why am I getting the error `500 Internal Server Error` or `410 Resource Gone`?
  - Retry your request. For more information, see [Transient fault handling](/azure/architecture/best-practices/transient-faults). If the problem persists, create a support issue in the Azure portal for the VM.

- Would this work for virtual machine scale set instances?
  - Yes, IMDS is available for virtual machine scale set instances.

- I updated my tags in virtual machine scale sets, but they don't appear in the instances (unlike single instance VMs). Am I doing something wrong?
  - Currently tags for virtual machine scale sets only show to the VM on a reboot, reimage, or disk change to the instance.

- Why am I am not seeing the SKU information for my VM in `instance/compute` details?
  - For custom images created from Azure Marketplace, Azure platform doesn't retain the SKU information for the custom image and the details for any VMs created from the custom image. This is by design and hence not surfaced in the VM `instance/compute` details.

- Why is my request timed out for my call to the service?
  - Metadata calls must be made from the primary IP address assigned to the primary network card of the VM. Additionally, if you've changed your routes, there must be a route for the 169.254.169.254/32 address in your VM's local routing table.

    ### [Windows](#tab/windows/)

    1. Dump your local routing table and look for the IMDS entry. For example:
        ```console
        > route print
        IPv4 Route Table
        ===========================================================================
        Active Routes:
        Network Destination        Netmask          Gateway       Interface  Metric
                0.0.0.0          0.0.0.0      172.16.69.1      172.16.69.7     10
                127.0.0.0        255.0.0.0         On-link         127.0.0.1    331
                127.0.0.1  255.255.255.255         On-link         127.0.0.1    331
        127.255.255.255  255.255.255.255         On-link         127.0.0.1    331
            168.63.129.16  255.255.255.255      172.16.69.1      172.16.69.7     11
        169.254.169.254  255.255.255.255      172.16.69.1      172.16.69.7     11
        ... (continues) ...
        ```
    1. Verify that a route exists for `169.254.169.254`, and note the corresponding network interface (for example, `172.16.69.7`).
    1. Dump the interface configuration and find the interface that corresponds to the one referenced in the routing table, noting the MAC (physical) address.
        ```console
        > ipconfig /all
        ... (continues) ...
        Ethernet adapter Ethernet:

        Connection-specific DNS Suffix  . : xic3mnxjiefupcwr1mcs1rjiqa.cx.internal.cloudapp.net
        Description . . . . . . . . . . . : Microsoft Hyper-V Network Adapter
        Physical Address. . . . . . . . . : 00-0D-3A-E5-1C-C0
        DHCP Enabled. . . . . . . . . . . : Yes
        Autoconfiguration Enabled . . . . : Yes
        Link-local IPv6 Address . . . . . : fe80::3166:ce5a:2bd5:a6d1%3(Preferred)
        IPv4 Address. . . . . . . . . . . : 172.16.69.7(Preferred)
        Subnet Mask . . . . . . . . . . . : 255.255.255.0
        ... (continues) ...
        ```
    1. Confirm that the interface corresponds to the VM's primary NIC and primary IP. You can find the primary NIC and IP by looking at the network configuration in the Azure portal, or by looking it up with the Azure CLI. Note the private IPs (and the MAC address if you're using the CLI). Here's a PowerShell CLI example:
        ```powershell
        $ResourceGroup = '<Resource_Group>'
        $VmName = '<VM_Name>'
        $NicNames = az vm nic list --resource-group $ResourceGroup --vm-name $VmName | ConvertFrom-Json | Foreach-Object { $_.id.Split('/')[-1] }
        foreach($NicName in $NicNames)
        {
            $Nic = az vm nic show --resource-group $ResourceGroup --vm-name $VmName --nic $NicName | ConvertFrom-Json
            Write-Host $NicName, $Nic.primary, $Nic.macAddress
        }
        # Output: wintest767 True 00-0D-3A-E5-1C-C0
        ```
    1. If they don't match, update the routing table so that the primary NIC and IP are targeted.

    ### [Linux](#tab/linux/)

    1. Dump your local routing table with a command such as `netstat -r` and look for the IMDS entry (e.g.):
        ```console
        ~$ netstat -r
        Kernel IP routing table
        Destination     Gateway         Genmask         Flags   MSS Window  irtt Iface
        default         _gateway        0.0.0.0         UG        0 0          0 eth0
        168.63.129.16   _gateway        255.255.255.255 UGH       0 0          0 eth0
        169.254.169.254 _gateway        255.255.255.255 UGH       0 0          0 eth0
        172.16.69.0     0.0.0.0         255.255.255.0   U         0 0          0 eth0
        ```
    1. Verify that a route exists for `169.254.169.254`, and note the corresponding network interface (e.g. `eth0`).
    1. Dump the interface configuration for the corresponding interface in the routing table (note the exact name of the configuration file may vary)
        ```console
        ~$ cat /etc/netplan/50-cloud-init.yaml
        network:
        ethernets:
            eth0:
                dhcp4: true
                dhcp4-overrides:
                    route-metric: 100
                dhcp6: false
                match:
                    macaddress: 00:0d:3a:e4:c7:2e
                set-name: eth0
        version: 2
        ```
    1. If you are using a dynamic IP, note the MAC address. If you are using a static IP, you may note the listed IP(s) and/or the MAC address.
    1. Confirm that the interface corresponds to the VM's primary NIC and primary IP. You can find the primary NIC and IP by looking at the network configuration in the Azure portal, or by looking it up with the Azure CLI. Note the private IPs (and the MAC address if you're using the CLI). Here's a PowerShell CLI example:
        ```powershell
        $ResourceGroup = '<Resource_Group>'
        $VmName = '<VM_Name>'
        $NicNames = az vm nic list --resource-group $ResourceGroup --vm-name $VmName | ConvertFrom-Json | Foreach-Object { $_.id.Split('/')[-1] }
        foreach($NicName in $NicNames)
        {
            $Nic = az vm nic show --resource-group $ResourceGroup --vm-name $VmName --nic $NicName | ConvertFrom-Json
            Write-Host $NicName, $Nic.primary, $Nic.macAddress
        }
        # Output: ipexample606 True 00-0D-3A-E4-C7-2E
        ```
    1. If they do not match, update the routing table such that the primary NIC/IP are targeted.

    ---

- Failover clustering in Windows Server
  - When you're querying IMDS with failover clustering, it's sometimes necessary to add a route to the routing table. Here's how:

    1. Open a command prompt with administrator privileges.

    1. Run the following command, and note the address of the Interface for Network Destination (`0.0.0.0`) in the IPv4 Route Table.

    ```bat
    route print
    ```

    > [!NOTE]
    > The following example output is from a Windows Server VM with failover cluster enabled. For simplicity, the output contains only the IPv4 Route Table.

    ```
    IPv4 Route Table
    ===========================================================================
    Active Routes:
    Network Destination        Netmask          Gateway       Interface  Metric
            0.0.0.0          0.0.0.0         10.0.1.1        10.0.1.10    266
            10.0.1.0  255.255.255.192         On-link         10.0.1.10    266
            10.0.1.10  255.255.255.255         On-link         10.0.1.10    266
            10.0.1.15  255.255.255.255         On-link         10.0.1.10    266
            10.0.1.63  255.255.255.255         On-link         10.0.1.10    266
            127.0.0.0        255.0.0.0         On-link         127.0.0.1    331
            127.0.0.1  255.255.255.255         On-link         127.0.0.1    331
    127.255.255.255  255.255.255.255         On-link         127.0.0.1    331
        169.254.0.0      255.255.0.0         On-link     169.254.1.156    271
        169.254.1.156  255.255.255.255         On-link     169.254.1.156    271
    169.254.255.255  255.255.255.255         On-link     169.254.1.156    271
            224.0.0.0        240.0.0.0         On-link         127.0.0.1    331
            224.0.0.0        240.0.0.0         On-link     169.254.1.156    271
    255.255.255.255  255.255.255.255         On-link         127.0.0.1    331
    255.255.255.255  255.255.255.255         On-link     169.254.1.156    271
    255.255.255.255  255.255.255.255         On-link         10.0.1.10    266
    ```

    Run the following command and use the address of the Interface for Network Destination (`0.0.0.0`), which is (`10.0.1.10`) in this example.

    ```bat
    route add 169.254.169.254/32 10.0.1.10 metric 1 -p
    ```

## Support

If you aren't able to get a metadata response after multiple attempts, you can create a support issue in the Azure portal.

## Product feedback

You can provide product feedback and ideas to our user feedback channel under Virtual Machines > Instance Metadata Service [here](https://feedback.azure.com/forums/216843-virtual-machines?category_id=394627)

## Next steps

- [Acquire an access token for the VM](../articles/active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)

- [Scheduled events for Linux](../articles/virtual-machines/linux/scheduled-events.md)

- [Scheduled events for Windows](../articles/virtual-machines/windows/scheduled-events.md)
