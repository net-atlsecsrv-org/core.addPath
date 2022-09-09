---
title: Kudu service overview
description: Learn about the engine that powers continuous deployment in App Service and its features.
ms.date: 03/17/2021
ms.topic: reference
---
# Kudu service overview

Kudu is the engine behind a number of features in [Azure App Service](overview.md) related to source control based deployment, and other deployment methods like Dropbox and OneDrive sync. 

## Access Kudu for your app
Anytime you create an app, App Service creates a companion app for it that's secured by HTTPS. This Kudu app is accessible at:

- App not in Isolated tier: `https://<app-name>.scm.azurewebsites.net`
- App in Isolated tier (App Service Environment): `https://<app-name>.scm.<ase-name>.p.azurewebsites.net`

For more information, see [Accessing the kudu service](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service).

## Kudu features

Kudu gives you helpful information about your App Service app, such as:

- App settings
- Connection strings
- Environment variables
- Server variables
- HTTP headers

It also provides other features, such as:

- Run commands in the [Kudu console](https://github.com/projectkudu/kudu/wiki/Kudu-console).
- Download IIS diagnostic dumps or Docker logs.
- Manage IIS processes and site extensions.
- Add deployment webhooks for Windows aps.
- Allow ZIP deployment UI with `/ZipDeploy`.
- Generates [custom deployment scripts](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script).
- Allows access with [REST API](https://github.com/projectkudu/kudu/wiki/REST-API).

## More Resources

Kudu is an [open source project](https://github.com/projectkudu/kudu), and has its documentation at [Kudu Wiki](https://github.com/projectkudu/kudu/wiki).

