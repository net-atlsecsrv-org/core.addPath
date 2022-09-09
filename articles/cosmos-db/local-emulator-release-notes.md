---
title: Azure Cosmos Emulator download and release notes
description: Get the Azure Cosmos emulator release notes for different versions and download information. 
ms.service: cosmos-db
ms.topic: tutorial
author: markjbrown
ms.author: mjbrown
ms.date: 06/20/2019
---

# Azure Cosmos Emulator - Release notes and download information

This article shows the Azure Cosmos emulator release notes with a list of feature updates that were made in each release. It also lists the latest version of emulator to download and use.

## Download

| | |
|---------|---------|
|**MSI download**|[Microsoft Download Center](https://aka.ms/cosmosdb-emulator)|
|**Get started**|[Develop locally with Azure Cosmos emulator](local-emulator.md)|

## Release notes

### 2.7.0

- This release fixes a regression which prevented users from executing queries against the SQL API account from the emulator when using .NET core or x86 .NET based clients.

### 2.4.6

- This release provides parity with the features in the Azure Cosmos service as of July 2019, with the exceptions noted in [Develop locally with Azure Cosmos emulator](local-emulator.md). It also fixes several bugs related to emulator shutdown when invoked via command line and internal IP address overrides for SDK clients using direct mode connectivity.

### 2.4.3

- Disabled starting the MongoDB service by default. Only the SQL endpoint is enabled as default. The user must start the endpoint manually using the emulator's "/EnableMongoDbEndpoint" command-line option. Now, it's like all the other service endpoints, such as Gremlin, Cassandra, and Table.
- Fixed a bug in the emulator when starting with “/AllowNetworkAccess” where the Gremlin, Cassandra, and Table endpoints weren't properly handling requests from external clients.
- Add direct connection ports to the Firewall Rules settings.

### 2.4.0

- Fixed an issue with emulator failing to start when network monitoring apps, such as Pulse Client, are present on the host computer.
