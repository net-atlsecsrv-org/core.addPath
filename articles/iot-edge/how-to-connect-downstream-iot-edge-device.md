---
title: Connect downstream IoT Edge devices - Azure IoT Edge | Microsoft Docs
description: How to configure an IoT Edge device to connect to Azure IoT Edge gateway devices. 
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/10/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:  [amqp, mqtt]
monikerRange: ">=iotedge-2020-11"
---

# Connect a downstream IoT Edge device to an Azure IoT Edge gateway (Preview)

This article provides instructions for establishing a trusted connection between an IoT Edge gateway and a downstream IoT Edge device.

>[!NOTE]
>This feature requires IoT Edge version 1.2, which is in public preview, running Linux containers.

In a gateway scenario, an IoT Edge device can be both a gateway and a downstream device. Multiple IoT Edge gateways can be layered to create a hierarchy of devices. The downstream (or child) devices can authenticate and send or receive messages through their gateway (or parent) device.

There are two different configurations for IoT Edge devices in a gateway hierarchy, and this article address both. The first is the **top layer** IoT Edge device. When multiple IoT Edge devices are connecting through each other, any device that does not have a parent device but connects directly to IoT Hub is considered to be in the top layer. This device is responsible for handling requests from all the devices below it. The other configuration applies to any IoT Edge device in a **lower layer** of the hierarchy. These devices may be a gateway for other downstream IoT and IoT Edge devices, but also need to route any communications through their own parent devices.

Some network architectures require that only the top IoT Edge device in a hierarchy can connect to the cloud. In this configuration, all IoT Edge devices in lower layers of a hierarchy can only communicate with their gateway (or parent) device and any downstream (or child) devices.

All the steps in this article build on those in [Configure an IoT Edge device to act as a transparent gateway](how-to-create-transparent-gateway.md), which sets up an IoT Edge device to be a gateway for downstream IoT devices. The same basic steps apply to all gateway scenarios:

* **Authentication**: Create IoT Hub identities for all devices in the gateway hierarchy.
* **Authorization**: Set up the parent/child relationship in IoT Hub to authorize child devices to connect to their parent device like they would connect to IoT Hub.
* **Gateway discovery**: Ensure that the child device can find its parent device on the local network.
* **Secure connection**: Establish a secure connection with trusted certificates that are part of the same chain.

## Prerequisites

* A free or standard IoT hub.
* At least two **IoT Edge devices**, one to be the top layer device and one or more lower layer devices. If you don't have IoT Edge devices available, you can [Run Azure IoT Edge on Ubuntu virtual machines](how-to-install-iot-edge-ubuntuvm.md).
* If you use the Azure CLI to create and manage devices, have Azure CLI v2.3.1 with the Azure IoT extension v0.9.10 or higher installed.

This article provides detailed steps and options to help you create the right gateway hierarchy for your scenario. For a guided tutorial, see [Create a hierarchy of IoT Edge devices using gateways](tutorial-nested-iot-edge.md).

## Create a gateway hierarchy

You create an IoT Edge gateway hierarchy by defining parent/child relationships for the IoT Edge devices in the scenario. You can set a parent device when you create a new device identity, or you can manage the parent and children of an existing device identity.

The step of setting up parent/child relationships authorizes child devices to connect to their parent device like they would connect to IoT Hub.

Only IoT Edge devices can be parent devices, but both IoT Edge devices and IoT devices can be children. A parent can have many children, but a child can only have one parent. A gateway hierarchy is created by chaining parent/child sets together so that the child of one device is the parent of another.

<!-- TODO: graphic of gateway hierarchy -->

# [Portal](#tab/azure-portal)

In the Azure portal, you can manage the parent/child relationship when you create new device identities, or by editing existing devices.

When you create a new IoT Edge device, you have the option of choosing parent and children devices from the list of existing IoT Edge devices in that hub.

