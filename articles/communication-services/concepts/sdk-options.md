---
title: Client libraries and REST APIs for Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Learn more about Azure Communication Services client libraries and REST APIs.
author: mikben
manager: jken
services: azure-communication-services

ms.author: mikben
ms.date: 03/18/2020
ms.topic: conceptual
ms.service: azure-communication-services
---
# Client libraries and REST APIs

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

Azure Communication Services capabilities are conceptually organized into six areas. Some areas have fully open-sourced client libraries. The Calling client library uses proprietary network interfaces and is currently closed-source, and the Chat library includes a closed-source dependency. Links to all SDKs and samples are maintained in the [Azure Communication Services GitHub repo](https://github.com/Azure/communication).

## Client libraries

| Assembly               | Protocols             |Open vs. Closed Source| Namespaces                          | Capabilities                                                      |
| ---------------------- | --------------------- | ---|-------------------------- | --------------------------------------------------------------------------- |
| Azure Resource Manager | REST | Open            | Azure.ResourceManager.Communication | Provision and manage Communication Services resources             |
| Common                 | REST | Open               | Azure.Communication.Common          | Provides base types for other client libraries |
| Administration         | REST | Open               | Azure.Communication.Administration  | Manage users, access tokens, and phone numbers, allocate standards-compliant STUN and TURN servers |
| Chat                   | REST with proprietary signalling | Open with closed source signalling package    | Azure.Communication.Chat            | Add real-time text based chat to your applications  |
| SMS                    | REST | Open              | Azure.Communication.SMS             | Send and receive SMS messages |
| Calling                | Proprietary transport | Closed |Azure.Communication.Calling         | Leverage voice, video, screen-sharing, and other real-time data communication capabilities          |

### Client library language support

Availability guidance and timelines for individual client library packages are detailed below. The [Azure roadmap](https://azure.microsoft.com/updates/) provides additional information on upcoming features.

| Area           | JavaScript | .NET | Python | Java | Swift or Obj-C | Java (Android) | Other                          |
| -------------- | ---------- | ---- | ------ | ---- | -------------- | -------------- | ------------------------------ |
| Azure Resource Manager | ✔️         | ✔️    | ✔️      | -    | -              | *Not yet supported*  | GO and Azure CLI *Not yet supported* |
| Common         | ✔️         | ✔️    | -      | ✔️   | ✔️            | ✔️             | -                              |
| Administration | ✔️         | ✔️    | ✔️      | ✔️   | -              | -              | CLI                            |
| Chat           | ✔️         | ✔️    | ✔️      | ✔️   | *Not yet supported*  | *Not yet supported*  | -                              |
| SMS            | ✔️         | ✔️    | ✔️      | ✔️   | -              | -              | -                              |
| Calling        | ✔️         | -      | -      | -     | (Obj-C) ✔️     | ✔️            | -                              |

### Client library public repository support

Communication Services publishes built libraries in several public repositories.

| Language       | Optimized for…                       | Packaging |
| -------------- | ------------------------------------ | --------- |
| .NET           | Cross-platform                       | NuGet     |
| Python         | Windows & Linux Servers              | Pypi      |
| Java (J2EE)    | JVM on Windows or Linux Servers      | Maven     |
| Java (Android) | Android client applications          | Maven     |
| JavaScript     | Browser client applications and Node | Npm       |

## REST APIs

Communication Services APIs are documented alongside other Azure REST APIs in [docs.microsoft.com](https://docs.microsoft.com/rest/api/azure/). This documentation will tell you how to structure your HTTP messages and offers guidance for using Postman. This documentation is also offered in Swagger format on [GitHub](https://github.com/Azure/azure-rest-api-specs).

## Additional support details

### iOS and Android support details

- Communication Services iOS client libraries target iOS version 13+, and Xcode 11+.
- Android Java client libraries target Android API level 21+ and Android Studio 4.0+

### .NET support details

With the exception of Calling, Communication Services packages target .NET Standard 2.0 which supports the platforms listed below.

Support via .NET Framework 4.6.1
- Windows 10, 8.1, 8 and 7
- Windows Server 2012 R2, 2012 and 2008 R2 SP1

Support via .NET Core 2.0:
- Windows 10 (1607+), 7 SP1+, 8.1
- Windows Server 2008 R2 SP1+
- Max OS X 10.12+
- Linux multiple versions/distributions
- UWP 10.0.16299 (RS3) September 2017
- Unity 2018.1
- Mono 5.4
- Xamarin iOS 10.14
- Xamarin Mac 3.8

## API stability expectations 

> [!IMPORTANT]
> This section provides guidance on REST APIs and client libraries marked **stable**. APIs marked pre-release, preview, or beta may be changed or deprecated **without notice**. Currently Azure Communication Services is in a **public preview**, and APIs are marked as such.

In the future we may retire versions of the Communication Services client libraries, and we may introduce breaking changes to our REST APIs and released client libraries. Azure Communication Services will *generally* follow two supportability policies for retiring service versions:

- You'll be notified at least three years before being required to change code due to a Communication Services interface change. All documented REST APIs and client library APIs generally enjoy at least three years warning before interfaces are decommissioned.
- You'll be notified at least one year before having to update client library assemblies to the latest minor version. These required updates shouldn't require any code changes because they're in the same major version. This is especially true for the Calling and Chat libraries which have real-time components that frequently require security and performance updates. We highly encourage you to keep your Communication Services client libraries updated.

### API and client library decommissioning examples

**You've integrated the v24 version of the SMS REST API into your application. Azure Communication releases v25.**

You'll get 3 years warning before these APIs stop working and are forced to update to v25. This update might require a code change.

**You've integrated the v2.02 version of the Calling client library into your application. Azure Communication releases v2.05.**

You may be required to update to the v2.05 version of the Calling client library within 12 months of the release of v2.05. This should be a simple replacement of the artifact without requiring a code change because v2.05 is in the v2 major version and has no breaking changes.

## Next steps

For more information, see the following client library overviews:

- [Calling client library Overview](../concepts/voice-video-calling/calling-sdk-features.md)
- [Chat client library Overview](../concepts/chat/sdk-features.md)
- [SMS client library Overview](../concepts/telephony-sms/sdk-features.md)

To get started with Azure Communication Services:

- [Create Azure Communication Resources](../quickstarts/create-communication-resource.md)
- Generate [User Access Tokens](../quickstarts/access-tokens.md)
