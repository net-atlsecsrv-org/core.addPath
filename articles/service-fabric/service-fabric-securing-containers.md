---
title: Import certificates into a container running on Azure Service Fabric| Microsoft Docs
description: Learn now to import certificate files into a Service Fabric container service.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''

ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
---

# Import a certificate file into a container running on Service Fabric

You can secure your container services by specifying a certificate. Service Fabric provides a mechanism for services inside a container to access a certificate that is installed on the nodes in a Windows or Linux cluster (version 5.7 or higher). The certificate must be installed in a certificate store under LocalMachine on all nodes of the cluster. The private key corresponding to the certificate must be available, accessible and - on Windows - exportable. The certificate information is provided in the application manifest under the `ContainerHostPolicies` tag as the following snippet shows:

```xml
  <ContainerHostPolicies CodePackageRef="NodeContainerService.Code">
    <CertificateRef Name="MyCert1" X509StoreName="My" X509FindValue="[Thumbprint1]"/>
    <CertificateRef Name="MyCert2" X509FindValue="[Thumbprint2]"/>
 ```

For Windows clusters, when starting the application, the runtime exports each referenced certificate and its corresponding private key into a PFX file, secured with a randomly-generated password. The PFX and password files, respectively, are accessible inside the container using the following environment variables: 

* Certificates_ServicePackageName_CodePackageName_CertName_PFX
* Certificates_ServicePackageName_CodePackageName_CertName_Password

For Linux clusters, the certificates (PEM) are copied over from the store specified by X509StoreName onto the container. The corresponding environment variables on Linux are:

* Certificates_ServicePackageName_CodePackageName_CertName_PEM
* Certificates_ServicePackageName_CodePackageName_CertName_PrivateKey

Alternatively, if you already have the certificates in the required form and want to access it inside the container, you can create a data package inside your app package and specify the following inside your application manifest:

```xml
<ContainerHostPolicies CodePackageRef="NodeContainerService.Code">
  <CertificateRef Name="MyCert1" DataPackageRef="[DataPackageName]" DataPackageVersion="[Version]" RelativePath="[Relative Path to certificate inside DataPackage]" Password="[password]" IsPasswordEncrypted="[true/false]"/>
 ```

The container service or process is responsible for importing the certificate files into the container. To import the certificate, you can use `setupentrypoint.sh` scripts or execute custom code within the container process. Here is sample code in C# for importing the PFX file:

```csharp
string certificateFilePath = Environment.GetEnvironmentVariable("Certificates_MyServicePackage_NodeContainerService.Code_MyCert1_PFX");
string passwordFilePath = Environment.GetEnvironmentVariable("Certificates_MyServicePackage_NodeContainerService.Code_MyCert1_Password");
X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser);
string password = File.ReadAllLines(passwordFilePath, Encoding.Default)[0];
password = password.Replace("\0", string.Empty);
X509Certificate2 cert = new X509Certificate2(certificateFilePath, password, X509KeyStorageFlags.MachineKeySet | X509KeyStorageFlags.PersistKeySet);
store.Open(OpenFlags.ReadWrite);
store.Add(cert);
store.Close();
```
This PFX certificate can be used for authenticating the application or service or secure communication with other services. By default, the files are ACLed only to SYSTEM. You can ACL it to other accounts as required by the service.

As a next step, read the following articles:

* [Deploy a Windows container to Service Fabric on Windows Server 2016](service-fabric-get-started-containers.md)
* [Deploy a Docker container to Service Fabric on Linux](service-fabric-get-started-containers-linux.md)
