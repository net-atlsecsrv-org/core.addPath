---
title: Azure Container Instances YAML reference    
description: Reference for the YAML file supported by Azure Container Instances to configure a container group
services: container-instances
author: dlepow
manager: gwallace

ms.service: container-instances
ms.topic: article
ms.date: 08/12/2019
ms.author: danlep
---

# YAML reference: Azure Container Instances

This article covers the syntax and properties for the YAML file supported by Azure Container Instances to configure a [container group](container-instances-container-groups.md). Use a YAML file to input the group configuration to the [az container create][az-container-create] command in the Azure CLI. 

A YAML file is a convenient way to configure a container group for reproducible deployments. It is a concise alternative to using a [Resource Manager template](/azure/templates/Microsoft.ContainerInstance/2018-10-01/containerGroups) or the Azure Container Instances SDKs to create or update a container group.

> [!NOTE]
> This reference applies to YAML files for Azure Container Instances REST API version `2018-10-01`.

## Schema 

The schema for the YAML file follows, including comments to highlight key properties. For a description of the properties in this schema, see the [Property values](#property-values) section.

```yml
name: string  # Name of the container group
apiVersion: '2018-10-01'
location: string
tags: {}
identity: 
  type: string
  userAssignedIdentities: {}
properties: # Properties of container group
  containers: # Array of container instances in the group
  - name: string # Name of an instance
    properties: # Properties of an instance
      image: string # Container image used to create the instance
      command:
      - string
      ports: # Exposed ports on the instance
      - protocol: string
        port: integer
      environmentVariables:
      - name: string
        value: string
        secureValue: string
      resources: # Resource requirements of the instance
        requests:
          memoryInGB: number
          cpu: number
          gpu:
            count: integer
            sku: string
        limits:
          memoryInGB: number
          cpu: number
          gpu:
            count: integer
            sku: string
      volumeMounts: # Array of volume mounts for the instance
      - name: string
        mountPath: string
        readOnly: boolean
      livenessProbe:
        exec:
          command:
          - string
        httpGet:
          path: string
          port: integer
          scheme: string
        initialDelaySeconds: integer
        periodSeconds: integer
        failureThreshold: integer
        successThreshold: integer
        timeoutSeconds: integer
      readinessProbe:
        exec:
          command:
          - string
        httpGet:
          path: string
          port: integer
          scheme: string
        initialDelaySeconds: integer
        periodSeconds: integer
        failureThreshold: integer
        successThreshold: integer
        timeoutSeconds: integer
  imageRegistryCredentials: # Credentials to pull a private image
  - server: string
    username: string
    password: string
  restartPolicy: string
  ipAddress: # IP address configuration of container group
    ports:
    - protocol: string
      port: integer
    type: string
    ip: string
    dnsNameLabel: string
  osType: string
  volumes: # Array of volumes available to the instances
  - name: string
    azureFile:
      shareName: string
      readOnly: boolean
      storageAccountName: string
      storageAccountKey: string
    emptyDir: {}
    secret: {}
    gitRepo:
      directory: string
      repository: string
      revision: string
  diagnostics:
    logAnalytics:
      workspaceId: string
      workspaceKey: string
      logType: string
      metadata: {}
  networkProfile: # Virtual network profile for container group
    id: string
  dnsConfig: # DNS configuration for container group
    nameServers:
    - string
    searchDomains: string
    options: string
```

## Property values

The following tables describe the values you need to set in the schema.

<a id="Microsoft.ContainerInstance/containerGroups" />

### Microsoft.ContainerInstance/containerGroups object

|  Name | Type | Required | Value |
|  ---- | ---- | ---- | ---- |
|  name | string | Yes | The name of the container group. |
|  apiVersion | enum | Yes | 2018-10-01 |
|  location | string | No | The resource location. |
|  tags | object | No | The resource tags. |
|  identity | object | No | The identity of the container group, if configured. - [ContainerGroupIdentity object](#ContainerGroupIdentity) |
|  properties | object | Yes | [ContainerGroupProperties object](#ContainerGroupProperties) |


<a id="ContainerGroupIdentity" />

### ContainerGroupIdentity object

|  Name | Type | Required | Value |
|  ---- | ---- | ---- | ---- |
|  type | enum | No | The type of identity used for the container group. The type 'SystemAssigned, UserAssigned' includes both an implicitly created identity and a set of user assigned identities. The type 'None' will remove any identities from the container group. - SystemAssigned, UserAssigned, SystemAssigned, UserAssigned, None |
|  userAssignedIdentities | object | No | The list of user identities associated with the container group. The user identity dictionary key references will be Azure Resource Manager resource IDs in the form: '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}'. |


<a id="ContainerGroupProperties" />

### ContainerGroupProperties object

|  Name | Type | Required | Value |
|  ---- | ---- | ---- | ---- |
|  containers | array | Yes | The containers within the container group. - [Container object](#Container) |
|  imageRegistryCredentials | array | No | The image registry credentials by which the container group is created from. - [ImageRegistryCredential object](#ImageRegistryCredential) |
|  restartPolicy | enum | No | Restart policy for all containers within the container group. - `Always` Always restart- `OnFailure` Restart on failure- `Never` Never restart. - Always, OnFailure, Never |
|  ipAddress | object | No | The IP address type of the container group. - [IpAddress object](#IpAddress) |
|  osType | enum | Yes | The operating system type required by the containers in the container group. - Windows or Linux |
|  volumes | array | No | The list of volumes that can be mounted by containers in this container group. - [Volume object](#Volume) |
|  diagnostics | object | No | The diagnostic information for a container group. - [ContainerGroupDiagnostics object](#ContainerGroupDiagnostics) |
|  networkProfile | object | No | The network profile information for a container group. - [ContainerGroupNetworkProfile object](#ContainerGroupNetworkProfile) |
|  dnsConfig | object | No | The DNS config information for a container group. - [DnsConfiguration object](#DnsConfiguration) |


<a id="Container" />

### Container object

|  Name | Type | Required | Value |
|  ---- | ---- | ---- | ---- |
|  name | string | Yes | The user-provided name of the container instance. |
|  properties | object | Yes | The properties of the container instance. - [ContainerProperties object](#ContainerProperties) |


<a id="ImageRegistryCredential" />

### ImageRegistryCredential object

|  Name | Type | Required | Value |
|  ---- | ---- | ---- | ---- |
|  server | string | Yes | The Docker image registry server without a protocol such as "http" and "https". |
|  username | string | Yes | The username for the private registry. |
|  password | string | No | The password for the private registry. |


<a id="IpAddress" />

### IpAddress object

|  Name | Type | Required | Value |
|  ---- | ---- | ---- | ---- |
|  ports | array | Yes | The list of ports exposed on the container group. - [Port object](#Port) |
|  type | enum | Yes | Specifies if the IP is exposed to the public internet or private VNET. - Public or Private |
|  ip | string | No | The IP exposed to the public internet. |
|  dnsNameLabel | string | No | The Dns name label for the IP. |


<a id="Volume" />

### Volume object

|  Name | Type | Required | Value |
|  ---- | ---- | ---- | ---- |
|  name | string | Yes | The name of the volume. |
|  azureFile | object | No | The Azure File volume. - [AzureFileVolume object](#AzureFileVolume) |
|  emptyDir | object | No | The empty directory volume. |
|  secret | object | No | The secret volume. |
|  gitRepo | object | No | The git repo volume. - [GitRepoVolume object](#GitRepoVolume) |


<a id="ContainerGroupDiagnostics" />

### ContainerGroupDiagnostics object

|  Name | Type | Required | Value |
|  ---- | ---- | ---- | ---- |
|  logAnalytics | object | No | Container group log analytics information. - [LogAnalytics object](#LogAnalytics) |


<a id="ContainerGroupNetworkProfile" />

### ContainerGroupNetworkProfile object

|  Name | Type | Required | Value |
|  ---- | ---- | ---- | ---- |
|  id | string | Yes | The identifier for a network profile. |


<a id="DnsConfiguration" />

### DnsConfiguration object

|  Name | Type | Required | Value |
|  ---- | ---- | ---- | ---- |
|  nameServers | array | Yes | The DNS servers for the container group. - string |
|  searchDomains | string | No | The DNS search domains for hostname lookup in the container group. |
|  options | string | No | The DNS options for the container group. |


<a id="ContainerProperties" />

### ContainerProperties object

|  Name | Type | Required | Value |
|  ---- | ---- | ---- | ---- |
|  image | string | Yes | The name of the image used to create the container instance. |
|  command | array | No | The commands to execute within the container instance in exec form. - string |
|  ports | array | No | The exposed ports on the container instance. - [ContainerPort object](#ContainerPort) |
|  environmentVariables | array | No | The environment variables to set in the container instance. - [EnvironmentVariable object](#EnvironmentVariable) |
|  resources | object | Yes | The resource requirements of the container instance. - [ResourceRequirements object](#ResourceRequirements) |
|  volumeMounts | array | No | The volume mounts available to the container instance. - [VolumeMount object](#VolumeMount) |
|  livenessProbe | object | No | The liveness probe. - [ContainerProbe object](#ContainerProbe) |
|  readinessProbe | object | No | The readiness probe. - [ContainerProbe object](#ContainerProbe) |


<a id="Port" />

### Port object

|  Name | Type | Required | Value |
|  ---- | ---- | ---- | ---- |
|  protocol | enum | No | The protocol associated with the port. - TCP or UDP |
|  port | integer | Yes | The port number. |


<a id="AzureFileVolume" />

### AzureFileVolume object

|  Name | Type | Required | Value |
|  ---- | ---- | ---- | ---- |
|  shareName | string | Yes | The name of the Azure File share to be mounted as a volume. |
|  readOnly | boolean | No | The flag indicating whether the Azure File shared mounted as a volume is read-only. |
|  storageAccountName | string | Yes | The name of the storage account that contains the Azure File share. |
|  storageAccountKey | string | No | The storage account access key used to access the Azure File share. |


<a id="GitRepoVolume" />

### GitRepoVolume object

|  Name | Type | Required | Value |
|  ---- | ---- | ---- | ---- |
|  directory | string | No | Target directory name. Must not contain or start with '..'.  If '.' is supplied, the volume directory will be the git repository.  Otherwise, if specified, the volume will contain the git repository in the subdirectory with the given name. |
|  repository | string | Yes | Repository URL |
|  revision | string | No | Commit hash for the specified revision. |


<a id="LogAnalytics" />

### LogAnalytics object

|  Name | Type | Required | Value |
|  ---- | ---- | ---- | ---- |
|  workspaceId | string | Yes | The workspace id for log analytics |
|  workspaceKey | string | Yes | The workspace key for log analytics |
|  logType | enum | No | The log type to be used. - ContainerInsights or ContainerInstanceLogs |
|  metadata | object | No | Metadata for log analytics. |


<a id="ContainerPort" />

### ContainerPort object

|  Name | Type | Required | Value |
|  ---- | ---- | ---- | ---- |
|  protocol | enum | No | The protocol associated with the port. - TCP or UDP |
|  port | integer | Yes | The port number exposed within the container group. |


<a id="EnvironmentVariable" />

### EnvironmentVariable object

|  Name | Type | Required | Value |
|  ---- | ---- | ---- | ---- |
|  name | string | Yes | The name of the environment variable. |
|  value | string | No | The value of the environment variable. |
|  secureValue | string | No | The value of the secure environment variable. |


<a id="ResourceRequirements" />

### ResourceRequirements object

|  Name | Type | Required | Value |
|  ---- | ---- | ---- | ---- |
|  requests | object | Yes | The resource requests of this container instance. - [ResourceRequests object](#ResourceRequests) |
|  limits | object | No | The resource limits of this container instance. - [ResourceLimits object](#ResourceLimits) |


<a id="VolumeMount" />

### VolumeMount object

|  Name | Type | Required | Value |
|  ---- | ---- | ---- | ---- |
|  name | string | Yes | The name of the volume mount. |
|  mountPath | string | Yes | The path within the container where the volume should be mounted. Must not contain colon (:). |
|  readOnly | boolean | No | The flag indicating whether the volume mount is read-only. |


<a id="ContainerProbe" />

### ContainerProbe object

|  Name | Type | Required | Value |
|  ---- | ---- | ---- | ---- |
|  exec | object | No | The execution command to probe - [ContainerExec object](#ContainerExec) |
|  httpGet | object | No | The Http Get settings to probe - [ContainerHttpGet object](#ContainerHttpGet) |
|  initialDelaySeconds | integer | No | The initial delay seconds. |
|  periodSeconds | integer | No | The period seconds. |
|  failureThreshold | integer | No | The failure threshold. |
|  successThreshold | integer | No | The success threshold. |
|  timeoutSeconds | integer | No | The timeout seconds. |


<a id="ResourceRequests" />

### ResourceRequests object

|  Name | Type | Required | Value |
|  ---- | ---- | ---- | ---- |
|  memoryInGB | number | Yes | The memory request in GB of this container instance. |
|  cpu | number | Yes | The CPU request of this container instance. |
|  gpu | object | No | The GPU request of this container instance. - [GpuResource object](#GpuResource) |


<a id="ResourceLimits" />

### ResourceLimits object

|  Name | Type | Required | Value |
|  ---- | ---- | ---- | ---- |
|  memoryInGB | number | No | The memory limit in GB of this container instance. |
|  cpu | number | No | The CPU limit of this container instance. |
|  gpu | object | No | The GPU limit of this container instance. - [GpuResource object](#GpuResource) |


<a id="ContainerExec" />

### ContainerExec object

|  Name | Type | Required | Value |
|  ---- | ---- | ---- | ---- |
|  command | array | No | The commands to execute within the container. - string |


<a id="ContainerHttpGet" />

### ContainerHttpGet object

|  Name | Type | Required | Value |
|  ---- | ---- | ---- | ---- |
|  path | string | No | The path to probe. |
|  port | integer | Yes | The port number to probe. |
|  scheme | enum | No | The scheme. - http or https |


<a id="GpuResource" />

### GpuResource object

|  Name | Type | Required | Value |
|  ---- | ---- | ---- | ---- |
|  count | integer | Yes | The count of the GPU resource. |
|  sku | enum | Yes | The SKU of the GPU resource. - K80, P100, V100 |


## Next steps

See the tutorial [Deploy a multi-container group using a YAML file](container-instances-multi-container-yaml.md).

See examples of using a YAML file to deploy container groups in a [virtual network](container-instances-vnet.md) or that [mount an external volume](container-instances-volume-azure-files.md).

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create

