--- 
title: Automate the lifecycle of your apps with Visual Studio App Center and Azure services
description: Learn about the services such as App Center that helps set up continuous build and integration for your mobile applications.
author: elamalani
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
---

# Automate the lifecycle of your apps with continuous build and integration

As developers, you write code and check it in into the code repository but the commits checked into the repo might not always be consistent. With multiple developers working on the same project, issues come up with integration and the team would run into situations where things are not working, bugs keep piling up, and project development gets delayed. Developers have to wait till the entire software code is built and tested to check for errors and this makes the process slow and less iterative. 

With **Continuous Build and Integration**, developers can simplify build and test of their code by not only committing their changes to the source code repository but also putting tests and verifications into the build environment so they are always running tests against their code. All the changes made to the source code are build continuously whenever there is a commit made to the repository. With every check-in, the CI server validates and executes any test that the developer has created. If the tests don't pass, the code is sent back for further changes. This allows the developer not to break the builds that are created as well as not to run all the tests locally on their computer which in turn increases developer productivity. 

## Key benefits
- **Automated** build, test, and deploy pipeline.
- **Detect bugs and fix issues** early to ensure faster release rates.
- **Commit code** more frequently and build applications fast.
- **Flexibility** to change code quickly without any issues.
- **Faster time-to-market** ensures that only good quality code makes it all the way through.
- **Small code changes** as it allows to integrate small pieces of code at one time.
- **Increase Team transparency and accountability** lets you get **continuous feedback** not only from your customers but your team as well.

Use the following services to enable continuous integration pipeline in your mobile apps.

## Visual Studio App Center
[App Center Build](/appcenter/build/) service helps you build native and cross platform applications that your team is working on, using a secure cloud infrastructure. You can easily connect your repo in App Center and start building your app in the cloud on every commit without having to worry about configuring build servers locally, complicated configurations, and code that builds on a coworker's machine but not yours.

With the added power of App Center services, you can further automate your workflow. Automatically release builds to testers and public app stores with App Center Distribute, or run automated UI tests on thousands of real device and OS configurations in the cloud with App Center Test.

**Key features**
- **Set up Continuous integration** in minutes and build applications more frequently and faster.
- Integrate with **GitHub, BitBucket, Azure DevOps and GitLab**.
- **Fast and secure builds** on managed, cloud-hosted machines.
- **Enable your builds to "Launch test"** and verify whether the app builds in real-world iOS and Android devices.
- **Native and Cross-Platform Support** - iOS, Android, macOS, Windows, Xamarin, React Native.
- **Customize your builds** by adding post-clone, pre-build, and post-build scripts.

**References**
- [Sign up with App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Get started with App Center Build](/appcenter/build/)

## Azure Pipelines
 [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/), a service in Azure DevOps, is a fully-featured continuous integration (CI) and continuous delivery (CD) service that works with your preferred Git provider and can deploy to most major cloud services, including Azure. You can start with your code on GitHub, GitHub Enterprise Server, GitLab, Bitbucket Cloud, or Azure Repos. Then you can automate the build, testing, and deployment of your code to Microsoft Azure, Google Cloud Platform, or Amazon Web Services.

**Key features**
- Simplified task-based experience for setting up a CI server for both native (Android, iOS, and Windows) and cross-platform (Xamarin, Cordova,and React Native) mobile applications, in addition to Microsoft and non-Microsoft (Node.js, Java) based server technologies.
- **Any language, platform, and cloud** - Build, test, and deploy Node.js, Python, Java, PHP, Ruby, Go, C/C++, C#, Android, and iOS applications. Run in parallel on Linux, macOS, and Windows. Deploy to cloud providers like Azure, AWS, and GCP. Distribute mobile applications through beta channels and app stores.
- **Native Container support** - Create new containers with ease and push them to any registry. Deploy containers to independent hosts or Kubernetes.
- **Advanced workflows** - Easy build chaining and multi-phased builds. Support for YAML, test integration, release gates, reporting, and more.
- **Extensible** - Use a range of build, test, and deployment tasks built by the community – hundreds of extensions from Slack to SonarCloud. You can even deploy from other CI systems, like Jenkins, and use webhooks and REST APIs to help you integrate
- **Free cloud-hosted builds** for public and private repositories.
- **Supports Deployment to other cloud vendors** like Amazon Web Services, Google Cloud Platform etc.

**References**
- [Get started with Azure Pipelines guide](/azure/devops/pipelines/get-started/pipelines-get-started?view=azure-devops)
- [Get started with Azure DevOps](https://app.vsaex.visualstudio.com/signup/) 
- [Quickstarts](/azure/devops/pipelines/create-first-pipeline?view=azure-devops&tabs=tfs-2018-2)

In order the choose the right service for your application builds, follow the article that compares [App Center Build vs. Azure Pipelines](/appcenter/build/choose-between-services).
