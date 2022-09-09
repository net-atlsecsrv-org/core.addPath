---
title: Quickly create a .NET app on Service Fabric in Azure
description: In this quickstart, you create a .NET application for Azure using the Service Fabric reliable services sample application.
ms.topic: quickstart
ms.date: 06/26/2019
ms.custom: mvc, devcenter, vs-azure
---
# Quickstart: Deploy a .NET reliable services application to Service Fabric

Azure Service Fabric is a distributed systems platform for deploying and managing scalable and reliable microservices and containers.

This quickstart shows how to deploy your first .NET application to Service Fabric. When you're finished, you have a voting application with an ASP.NET Core web front end that saves voting results in a stateful back-end service in the cluster.

![Application Screenshot](./media/service-fabric-quickstart-dotnet/application-screenshot.png)

Using this application you learn how to:

* Create an application using .NET and Service Fabric
* Use ASP.NET core as a web front end
* Store application data in a stateful service
* Debug your application locally
* Scale-out the application across multiple nodes
* Perform a rolling application upgrade

## Prerequisites

To complete this quickstart:

1. [Install Visual Studio 2019](https://www.visualstudio.com/) with the **Azure development** and **ASP.NET and web development** workloads.
2. [Install Git](https://git-scm.com/)
3. [Install the Microsoft Azure Service Fabric SDK](https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK)
4. Run the following command to enable Visual Studio to deploy to the local Service Fabric cluster:

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force -Scope CurrentUser
   ```
	
## Build a cluster

After you install the runtime, SDKs, Visual Studio tools, Docker, and have Docker running, create a five-node local development cluster.

> [!Note]
> The reason to have Docker running when you create the cluster is so that
> the cluster is created with container features enabled. If Docker is not running,
> you will have to recreate the cluster to enable container features.
> Although unnecessary for this particular quickstart, the instruction to have
> Docker running when you create the cluster is included as a best-practice.
> Test that Docker is running by opening a terminal window and running `docker ps` to see if an error occurs. If the response does not indicate an error, Docker is running and you're ready to build a cluster.
>
> [Set up Windows 10 or Windows Server for containers](/virtualization/windowscontainers/quick-start/set-up-environment?tabs=Windows-10-Client)

1. Open a new, elevated PowerShell window as an administrator.
2. Run the following PowerShell command to create a development cluster:

   ```powershell
   . "C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1"
   ```
3. Run the following command to start the local cluster manager tool:

   ```powershell
   . "C:\Program Files\Microsoft SDKs\Service Fabric\Tools\ServiceFabricLocalClusterManager\ServiceFabricLocalClusterManager.exe"
   ```

>[!NOTE]
> The sample application in this quickstart uses features that are not available on Windows 7.
>

## Download the sample

In a command window, run the following command to clone the sample app repository to your local machine.

```git
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

## Run the application locally

Right-click the Visual Studio icon in the Start Menu and choose **Run as administrator**. To attach the debugger to your services, you need to run Visual Studio as administrator.

Open the **Voting.sln** Visual Studio solution from the repository you cloned.

By default, the Voting application listens on port 8080.  The application port is set in the */VotingWeb/PackageRoot/ServiceManifest.xml* file.  You can change the application port by updating the **Port** attribute of the **Endpoint** element.  To deploy and run the application locally, the application port must be open and available on your computer.  If you change the application port, replace the new application port value for "8080" throughout this article.

To deploy the application, press **F5**.

> [!NOTE]
> In the Visual Studio output window, you will see the message "The application URL is not set or is not an HTTP/HTTPS URL so the browser will not be opened to the application."  This message does not indicate an error, but that a browser will not auto-launch.

When the deployment is complete, launch a browser and open `http://localhost:8080` to view the web front end of the application.

![Application front end](./media/service-fabric-quickstart-dotnet/application-screenshot-new.png)

You can now add a set of voting options, and start taking votes. The application runs and stores all data in your Service Fabric cluster, without the need for a separate database.

## Walk through the voting sample application

The voting application consists of two services:

* Web front-end service (VotingWeb)- An ASP.NET Core web front-end service, which serves the web page and exposes web APIs to communicate with the backend service.
* Back-end service (VotingData)- An ASP.NET Core web service, which exposes an API to store the vote results in a reliable dictionary persisted on disk.

![Application Diagram](./media/service-fabric-quickstart-dotnet/application-diagram.png)

When you vote in the application, the following events occur:

1. A JavaScript sends the vote request to the web API in the web front-end service as an HTTP PUT request.

2. The web front-end service uses a proxy to locate and forward an HTTP PUT request to the back-end service.

3. The back-end service takes the incoming request, and stores the updated result in a reliable dictionary, which gets replicated to multiple nodes within the cluster and persisted on disk. All the application's data is stored in the cluster, so no database is needed.

## Debug in Visual Studio

The application should be running OK, but you can use the debugger to see how key parts of the application work. When debugging the application in Visual Studio, you're using a local Service Fabric development cluster. You can adjust your debugging experience to your scenario. In this application, data is stored in back-end service using a reliable dictionary. Visual Studio removes the application per default when you stop the debugger. Removing the application causes the data in the back-end service to also be removed. To persist the data between debugging sessions, you can change the **Application Debug Mode** as a property on the **Voting** project in Visual Studio.

To look at what happens in the code, complete the following steps:

1. Open the **/VotingWeb/Controllers/VotesController.cs** file and set a breakpoint in the web API's **Put** method (line 69) - You can search for the file in the Solution Explorer in Visual Studio.

2. Open the **/VotingData/Controllers/VoteDataController.cs** file and set a breakpoint in this web API's **Put** method (line 54).

3. Go back to the browser and click a voting option or add a new voting option. You hit the first breakpoint in the web front end's api controller.
   * This step is where the JavaScript in the browser sends a request to the web API controller in the front-end service.

     ![Add Vote Front-End Service](./media/service-fabric-quickstart-dotnet/addvote-frontend.png)

   * First, construct the URL to the ReverseProxy for our back-end service **(1)**.
   * Then, send the HTTP PUT Request to the ReverseProxy **(2)**.
   * Finally, return the response from the back-end service to the client **(3)**.

4. Press **F5** to continue
   - If prompted by the browser, grant ServiceFabricAllowedUsers group read and execute permissions for Debug Mode.
   - You're now at the break point in the back-end service.

     ![Add Vote Back-End Service](./media/service-fabric-quickstart-dotnet/addvote-backend.png)

   - In the first line in the method **(1)** the `StateManager` gets or adds a reliable dictionary called `counts`.
   - All interactions with values in a reliable dictionary require a transaction, this using statement **(2)** creates that transaction.
   - In the transaction, update the value of the relevant key for the voting option and commit the operation **(3)**. Once the commit method returns, the data is updated in the dictionary and replicated to other nodes in the cluster. The data is now safely stored in the cluster, and the back-end service can fail over to other nodes, still having the data available.
5. Press **F5** to continue

To stop the debugging session, press **Shift+F5**.

## Perform a rolling application upgrade

When deploying new updates to your application, Service Fabric rolls out the update in a safe way. Rolling upgrades gives you no downtime while upgrading as well as automated rollback should errors occur.

To upgrade the application, do the following:

1. Open the **/VotingWeb/Views/Home/Index.cshtml** file in Visual Studio.
2. Change the heading on the page by adding or updating the text. For example, change the heading to "Service Fabric Voting Sample v2".
3. Save the file.
4. Right-click **Voting** in the Solution Explorer and choose **Publish**. The Publish dialog appears.
5. Click the **Manifest Version** button to change the version of the service and application.
6. Change the version of the **Code** element under **VotingWebPkg** to "2.0.0", for example, and click **Save**.

    ![Change Version Dialog](./media/service-fabric-quickstart-dotnet/change-version.png)
7. In the **Publish Service Fabric Application** dialog, check the **Upgrade the Application checkbox**.
8.  Change **Target profile** to **PublishProfiles\Local.5Node.xml** and ensure that **Connection Endpoint** is set to **Local Cluster**. 
9. Select **Upgrade the Application**.

    ![Publish Dialog Upgrade Setting](./media/service-fabric-quickstart-dotnet/upgrade-app.png)

10. Click **Publish**.

    While the upgrade is running, you can still use the application. Because you have two instances of the service running in the cluster, some of your requests may get an upgraded version of the application, while others may still get the old version.

11. Open your browser and browse to the cluster address on port 19080. For example, `http://localhost:19080/`.
12. Click on the **Applications** node in the tree view, and then **Upgrades in Progress** in the right-hand pane. You see how the upgrade rolls through the upgrade domains in your cluster, making sure each domain is healthy before proceeding to the next. An upgrade domain in the progress bar appears green when the health of the domain has been verified.
    ![Upgrade View in Service Fabric Explorer](./media/service-fabric-quickstart-dotnet/upgrading.png)

    Service Fabric makes upgrades safe by waiting two minutes after upgrading the service on each node in the cluster. Expect the entire update to take approximately eight minutes.

## Next steps

In this quickstart, you learned how to:

* Create an application using .NET and Service Fabric
* Use ASP.NET core as a web front end
* Store application data in a stateful service
* Debug your application locally
* Scale-out the application across multiple nodes
* Perform a rolling application upgrade

To learn more about Service Fabric and .NET, take a look at this tutorial:
> [!div class="nextstepaction"]
> [.NET application on Service Fabric](service-fabric-tutorial-create-dotnet-app.md)