1. In the [Azure portal](https://portal.azure.com), navigate to your IoT hub.
1. Select **IoT Edge** from the navigation menu.
1. Select **Add an IoT Edge device**.
1. Along with setting the device ID and authentication settings, you can **Set a parent device** or **Choose child devices**.
1. Choose the device or devices that you want as a parent or child.

You can also create or manage parent/child relationships for existing devices.

1. In the [Azure portal](https://portal.azure.com), navigate to your IoT hub.
1. Select **IoT Edge** from the navigation menu.
1. Select the device you want to manage from the list of **IoT Edge devices**.
1. Select **Set a parent device** or **Manage child devices**.
1. Add or remove any parent or child devices.

# [Azure CLI](#tab/azure-cli)

The [azure-iot](/cli/azure/ext/azure-iot) extension for the Azure CLI provides commands to manage your IoT resources. You can manage the parent/child relationship of IoT and IoT Edge devices when you create new device identities or by editing existing devices.

The [az iot hub device-identity](/cli/azure/ext/azure-iot/iot/hub/device-identity) set of commands allow you to manage the parent/child relationships for a given device.

The `create` command includes parameters for adding children devices and setting a parent device at the time of device creation.

Additional device-identity commands, including `add-children`,`list-children`, and `remove-children` or `get-parent` and `set-parent`, allow you to manage the parent/child relationships for existing devices.

---

## Prepare certificates

A consistent chain of certificates must be installed across devices in the same gateway hierarchy to establish a secure communication between themselves. Every device in the hierarchy, whether an IoT Edge device or an IoT leaf device, needs a copy of the same root CA certificate. Each IoT Edge device in the hierarchy then uses that root CA certificate as the root for its device CA certificate.

With this setup, each downstream IoT Edge device or IoT leaf device can verify the identity of their parent by verifying that the edgeHub they connect to has a server certificate that is signed by the shared root CA certificate.

<!-- TODO: certificate graphic -->

Create the following certificates:

* A **root CA certificate**, which is the topmost shared certificate for all the devices in a given gateway hierarchy. This certificate is installed on all devices.
* Any **intermediate certificates** that you want to include in the root certificate chain.
* A **device CA certificate** and its **private key**, generated by the root and intermediate certificates. You need one unique device CA certificate for each IoT Edge device in the gateway hierarchy.

>[!NOTE]
>Currently, a limitation in libiothsm prevents the use of certificates that expire on or after January 1, 2038.

You can use either a self-signed certificate authority or purchase one from a trusted commercial certificate authority like Baltimore, Verisign, Digicert, or GlobalSign.

If you don't have your own certificates to use, you can [create demo certificates to test IoT Edge device features](how-to-create-test-certificates.md). Follow the steps in that article to create one set of root and intermediate certificates, then to create IoT Edge device CA certificates for each of your devices.

## Configure IoT Edge on devices

The steps for setting up IoT Edge as a gateway is very similar to the steps for setting up IoT Edge as a downstream device.

To enable gateway discovery, every IoT Edge gateway device needs to be configured with a **hostname** that its child devices will use to find it on the local network. Every downstream IoT Edge device needs to be configured with a **parent_hostname** to connect to. If a single IoT Edge device is both a parent and a child device, it needs both parameters.

To enable secure connections, every IoT Edge device in a gateway scenario needs to be configured with an unique device CA certificate and a copy of the root CA certificate shared by all devices in the gateway hierarchy.

You should already have IoT Edge installed on your device. If not, follow the steps to [Install the Azure IoT Edge runtime](how-to-install-iot-edge.md) and then provision your device with either [symmetric key authentication](how-to-manual-provision-symmetric-key.md) or [X.509 certificate authentication](how-to-manual-provision-x509.md).

The steps in this section reference the **root CA certificate** and **device CA certificate and private key** that were discussed earlier in this article. If you created those certificates on a different device, have them available on this device. You can transfer the files physically, like with a USB drive, with a service like [Azure Key Vault](../key-vault/general/overview.md), or with a function like [Secure file copy](https://www.ssh.com/ssh/scp/).

Use the following steps to configure IoT Edge on your device.

On Linux, make sure that the user **iotedge** has read permissions for the directory holding the certificates and keys.

1. Install the **root CA certificate** on this IoT Edge device.

   ```bash
   sudo cp <path>/<root ca certificate>.pem /usr/local/share/ca-certificates/<root ca certificate>.pem
   ```

1. Update the certificate store.

   ```bash
   sudo update-ca-certificates
   ```

   This command should output that one certificate was added to /etc/ssl/certs.

1. Open the IoT Edge security daemon configuration file.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. Find the **certificates** section in the config.yaml file. Update the three certificate fields to point to your certificates. Provide the file URI paths, which take the format `file:///<path>/<filename>`.

   * **device_ca_cert**: File URI path to the device CA certificate unique to this device.
   * **device_ca_pk**: File URI path to the device CA private key unique to this device.
   * **trusted_ca_certs**: File URI path to the root CA certificate shared by all devices in the gateway hierarchy.

1. Find the **hostname** parameter in the config.yaml file. Update the hostname to be the fully qualified domain name (FQDN) or the IP address of the IoT Edge device.

   The value of this parameter is what downstream devices will use to connect to this gateway. The hostname takes the machine name by default, but the FQDN or IP address is required to connect downstream devices.

   Use a hostname shorter than 64 characters, which is the character limit for a server certificate common name.

   Be consistent with the hostname pattern across a gateway hierarchy. Use either FQDNs or IP addresses, but not both.

1. **If this device is a child device**, find the **parent_hostname** parameter. Update the **parent_hostname** field to be the FQDN or IP address of the parent device, matching whatever was provided as the hostname in the parent's config.yaml file.

1. While this feature is in public preview, you need to configure your IoT Edge device to use the public preview version of the IoT Edge agent when it starts up.

   Find the **agent** yaml section and update the image value to the public preview image:

   ```yml
   agent:
     name: "edgeAgent"
     type: "docker"
     env: {}
     config:
       image: "mcr.microsoft.com/azureiotedge-agent:1.2.0-rc1"
       auth: {}
   ```

1. Save (`Ctrl+O`) and close (`Ctrl+X`) the config.yaml file.

1. If you've used any other certificates for IoT Edge before, delete the files in the following two directories to make sure that your new certificates get applied:

   * `/var/lib/iotedge/hsm/certs`
   * `/var/lib/iotedge/hsm/cert_keys`

1. Restart the IoT Edge service to apply your changes.

   ```bash
   sudo systemctl restart iotedge
   ```

1. Check for any errors in the configuration.

   ```bash
   sudo iotedge check --verbose
   ```

   >[!TIP]
   >The IoT Edge check tool uses a container to perform some of the diagnostics check. If you want to use this tool on downstream IoT Edge devices, make sure they can access `mcr.microsoft.com/azureiotedge-diagnostics:latest`, or have the container image in your private container registry.

## Configure runtime modules for public preview

While this feature is in public preview, you need to configure your IoT Edge device to use the public preview versions of the IoT Edge runtime modules. The previous section provides steps for configuring edgeAgent at startup. You also need to configure the runtime modules in deployments for your device.

1. Configure the edgeHub module to use the public preview image: `mcr.microsoft.com/azureiotedge-hub:1.2.0-rc1`.

1. Configure the following environment variables for the edgeHub module:

   | Name | Value |
   | - | - |
   | `experimentalFeatures__enabled` | `true` |
   | `experimentalFeatures__nestedEdgeEnabled` | `true` |

1. Configure the edgeAgent module to use the public preview image: `mcr.microsoft.com/azureiotedge-hub:1.2.0-rc1`.

## Network isolate downstream devices

The steps so far in this article set up IoT Edge devices as either a gateway or a downstream device, and create a trusted connection between them. The gateway device handles interactions between the downstream device and IoT Hub, including authentication and message routing. Modules deployed to downstream IoT Edge devices can still create their own connections to cloud services.

Some network architectures, like those that follow the ISA-95 standard, seek to minimize the number of internet connections. In those scenarios, you can configure downstream IoT Edge devices without direct internet connectivity. Beyond routing IoT Hub communications through their gateway device, downstream IoT Edge devices can rely on the gateway device for all cloud connections.

This network configuration requires that only the IoT Edge device in the top layer of a gateway hierarchy has direct connections to the cloud. IoT Edge devices in the lower layers can only communicate with their parent device or any children devices. Special modules on the gateway devices enable this scenario, including:

* The **API proxy module** is required on any IoT Edge gateway that has another IoT Edge device below it. That means it must be on *every layer* of a gateway hierarchy except the bottom layer. This module uses an [nginx](https://nginx.org) reverse proxy to route HTTP data through network layers over a single port. It is highly configurable through its module twin and environment variables, so can be adjusted to fit your gateway scenario requirements.

* The **Docker registry module** can be deployed on the IoT Edge gateway at the *top layer* of a gateway hierarchy. This module is responsible for retrieving and caching container images on behalf of all the IoT Edge devices in lower layers. The alternative to deploying this module at the top layer is to use a local registry, or to manually load container images onto devices and set the module pull policy to **never**.

* The **Azure Blob Storage on IoT Edge** can be deployed on the IoT Edge gateway at the *top layer* of a gateway hierarchy. This module is responsible for uploading blobs on behalf of all the IoT Edge devices in lower layers. The ability to upload blobs also enables useful troubleshooting functionality for IoT Edge devices in lower layers, like module log upload and support bundle upload.

### Network configuration

For each gateway device in the top layer, network operators need to:

* Provide a static IP address or fully qualified domain name (FQDN).
* Authorize outbound communications from this IP address to your Azure IoT Hub hostname over ports 443 (HTTPS) and 5671 (AMQP).
* Authorize outbound communications from this IP address to your Azure Container Registry hostname over port 443 (HTTPS).

  The API proxy module can only handle connections to one container registry at a time. We recommend having all container images, including the public images provided by Microsoft Container Registry (mcr.microsoft.com), stored in your private container registry.

For each gateway device in a lower layer, network operators need to:

* Provide a static IP address.
* Authorize outbound communications from this IP address to the parent gateway's IP address over ports 443 (HTTPS) and 5671 (AMQP).

### Deploy modules to top layer devices

The IoT Edge device at the top layer of a gateway hierarchy has a set of required modules that must be deployed to it, in addition to any workload modules you may run on the device.

The API proxy module was designed to be customized to handle most common gateway scenarios. This article provides and example to set up the modules in a basic configuration. Refer to [Configure the API proxy module for your gateway hierarchy scenario](how-to-configure-api-proxy-module.md) for more detailed information and examples.

1. In the [Azure portal](https://portal.azure.com), navigate to your IoT hub.
1. Select **IoT Edge** from the navigation menu.
1. Select the top layer device that you're configuring from the list of **IoT Edge devices**.
1. Select **Set modules**.
1. In the **IoT Edge modules** section, select **Add** then choose **Marketplace module**.
1. Search for and select the **IoT Edge API Proxy** module.
1. Select the name of the API proxy module from the list of deployed modules and update the following module settings:
   1. In the **Environment variables** tab, update the value of **NGINX_DEFAULT_PORT** to `443`.
   1. In the **Container create options** tab, update the port bindings to reference port 443.

      ```json
      {
        "HostConfig": {
          "PortBindings": {
            "443/tcp": [
              {
                "HostPort": "443"
              }
            ]
          }
        }
      }
      ```

   These changes configure the API proxy module to listen on port 443. To prevent port binding collisions, you need to configure the edgeHub module to not listen on port 443. Instead, the API proxy module will route any edgeHub traffic on port 443.

1. Select **Runtime Settings** and find the edgeHub module create options. Delete the port binding for port 443, leaving the bindings for ports 5671 and 8883.

   ```json
   {
     "HostConfig": {
       "PortBindings": {
         "5671/tcp": [
           {
             "HostPort": "5671"
           }
         ],
         "8883/tcp": [
           {
             "HostPort": "8883"
           }
         ]
       }
     }
   }
   ```

1. Select **Save** to save your changes to the runtime settings.
1. Select **Add** again, then choose **IoT Edge module**.
1. Provide the following values to add the Docker registry module to your deployment:
   1. **IoT Edge module name**: `registry`
   1. On the **Module settings** tab, **Image URI**: `registry:latest`
   1. On the **Environment variables** tab, add the following environment variables:

      * **Name**: `REGISTRY_PROXY_REMOTEURL` **Value**: The URL for the container registry you want this registry module to map to. For example, `https://myregistry.azurecr`.

        The registry module can only map to one container registry, so we recommend having all container images in a single private container registry.

      * **Name**: `REGISTRY_PROXY_USERNAME` **Value**: Username to authenticate to the container registry.

      * **Name**: `REGISTRY_PROXY_PASSWORD` **Value**: Password to authenticate to the container registry.

   1. On the **Container create options** tab, paste:

      ```json
      {
          "HostConfig": {
              "PortBindings": {
                  "5000/tcp": [
                      {
                          "HostPort": "5000"
                      }
                  ]
              }
          }
      }
      ```

1. Select **Add** to add the module to the deployment.
1. Select **Next: Routes** to go to the next step.
1. To enable device-to-cloud messages from downstream devices to reach IoT Hub, include a route that passes all messages to IoT Hub. For example:
    1. **Name**: `Route`
    1. **Value**: `FROM /messages/* INTO $upstream`
1. Select **Review + create** to go to the final step.
1. Select **Create** to deploy to your device.

### Deploy modules to lower layer devices

IoT Edge devices in lower layers of a gateway hierarchy have one required module that must be deployed to them, in addition to any workload modules you may run on the device.

#### Route container image pulls

Before discussing the required proxy module for IoT Edge devices in gateway hierarchies, it's important to understand how IoT Edge devices in lower layers get their module images.

If your lower layer devices can't connect to the cloud, but you want them to pull module images as usual, then the top layer device of the gateway hierarchy must be configured to handle these requests. The top layer device needs to run a Docker **registry** module that is mapped to your container registry. Then, configure the API proxy module to route container requests to it. Those details are discussed in the earlier sections of this article. In this configuration, the lower layer devices should not point to cloud container registries, but to the registry running in the top layer.

For example, instead of calling `mcr.microsoft.com/azureiotedge-api-proxy:latest`, lower layer devices should call `$upstream:443/azureiotedge-api-proxy:latest`.

The **$upstream** parameter points to the parent of a lower layer device, so the request will route through all the layers until it reaches the top layer which has a proxy environment routing container requests to the registry module. The `:443` port in this example should be replaced with whichever port the API proxy module on the parent device is listening on.

The API proxy module can only route to one registry module, and each registry module can only map to one container registry. Therefore, any images that lower layer devices need to pull must be stored in a single container registry.

If you don't want lower layer devices making module pull requests through a gateway hierarchy, another option is to manage a local registry solution. Or, push the module images onto the devices before creating deployments and then set the **imagePullPolicy** to **never**.

#### Bootstrap the IoT Edge agent

The IoT Edge agent is the first runtime component to start on any IoT Edge device. You need to make sure that any downstream IoT Edge devices can access the edgeAgent module image when they start up, and then they can access deployments and start the rest of the module images.

When you go into the config.yaml file on an IoT Edge device to provide its authentication information, certificates, and parent hostname, also update the edgeAgent container image.

If the top level gateway device is configured to handle container image requests, replace `mcr.microsoft.com` with the parent hostname and API proxy listening port. In the deployment manifest, you can use `$upstream` as a shortcut, but that requires the edgeHub module to handle routing and that module hasn't started at this point. For example:

```yml
agent:
  name: "edgeAgent"
  type: "docker"
  env: {}
  config:
    image: "{Parent FQDN or IP}:443/azureiotedge-agent:1.2.0-rc1"
    auth: {}
```

If you are using a local container registry, or providing the container images manually on the device, update the config.yaml file accordingly.

#### Configure runtime and deploy proxy module

The **API proxy module** is required for routing all communications between the cloud and any downstream IoT Edge devices. An IoT Edge device in the bottom layer of the hierarchy, with no downstream IoT Edge devices, does not need this module.

The API proxy module was designed to be customized to handle most common gateway scenarios. This article briefly touches on the steps to set up the modules in a basic configuration. Refer to [Configure the API proxy module for your gateway hierarchy scenario](how-to-configure-api-proxy-module.md) for more detailed information and examples.

1. In the [Azure portal](https://portal.azure.com), navigate to your IoT hub.
1. Select **IoT Edge** from the navigation menu.
1. Select the lower layer device that you're configuring from the list of **IoT Edge devices**.
1. Select **Set modules**.
1. In the **IoT Edge modules** section, select **Add** then choose **Marketplace module**.
1. Search for and select the **IoT Edge API Proxy** module.
1. Select the name of the API proxy module from the list of deployed modules and update the following module settings:
   1. In the **Module settings** tab, update the value of **Image URI**. Replace `mcr.microsoft.com` with `$upstream:443`.
   1. In the **Environment variables** tab, update the value of **NGINX_DEFAULT_PORT** to `443`.
   1. In the **Container create options** tab, update the port bindings to reference port 443.

      ```json
      {
        "HostConfig": {
          "PortBindings": {
            "443/tcp": [
              {
                "HostPort": "443"
              }
            ]
          }
        }
      }
      ```

   These changes configure the API proxy module to listen on port 443. To prevent port binding collisions, you need to configure the edgeHub module to not listen on port 443. Instead, the API proxy module will route any edgeHub traffic on port 443.

1. Select **Runtime Settings**.
1. Update the edgeHub module settings:

   1. In the **Image** field, replace `mcr.microsoft.com` with `$upstream:443`.
   1. In the **Create options** field, delete the port binding for port 443, leaving the bindings for ports 5671 and 8883.

   ```json
   {
     "HostConfig": {
       "PortBindings": {
         "5671/tcp": [
           {
             "HostPort": "5671"
           }
         ],
         "8883/tcp": [
           {
             "HostPort": "8883"
           }
         ]
       }
     }
   }
   ```

1. Update the edgeAgent module settings:
   1. In the **Image** field, replace `mcr.microsoft.com` with `$upstream:443`.

1. Select **Save** to save your changes to the runtime settings.
1. Select **Next: Routes** to go to the next step.
1. To enable device-to-cloud messages from downstream devices to reach IoT Hub, include a route that passes all messages to `$upstream`. The upstream parameter points to the parent device in the case of lower layer devices. For example:
    1. **Name**: `Route`
    1. **Value**: `FROM /messages/* INTO $upstream`
1. Select **Review + create** to go to the final step.
1. Select **Create** to deploy to your device.

## Next steps

[How an IoT Edge device can be used as a gateway](iot-edge-as-gateway.md)

[Configure the API proxy module for your gateway hierarchy scenario](how-to-configure-api-proxy-module.md)