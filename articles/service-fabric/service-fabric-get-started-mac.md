---
title: Set up your dev environment on macOS
description: Install the runtime, SDK, and tools and create a local development cluster. After completing this setup, you'll be ready to build applications on macOS.

ms.topic: conceptual
ms.date: 10/16/2020
ms.custom: devx-track-js

# Maintainer notes: Keep these documents in sync:
# service-fabric-get-started-linux.md
# service-fabric-get-started-mac.md
# service-fabric-local-linux-cluster-windows.md
---
# Set up your development environment on Mac OS X
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md)
> * [Linux](service-fabric-get-started-linux.md)
> * [Mac OS X](service-fabric-get-started-mac.md)

You can build Azure Service Fabric applications to run on Linux clusters by using Mac OS X. This document covers how to set up your Mac for development.

## Prerequisites
Azure Service Fabric doesn't run natively on Mac OS X. To run a local Service Fabric cluster, a pre-configured Docker container image is provided. Before you get started, you need:

* At least 4 GB of RAM.
* The latest version of [Docker](https://www.docker.com/).

>[!TIP]
>
>To install Docker on your Mac, follow the steps in the [Docker documentation](https://docs.docker.com/docker-for-mac/install/#what-to-know-before-you-install). After installing, [verify your installation](https://docs.docker.com/docker-for-mac/#check-versions-of-docker-engine-compose-and-machine).
>

## Create a local container and set up Service Fabric
To set up a local Docker container and have a Service Fabric cluster running on it, perform the following steps:

1. Update the Docker daemon configuration on your host with the following settings and restart the Docker daemon: 

    ```json
    {
        "ipv6": true,
        "fixed-cidr-v6": "fd00::/64"
    }
    ```
    You can update these settings directly in the daemon.json file in your Docker installation path. You can directly modify the daemon configuration settings in Docker. Select the **Docker icon**, and then select **Preferences** > **Daemon** > **Advanced**.
    
    >[!NOTE]
    >
    >Modifying the daemon directly in Docker is recommended because the location of the daemon.json file can vary from machine to machine. For example,
    > ~/Library/Containers/com.docker.docker/Data/database/com.docker.driver.amd64-linux/etc/docker/daemon.json.
    >

    >[!TIP]
    >We recommend increasing the resources allocated to Docker when testing large applications. This can be done by selecting the **Docker Icon**, then selecting **Advanced** to adjust the number of cores and memory.

2. Start the cluster.<br/>
    <b>Ubuntu 18.04 LTS:</b>
    ```bash
    docker run --name sftestcluster -d -v /var/run/docker.sock:/var/run/docker.sock -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 mcr.microsoft.com/service-fabric/onebox:u18
    ```

    <b>Ubuntu 16.04 LTS:</b>
    ```bash
    docker run --name sftestcluster -d -v /var/run/docker.sock:/var/run/docker.sock -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 mcr.microsoft.com/service-fabric/onebox:u16
    ```

    >[!TIP]
    > By default, this will pull the image with the latest version of Service Fabric. For particular revisions, please visit the [Service Fabric Onebox](https://hub.docker.com/_/microsoft-service-fabric-onebox) page on Docker Hub.



3. Optional: Build your extended Service Fabric image.

    In a new directory, create a file called `Dockerfile` to build your customized image:

    >[!NOTE]
    >You can adapt the image above with a Dockerfile to add additional programs or dependencies into your container.
    >For example, adding `RUN apt-get install nodejs -y` will allow support for `nodejs` applications as guest executables.
    ```Dockerfile
    FROM mcr.microsoft.com/service-fabric/onebox:u18
    RUN apt-get install nodejs -y
    EXPOSE 19080 19000 80 443
    WORKDIR /home/ClusterDeployer
    CMD ["./ClusterDeployer.sh"]
    ```
    
    >[!TIP]
    > By default, this will pull the image with the latest version of Service Fabric. For particular revisions, please visit the [Docker Hub](https://hub.docker.com/r/microsoft/service-fabric-onebox/) page.

    To build your reusable image from the `Dockerfile`, open a terminal and `cd` to the directly holding your `Dockerfile` then run:

    ```bash 
    docker build -t mysfcluster .
    ```
    
    >[!NOTE]
    >This operation will take some time but is only needed once.

    Now you can quickly start a local copy of Service Fabric whenever you need it by running:

    ```bash 
    docker run --name sftestcluster -d -v /var/run/docker.sock:/var/run/docker.sock -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 mysfcluster
    ```

    >[!TIP]
    >Provide a name for your container instance so it can be handled in a more readable manner. 
    >
    >If your application is listening on certain ports, the ports must be specified by using additional `-p` tags. For example, if your application is listening on port 8080, add the following `-p` tag:
    >
    >`docker run -itd -p 19000:19000 -p 19080:19080 -p 8080:8080 --name sfonebox mcr.microsoft.com/service-fabric/onebox:u18`
    >

4. The cluster will take a moment to start. When it is running, you can view logs using the following command or jump to the dashboard to view the clusters health: `http://localhost:19080`

    ```bash 
    docker logs sftestcluster
    ```


5. To stop and clean up the container, use the following command. However, we will be using this container in the next step.

    ```bash 
    docker rm -f sftestcluster
    ```

### Known Limitations 
 
 The following are known limitations of the local cluster running in a container for Mac's: 
 
 * DNS service does not run and is currently not supported within the container. [Issue #132](https://github.com/Microsoft/service-fabric/issues/132)
 * Running container-based apps requires running SF on a Linux host. Nested container apps are currently not supported.

## Set up the Service Fabric CLI (sfctl) on your Mac

Follow the instructions at [Service Fabric CLI](service-fabric-cli.md#cli-mac) to install the Service Fabric CLI (`sfctl`) on your Mac.
The CLI commands support interacting with Service Fabric entities, including clusters, applications, and services.

1. To connect to the cluster before deploying applications run the command below. 

```bash
sfctl cluster select --endpoint http://localhost:19080
```

## Create your application on your Mac by using Yeoman

Service Fabric provides scaffolding tools that help you to create a Service Fabric application from the terminal by using the Yeoman template generator. Use the following steps to ensure that the Service Fabric Yeoman template generator is working on your machine:

1. Node.js and Node Package Manager (NPM) must be installed on your Mac. The software can be installed by using [HomeBrew](https://brew.sh/), as follows:

    ```bash
    brew install node
    node -v
    npm -v
    ```
2. Install the [Yeoman](https://yeoman.io/) template generator on your machine from NPM:

    ```bash
    npm install -g yo
    ```
3. Install the Yeoman generator that you prefer by following the steps in the getting started [documentation](service-fabric-get-started-linux.md#set-up-yeoman-generators-for-containers-and-guest-executables). To create Service Fabric applications by using Yeoman, follow these steps:

    ```bash
    npm install -g generator-azuresfjava       # for Service Fabric Java Applications
    npm install -g generator-azuresfguest      # for Service Fabric Guest executables
    npm install -g generator-azuresfcontainer  # for Service Fabric Container Applications
    ```
4. After you install the generators, create guest executable or container services by running `yo azuresfguest` or `yo azuresfcontainer`, respectively.

5. To build a Service Fabric Java application on your Mac, JDK version 1.8 and Gradle must be installed on the host machine. The software can be installed by using [HomeBrew](https://brew.sh/), as follows: 

    ```bash
    brew update
    brew cask install java
    brew install gradle
    ```

    > [!IMPORTANT]
    > Current versions of `brew cask install java` may install a more recent version of the JDK.
    > Be sure to install JDK 8.

## Deploy your application on your Mac from the terminal

After you create and build your Service Fabric application, you can deploy your application by using the [Service Fabric CLI](service-fabric-cli.md#cli-mac):

1. Connect to the Service Fabric cluster that is running inside the container instance on your Mac:

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```

2. From inside your project directory, run the install script:

    ```bash
    cd MyProject
    bash install.sh
    ```

## Set up .NET Core 3.1 development

Install the [.NET Core 3.1 SDK for Mac](https://www.microsoft.com/net/core#macos) to start [creating C# Service Fabric applications](service-fabric-create-your-first-linux-application-with-csharp.md). Packages for .NET Core Service Fabric applications are hosted on NuGet.org.

## Install the Service Fabric plug-in for Eclipse on your Mac

Azure Service Fabric provides a plug-in for Eclipse Neon (or later) for the Java IDE. The plug-in simplifies the process of creating, building, and deploying Java services. To install or update the Service Fabric plug-in for Eclipse to the latest version, follow [these steps](service-fabric-get-started-eclipse.md#install-or-update-the-service-fabric-plug-in-in-eclipse). The other steps in the [Service Fabric for Eclipse documentation](service-fabric-get-started-eclipse.md) are also applicable: build an application, add a service to an application, uninstall an application, and so on.

The last step is to instantiate the container with a path that is shared with your host. The plug-in requires this type of instantiation to work with the Docker container on your Mac. For example:

```bash
docker run -itd -p 19080:19080 -v /Users/sayantan/work/workspaces/mySFWorkspace:/tmp/mySFWorkspace --name sfonebox mcr.microsoft.com/service-fabric/onebox:latest
```

The attributes are defined as follows:
* `/Users/sayantan/work/workspaces/mySFWorkspace` is the fully qualified path of the workspace on your Mac.
* `/tmp/mySFWorkspace` is the path that is inside of the container to where the workspace should be mapped.

>[!NOTE]
> 
>If you have a different name/path for your workspace, update these values in the `docker run` command.
> 
>If you start the container with a name other than `sfonebox`, update the name value in the testclient.sh file in your Service Fabric actor Java application.
>

## Next steps
<!-- Links -->
* [Create and deploy your first Service Fabric Java application on Linux using Yeoman](service-fabric-create-your-first-linux-application-with-java.md)
* [Create and deploy your first Service Fabric Java application on Linux using Service Fabric plug-in for Eclipse](service-fabric-get-started-eclipse.md)
* [Create a Service Fabric cluster in the Azure portal](service-fabric-cluster-creation-via-portal.md)
* [Create a Service Fabric cluster by using Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
* [Understand the Service Fabric application model](service-fabric-application-model.md)
* [Use the Service Fabric CLI to manage your applications](service-fabric-application-lifecycle-sfctl.md)
* [Prepare a Linux development environment on Windows](service-fabric-local-linux-cluster-windows.md)

<!-- Images -->
[cluster-setup-script]: ./media/service-fabric-get-started-mac/cluster-setup-mac.png
[sfx-mac]: ./media/service-fabric-get-started-mac/sfx-mac.png
[sf-eclipse-plugin-install]: ./media/service-fabric-get-started-mac/sf-eclipse-plugin-install.png
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship
