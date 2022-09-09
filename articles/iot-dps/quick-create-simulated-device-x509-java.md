---
title: Provision simulated X.509 device to Azure IoT Hub using Java
description: Azure Quickstart - Create and provision a simulated X.509 device using Java device SDK for IoT Hub Device Provisioning Service (DPS).This quickstart uses individual enrollments.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2018
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps 
ms.devlang: java
ms.custom: mvc, devx-track-java
---

# Quickstart: Create and provision a simulated X.509 device using Java device SDK for IoT Hub Device Provisioning Service

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-x509](../../includes/iot-dps-selector-quick-create-simulated-device-x509.md)]

In this quickstart, you create a simulated X.509 device on a Windows computer. You use device sample Java code to connect this simulated device with your IoT hub using an individual enrollment with the Device Provisioning Service (DPS).

## Prerequisites

- Review of [Auto-provisioning concepts](concepts-auto-provisioning.md).
- Completion of [Set up IoT Hub Device Provisioning Service with the Azure portal](./quick-setup-auto-provision.md).
- An Azure account with an active subscription. [Create one for free](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Java SE Development Kit 8](https://aka.ms/azure-jdks).
- [Maven](https://maven.apache.org/install.html).
- [Git](https://git-scm.com/download/).

## Prepare the environment 

1. Make sure you have [Java SE Development Kit 8](https://aka.ms/azure-jdks) installed on your machine.

2. Download and install [Maven](https://maven.apache.org/install.html).

3. Make sure Git is installed on your machine and is added to the environment variables accessible to the command window. See [Software Freedom Conservancy's Git client tools](https://git-scm.com/download/) for the latest version of `git` tools to install, which includes the **Git Bash**, the command-line app that you can use to interact with your local Git repository. 

4. Open a command prompt. Clone the GitHub repo for device simulation code sample:
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-java.git --recursive
    ```
5. Navigate to the root `azure-iot-sdk-`java` directory and build the project to download all needed packages.
   
   ```cmd/sh
   cd azure-iot-sdk-java
   mvn install -DskipTests=true
   ```
6. Navigate to the certificate generator project and build the project. 

    ```cmd/sh
    cd azure-iot-sdk-java/provisioning/provisioning-tools/provisioning-x509-cert-generator
    mvn clean install
    ```

## Create a self-signed X.509 device certificate and individual enrollment entry

In this section you, will use a self-signed X.509 certificate, it is important to keep in mind the following:

* Self-signed certificates are for testing only, and should not be used in production.
* The default expiration date for a self-signed certificate is one year.

You will use sample code from the [Azure IoT SDK for Java](https://github.com/Azure/azure-iot-sdk-java.git) to create the certificate to be used with the individual enrollment entry for the simulated device.

The Azure IoT Device Provisioning Service supports two types of enrollments:

- [Enrollment groups](concepts-service.md#enrollment-group): Used to enroll multiple related devices.
- [Individual enrollments](concepts-service.md#individual-enrollment): Used to enroll a single device.

This article demonstrates individual enrollments.

1. Using the command prompt from previous steps, navigate to the `target` folder, then execute the .jar file created in the previous step.

    ```cmd/sh
    cd target
    java -jar ./provisioning-x509-cert-generator-{version}-with-deps.jar
    ```

2. Enter **N** for _Do you want to input common name_. Copy to the clipboard the output of `Client Cert`, starting from *-----BEGIN CERTIFICATE-----* through *-----END CERTIFICATE-----*.

   ![Individual certificate generator](./media/java-quick-create-simulated-device-x509/individual.png)

3. Create a file named **_X509individual.pem_** on your Windows machine, open it in an editor of your choice, and copy the clipboard contents to this file. Save the file and close your editor.

4. In the command prompt, enter **N** for _Do you want to input Verification Code_ and keep the program output open for reference later in the quickstart. Later you copy the `Client Cert` and `Client Cert Private Key` values, for use in the next section.

5. Sign in to the [Azure portal](https://portal.azure.com), select the **All resources** button on the left-hand menu and open your Device Provisioning Service instance.

6. From the Device Provisioning Service menu, select **Manage enrollments**. Select **Individual Enrollments** tab and select the **Add individual enrollment** button at the top. 

7. In the **Add Enrollment** panel, enter the following information:
   - Select **X.509** as the identity attestation *Mechanism*.
   - Under the *Primary certificate .pem or .cer file*, choose *Select a file* to select the certificate file **X509individual.pem** created in the previous steps.  
   - Optionally, you may provide the following information:
     - Select an IoT hub linked with your provisioning service.
     - Enter a unique device ID. Make sure to avoid sensitive data while naming your device. 
     - Update the **Initial device twin state** with the desired initial configuration for the device.
     - Once complete, press the **Save** button. 

     [![Add individual enrollment for X.509 attestation in the portal](./media/java-quick-create-simulated-device-x509/device-enrollment.png)](./media/how-to-manage-enrollments/individual-enrollment.png#lightbox)

     Upon successful enrollment, your X.509 device appears as **microsoftriotcore** under the *Registration ID* column in the *Individual Enrollments* tab. 



## Simulate the device

1. From the Device Provisioning Service menu, select **Overview** and note your _ID Scope_ and _Provisioning Service Global Endpoint_.

    ![Service information](./media/java-quick-create-simulated-device-x509/extract-dps-endpoints.png)

2. Open a command prompt. Navigate to the sample project folder of the Java SDK repository.

    ```cmd/sh
    cd azure-iot-sdk-java/provisioning/provisioning-samples/provisioning-X509-sample
    ```

3. Enter the provisioning service and X.509 identity information in your code. This is used during autoprovisioning, for attestation of the simulated device, prior to device registration:

   - Edit the file `/src/main/java/samples/com/microsoft/azure/sdk/iot/ProvisioningX509Sample.java`, to include your _ID Scope_ and _Provisioning Service Global Endpoint_ as noted previously. Also include _Client Cert_ and _Client Cert Private Key_ as noted in the previous section.

      ```java
      private static final String idScope = "[Your ID scope here]";
      private static final String globalEndpoint = "[Your Provisioning Service Global Endpoint here]";
      private static final ProvisioningDeviceClientTransportProtocol PROVISIONING_DEVICE_CLIENT_TRANSPORT_PROTOCOL = ProvisioningDeviceClientTransportProtocol.HTTPS;
      private static final String leafPublicPem = "<Your Public PEM Certificate here>";
      private static final String leafPrivateKey = "<Your Private PEM Key here>";
      ```

   - Use the following format when copying/pasting your certificate and private key:
        
      ```java
      private static final String leafPublicPem = "-----BEGIN CERTIFICATE-----\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "+XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "-----END CERTIFICATE-----\n";
      private static final String leafPrivateKey = "-----BEGIN PRIVATE KEY-----\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXX\n" +
            "-----END PRIVATE KEY-----\n";
      ```

4. Build the sample. Navigate to the `target` folder and execute the created .jar file.

    ```cmd/sh
    mvn clean install
    cd target
    java -jar ./provisioning-x509-sample-{version}-with-deps.jar
    ```

5. In the Azure portal, navigate to the IoT hub linked to your provisioning service and open the **Device Explorer** blade. Upon successful provisioning of the simulated X.509 device to the hub, its device ID appears on the **Device Explorer** blade, with *STATUS* as **enabled**.  You might need to press the **Refresh** button at the top if you already opened the blade prior to running the sample device application. 

    ![Device is registered with the IoT hub](./media/java-quick-create-simulated-device-x509/hubregistration.png) 

> [!NOTE]
> If you changed the *initial device twin state* from the default value in the enrollment entry for your device, it can pull the desired twin state from the hub and act accordingly. For more information, see [Understand and use device twins in IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md).
>


## Clean up resources

If you plan to continue working on and exploring the device client sample, do not clean up the resources created in this quickstart. If you do not plan to continue, use the following steps to delete all resources created by this quickstart.

1. Close the device client sample output window on your machine.
2. From the left-hand menu in the Azure portal, select **All resources** and then select your Device Provisioning service. Open the **Manage Enrollments** blade for your service, and then select the **Individual Enrollments** tab. Select the check box next to the *REGISTRATION ID* of the device you enrolled in this quickstart, and press the **Delete** button at the top of the pane. 
3. From the left-hand menu in the Azure portal, select **All resources** and then select your IoT hub. Open the **IoT devices** blade for your hub, select the check box next to the *DEVICE ID* of the device you registered in this quickstart, and then press the **Delete** button at the top of the pane.


## Next steps

In this quickstart, you created a simulated X.509 device on your Windows machine. You configured its enrollment in your Azure IoT Hub Device Provisioning Service, then autoprovisioned the device to your IoT hub. To learn how to enroll your X.509 device programmatically, continue to the quickstart for programmatic enrollment of X.509 devices. 

> [!div class="nextstepaction"]
> [Azure quickstart - Enroll X.509 devices to Azure IoT Hub Device Provisioning Service](quick-enroll-device-x509-java.md)
