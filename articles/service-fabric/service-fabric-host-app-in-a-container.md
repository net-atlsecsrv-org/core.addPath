---
title: Deploy a .NET app in a container to Azure Service Fabric 
description: Learn how to containerize an existing .NET application using Visual Studio and debug containers in Service Fabric locally. The containerized application is pushed to an Azure container registry and deployed to a Service Fabric cluster. When deployed to Azure, the application uses Azure SQL DB to persist data.

ms.topic: tutorial
ms.date: 07/08/2019
---

# Tutorial: Deploy a .NET application in a Windows container to Azure Service Fabric

This tutorial shows you how to containerize an existing ASP.NET application and package it as a Service Fabric application.  Run the containers locally on the Service Fabric development cluster and then deploy the application to Azure.  The application persists data in [Azure SQL Database](../azure-sql/database/sql-database-paas-overview.md).

In this tutorial, you learn how to:

> [!div class="checklist"]
>
> * Containerize an existing application using Visual Studio
> * Create a database in Azure SQL Database
> * Create an Azure container registry
> * Deploy a Service Fabric application to Azure

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## Prerequisites

1. If you don't have an Azure subscription, [create a free account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
2. Install [Docker CE for Windows](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description) so that you can run containers on Windows 10.
3. Install [Service Fabric runtime version 6.2 or later](service-fabric-get-started.md) and the [Service Fabric SDK version 3.1](service-fabric-get-started.md) or later.
4. Install [Visual Studio 2019 Version 16.1](https://www.visualstudio.com/) or later with the **Azure development** and **ASP.NET and web development** workloads.
5. Install [Azure PowerShell][link-azure-powershell-install]

## Download and run Fabrikam Fiber CallCenter

1. Download the [Fabrikam Fiber CallCenter][link-fabrikam-github] sample application.  Click the **download archive** link.  From the *sourceCode* directory in the *fabrikam.zip* file, extract the *sourceCode.zip* file and then extract the *VS2015* directory to your computer.

2. Verify that the Fabrikam Fiber CallCenter application builds and runs without error.  Launch Visual Studio as an **administrator** and open the [FabrikamFiber.CallCenter.sln][link-fabrikam-github] file.  Press F5 to debug and run the application.

   ![Screenshot of the Fabrikam Fiber CallCenter application home page running on the local host. The page shows a dashboard with a list of support calls.][fabrikam-web-page]

## Containerize the application

1. Right-click the **FabrikamFiber.Web** project > **Add** > **Container Orchestrator Support**.  Select **Service Fabric** as the container orchestrator and click **OK**.

2. If prompted, click **Yes** to switch Docker to Windows containers now.

   A new project Service Fabric application project **FabrikamFiber.CallCenterApplication** is created in the solution.  A Dockerfile is added to the existing **FabrikamFiber.Web** project.  A **PackageRoot** directory is also added to the **FabrikamFiber.Web** project, which contains the service manifest and settings for the new FabrikamFiber.Web service.

   The container is now ready to be built and packaged in a Service Fabric application. Once you have the container image built on your machine, you can push it to any container registry and pull it down to any host to run.

## Create an Azure SQL DB

When running the Fabrikam Fiber CallCenter application in production, the data needs to be persisted in a database. There is currently no way to guarantee persistent data in a container, therefore you cannot store production data in SQL Server in a container.

We recommend [Azure SQL Database](../azure-sql/database/powershell-script-content-guide.md). To set up and run a managed SQL Server DB in Azure, run the following script.  Modify the script variables as necessary. *clientIP* is the IP address of your development computer. Take note of the name of the server outputted by the script.

```powershell
$subscriptionID="<subscription ID>"

# Sign in to your Azure account and select your subscription.
Login-AzAccount -SubscriptionId $subscriptionID

# The data center and resource name for your resources.
$dbresourcegroupname = "fabrikam-fiber-db-group"
$location = "southcentralus"

# The server name: Use a random value or replace with your own value (do not capitalize).
$servername = "fab-fiber-$(Get-Random)"

# Set an admin login and password for your database.
# The login information for the server.
$adminlogin = "ServerAdmin"
$password = "Password@123"

# The IP address of your development computer that accesses the SQL DB.
$clientIP = "<client IP>"

# The database name.
$databasename = "call-center-db"

# Create a new resource group for your deployment and give it a name and a location.
New-AzResourceGroup -Name $dbresourcegroupname -Location $location

# Create the SQL server.
New-AzSqlServer -ResourceGroupName $dbresourcegroupname `
    -ServerName $servername `
    -Location $location `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))

# Create the firewall rule to allow your development computer to access the server.
New-AzSqlServerFirewallRule -ResourceGroupName $dbresourcegroupname `
    -ServerName $servername `
    -FirewallRuleName "AllowClient" -StartIpAddress $clientIP -EndIpAddress $clientIP

# Create the database in the server.
New-AzSqlDatabase  -ResourceGroupName $dbresourcegroupname `
    -ServerName $servername `
    -DatabaseName $databasename `
    -RequestedServiceObjectiveName "S0"

Write-Host "Server name is $servername"
```

> [!TIP]
> If you are behind a corporate firewall, the IP address of your development computer may not be IP address exposed to the internet. To verify that the database has the correct IP address for the firewall rule, go to the [Azure portal](https://portal.azure.com) and find your database in the SQL Databases section. Click on its name, then in the Overview section click "Set server firewall". "Client IP address" is the IP address of your development machine. Ensure that it matches the IP address in the "AllowClient" rule.

## Update the web config

Back in the **FabrikamFiber.Web** project, update the connection string in the **web.config** file, to point to the SQL Server in the container.  Update the *Server* part of the connection string to be the server name created by the previous script. It should be something like "fab-fiber-751718376.database.windows.net". In the following XML, you need to update only the `connectionString` attribute; the `providerName` and `name` attributes don't need to be changed.

```xml
<add name="FabrikamFiber-Express" connectionString="Server=<server name>,1433;Initial Catalog=call-center-db;Persist Security Info=False;User ID=ServerAdmin;Password=Password@123;MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;" providerName="System.Data.SqlClient" />
<add name="FabrikamFiber-DataWarehouse" connectionString="Server=<server name>,1433;Initial Catalog=call-center-db;Persist Security Info=False;User ID=ServerAdmin;Password=Password@123;MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;" providerName="System.Data.SqlClient" />
  
```

>[!NOTE]
>You can use any SQL Server you prefer for local debugging, as long as it is reachable from your host. However, **localdb** does not support `container -> host` communication. If you want to use a different SQL database when building a release build of your web application, add another connection string to your *web.release.config* file.

## Run the containerized application locally

Press **F5** to run and debug the application in a container on the local Service Fabric development cluster. Click **Yes** if presented with a message box asking to grant 'ServiceFabricAllowedUsers' group read and execute permissions to your Visual Studio project directory.

## Create a container registry

Now that the application runs locally, start preparing to deploy to Azure.  Container images need to be stored in a container registry.  Create an [Azure container registry](../container-registry/container-registry-intro.md) using the following script. The container registry name is visible by other Azure subscriptions, so it must be unique.
Before deploying the application to Azure, you push the container image to this registry.  When the application deploys to the cluster in Azure, the container image is pulled from this registry.

```powershell
# Variables
$acrresourcegroupname = "fabrikam-acr-group"
$location = "southcentralus"
$registryname="fabrikamregistry$(Get-Random)"

New-AzResourceGroup -Name $acrresourcegroupname -Location $location

$registry = New-AzContainerRegistry -ResourceGroupName $acrresourcegroupname -Name $registryname -EnableAdminUser -Sku Basic
```

## Create a Service Fabric cluster on Azure

Service Fabric applications run on a cluster, a network-connected set of virtual or physical machines.  Before you can deploy the application to Azure, create a Service Fabric cluster in Azure.

You can:

* Create a test cluster from Visual Studio. This option allows you to create a secure cluster directly from Visual Studio with your preferred configurations.
* [Create a secure cluster from a template](service-fabric-tutorial-create-vnet-and-windows-cluster.md)

This tutorial creates a cluster from Visual Studio, which is ideal for test scenarios. If you create a cluster some other way or use an existing cluster, you can copy and paste your connection endpoint or choose it from your subscription.

Before starting, open FabrikamFiber.Web->PackageRoot->ServiceManifest.xml in the Solution Explorer. Take note of the port for the web front-end listed in **Endpoint**.

When creating the cluster:

1. Right-click on the **FabrikamFiber.CallCenterApplication** application project in the Solution Explorer and choose **Publish**.
2. Sign in by using your Azure account so that you can have access to your subscription(s).
3. Below the dropdown for the **Connection Endpoint**, select the **Create New Cluster...** option.
4. In the **Create cluster** dialog, modify the following settings:

    a. Specify the name of your cluster in the **Cluster Name** field, as well as the subscription and location you want to use. Take note of the name of your cluster resource group.

    b. Optional: You can modify the number of nodes. By default you have three nodes, the minimum required for testing Service Fabric scenarios.

    c. Select the **Certificate** tab. In this tab, type a password to use to secure the certificate of your cluster. This certificate helps make your cluster secure. You can also modify the path to where you want to save the certificate. Visual Studio can also import the certificate for you, since this is a required step to publish the application to the cluster.

    d. Select the **VM Detail** tab. Specify the password you would like to use for the Virtual Machines (VM) that make up the cluster. The user name and password can be used to remotely connect to the VMs. You must also select a VM machine size and can change the VM image if needed.

    > [!IMPORTANT]
    > Choose a SKU that supports running containers. The Windows Server OS on your cluster nodes must be compatible with the Windows Server OS of your container. To learn more, see [Windows Server container OS and host OS compatibility](service-fabric-get-started-containers.md#windows-server-container-os-and-host-os-compatibility). By default, this tutorial creates a Docker image based on Windows Server 2016 LTSC. Containers based on this image will run on clusters created with Windows Server 2016 Datacenter with Containers. However, if you create a cluster or use an existing cluster based on a different version of Windows Server, you must change the OS image that the container is based on. Open the **dockerfile** in the **FabrikamFiber.Web** project, comment out any existing `FROM` statement based on a previous version of Windows Server, and add a `FROM` statement based on the desired version's tag from the [Windows Server Core DockerHub page](https://hub.docker.com/_/microsoft-windows-servercore). For additional information about Windows Server Core releases, support timelines, and versioning, refer to [Windows Server Core release info](/windows-server/get-started/windows-server-release-info). 

    e. In the **Advanced** tab, list the application port to open in the load balancer when the cluster deploys. This is the port that you took note of before starting creating the cluster. You can also add an existing Application Insights key to be used to route application log files to.

    f. When you are done modifying settings, select the **Create** button.

5. Creation takes several minutes to complete; the output window will indicate when the cluster is fully created.

## Allow your application running in Azure to access SQL Database

Previously, you created a SQL firewall rule to give access to your application running locally.  Next, you need to enable the application running in Azure to access the SQL DB.  Create a [virtual network service endpoint](../azure-sql/database/vnet-service-endpoint-rule-overview.md) for the Service Fabric cluster and then create a rule to allow that endpoint to access the SQL DB. Be sure to specify the cluster resource group variable that you took note of when creating the cluster.

```powershell
# Create a virtual network service endpoint
$clusterresourcegroup = "<cluster resource group>"
$resource = Get-AzResource -ResourceGroupName $clusterresourcegroup -ResourceType Microsoft.Network/virtualNetworks | Select-Object -first 1
$vnetName = $resource.Name

Write-Host 'Virtual network name: ' $vnetName

# Get the virtual network by name.
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName $clusterresourcegroup `
  -Name              $vnetName

Write-Host "Get the subnet in the virtual network:"

# Get the subnet, assume the first subnet contains the Service Fabric cluster.
$subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vnet | Select-Object -first 1

$subnetName = $subnet.Name
$subnetID = $subnet.Id
$addressPrefix = $subnet.AddressPrefix

Write-Host "Subnet name: " $subnetName " Address prefix: " $addressPrefix " ID: " $subnetID

# Assign a Virtual Service endpoint 'Microsoft.Sql' to the subnet.
$vnet = Set-AzVirtualNetworkSubnetConfig `
  -Name            $subnetName `
  -AddressPrefix   $addressPrefix `
  -VirtualNetwork  $vnet `
  -ServiceEndpoint Microsoft.Sql | Set-AzVirtualNetwork

$vnet.Subnets[0].ServiceEndpoints;  # Display the first endpoint.

# Add a SQL DB firewall rule for the virtual network service endpoint
$subnet = Get-AzVirtualNetworkSubnetConfig `
  -Name           $subnetName `
  -VirtualNetwork $vnet;

$VNetRuleName="ServiceFabricClusterVNetRule"
$vnetRuleObject1 = New-AzSqlServerVirtualNetworkRule `
  -ResourceGroupName      $dbresourcegroupname `
  -ServerName             $servername `
  -VirtualNetworkRuleName $VNetRuleName `
  -VirtualNetworkSubnetId $subnetID;
```

## Deploy the application to Azure

Now that the application is ready, you can deploy it to the cluster in Azure directly from Visual Studio.  In Solution Explorer, right-click the **FabrikamFiber.CallCenterApplication** application project and select **Publish**.  In **Connection Endpoint**, select the endpoint of the cluster that you created previously.  In **Azure Container Registry**, select the container registry that you created previously.  Click **Publish** to deploy the application to the cluster in Azure.

![Publish application][publish-app]

Follow the deployment progress in the output window. When the application is deployed, open a browser and type in the cluster address and application port. For example, `https://fabrikamfibercallcenter.southcentralus.cloudapp.azure.com:8659/`.

![Screenshot of the Fabrikam Fiber CallCenter application home page running on azure.com. The page shows a dashboard with a list of support calls.][fabrikam-web-page-deployed]

## Set up Continuous Integration and Deployment (CI/CD) with a Service Fabric cluster

To learn how to use Azure DevOps to configure CI/CD application deployment to a Service Fabric cluster, see
[Tutorial: Deploy an application with CI/CD to a Service Fabric cluster](service-fabric-tutorial-deploy-app-with-cicd-vsts.md). The process described in the tutorial is the same for this (FabrikamFiber) project, just skip downloading the Voting sample and substitute FabrikamFiber as the repository name instead of Voting.

## Clean up resources

If you're done, be sure to remove all the resources you created.  The simplest way to is to remove the resources groups that contain the Service Fabric cluster, Azure SQL DB, and Azure Container Registry.

```powershell
$dbresourcegroupname = "fabrikam-fiber-db-group"
$acrresourcegroupname = "fabrikam-acr-group"
$clusterresourcegroupname="fabrikamcallcentergroup"

# Remove the Azure SQL DB
Remove-AzResourceGroup -Name $dbresourcegroupname

# Remove the container registry
Remove-AzResourceGroup -Name $acrresourcegroupname

# Remove the Service Fabric cluster
Remove-AzResourceGroup -Name $clusterresourcegroupname
```

## Next steps

In this tutorial, you learned how to:

> [!div class="checklist"]
>
> * Containerize an existing application using Visual Studio
> * Create a database in Azure SQL Database
> * Create an Azure container registry
> * Deploy a Service Fabric application to Azure

In the next part of the tutorial, learn how to [Deploy a container application with CI/CD to a Service Fabric cluster](service-fabric-tutorial-deploy-container-app-with-cicd-vsts.md).

[link-fabrikam-github]: https://aka.ms/fabrikamcontainer
[link-azure-powershell-install]: /powershell/azure/install-Az-ps
[link-servicefabric-create-secure-clusters]: service-fabric-cluster-creation-via-arm.md
[link-visualstudio-cd-extension]: https://aka.ms/cd4vs
[link-servicefabric-containers]: service-fabric-get-started-containers.md
[link-azure-portal]: https://portal.azure.com
[link-sf-clustertemplate]: https://aka.ms/securepreviewonelineclustertemplate
[link-azure-pricing-calculator]: https://azure.microsoft.com/pricing/calculator/
[link-azure-subscription]: https://azure.microsoft.com/free/
[link-vsts-account]: https://www.visualstudio.com/team-services/pricing/
[link-azure-sql]: /azure/sql-database/

[fabrikam-web-page]: media/service-fabric-host-app-in-a-container/fabrikam-web-page.png
[fabrikam-web-page-deployed]: media/service-fabric-host-app-in-a-container/fabrikam-web-page-deployed.png
[publish-app]: media/service-fabric-host-app-in-a-container/publish-app.png