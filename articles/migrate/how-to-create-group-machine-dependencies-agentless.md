---
title: Set up agentless dependency analysis in Azure Migrate
description:  Set up agentless dependency analysis in Azure Migrate.
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: how-to
ms.date: 6/08/2020
---


# Analyze server dependencies (agentless)

This article describes how to set up agentless dependency analysis using Azure Migrate:Discovery and assessment tool. [Dependency analysis](concepts-dependency-visualization.md) helps you to identify and understand dependencies across servers for assessment and migration to Azure.

> [!IMPORTANT]
> Agentless dependency analysis is currently in preview for servers running in your VMware environment, discovered with the Azure Migrate:Discovery and assessment tool.
> This preview is covered by customer support and can be used for production workloads.
> For more information, see [Supplemental Terms of Use for Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## Current limitations

- In the dependency analysis view, you currently cannot add or remove a server from a group.
- A dependency map for a group of servers isn't currently available.
- In an Azure Migrate project, dependency data collection can be enabled concurrently for 1000 servers. You can analyze a higher number of servers by sequencing in batches of 1000 servers.

## Before you start

- Ensure that you have [created a project](./create-manage-projects.md) with the Azure Migrate:Discovery and assessment tool added to it.
- Review [VMware requirements](migrate-support-matrix-vmware.md#vmware-requirements) to perform dependency analysis.
- Review [appliance requirements](migrate-support-matrix-vmware.md#azure-migrate-appliance-requirements) before setting up the appliance.
- [Review dependency analysis requirements](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) before enabling dependency analysis on servers.

## Deploy and configure the Azure Migrate appliance

1. [Review](migrate-appliance.md#appliance---vmware) the requirements for deploying the Azure Migrate appliance.
2. Review the Azure URLs that the appliance will need to access in the [public](migrate-appliance.md#public-cloud-urls) and [government clouds](migrate-appliance.md#government-cloud-urls).
3. [Review data](migrate-appliance.md#collected-data---vmware) that the appliance collects during discovery and assessment.
4. [Note](migrate-support-matrix-vmware.md#port-access-requirements) port access requirements for the appliance.
5. [Deploy the Azure Migrate appliance](how-to-set-up-appliance-vmware.md) to start discovery. To deploy the appliance, you download and import an OVA template into VMware to create a server running in your vCenter Server. After deploying the appliance, you need to register it with the project and configure it to initiate the discovery.
6. As you configure the appliance, you need to specify the following in the appliance configuration manager:
    - The details of the vCenter Server to which you want to connect.
    - vCenter Server credentials scoped to discover the servers in your VMware environment.
    - Server credentials which can be domain/ Windows(non-domain)/ Linux(non-domain) credentials. [Learn more](add-server-credentials.md) about how to provide credentials and how we handle them.

## Verify permissions

- You need to [create a vCenter Server read-only account](./tutorial-discover-vmware.md#prepare-vmware) for discovery and assessment. The read-only account needs privileges enabled for **Virtual Machines** > **Guest Operations**, in order to interact with the servers to collect dependency data.
- You need a user account so that Azure Migrate can access the server to collect dependency data. [Learn](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) about account requirements for Windows and Linux servers.

### Add credentials and initiate discovery

1. Open the appliance configuration manager, complete the prerequisite checks and registration of the appliance.
2. Navigate to the **Manage credentials and discovery sources** panel.
1.  In **Step 1: Provide vCenter Server credentials**, click on **Add credentials** to  provide credentials for the vCenter Server account that the appliance will use to discover servers running on the vCenter Server.
1. In **Step 2: Provide vCenter Server details**, click on **Add discovery source** to select the friendly name for credentials from the drop-down, specify the **IP address/FQDN** of the vCenter Server instance
:::image type="content" source="./media/tutorial-discover-vmware/appliance-manage-sources.png" alt-text="Panel 3 on appliance configuration manager for vCenter Server details":::
1. In **Step 3: Provide server credentials to perform software inventory, agentless dependency analysis and discovery of SQL Server instances and databases**, click **Add credentials** to provide multiple server credentials to initiate software inventory.
1. Click on **Start discovery**, to kick off vCenter Server discovery.

 After the vCenter Server discovery is complete, appliance initiates the discovery of installed applications, roles and features (software inventory).During software inventory, the added servers credentials will be iterated against servers and validated for agentless dependency analysis.You can enable agentless dependency analysis for servers from the portal. Only the servers where the validation succeeds can be selected to enable agentless dependency analysis.

## Start dependency discovery

Select the servers on which you want to enable dependency discovery.

1. In **Azure Migrate: Discovery and assessment**, click **Discovered servers**.
2. Choose the **Appliance name** whose discovery you want to review.
1. You can see the validation status of the servers under **Dependencies (agentless)** column.
1. Click the **Dependency analysis** drop-down.
1. Click **Add servers**.
1. In the **Add servers** page, select the servers where you want to enable dependency analysis. You can enable dependency mapping only on those servers where validation succeeded. The next validation cycle will run 24 hours after the last validation timestamp.
1. After selecting the servers, click **Add servers**.

:::image type="content" source="./media/how-to-create-group-machine-dependencies-agentless/start-dependency-discovery.png" alt-text="Start dependency analysis":::

You can visualize dependencies around six hours after enabling dependency analysis on servers. If you want to simultaneously enable multiple servers for dependency analysis, you can use [PowerShell](#start-or-stop-dependency-analysis-using-powershell) to do so.

## Visualize dependencies

1. In **Azure Migrate: Discovery and assessment**, click **Discovered servers**.
1. Choose the **Appliance name** whose discovery you want to review.
1. Search for the server whose dependencies, you want to review.
1. Under the **Dependencies (agentless)** column, click **View dependencies**
1. Change the time period for which you want to view the map using the **Time duration** dropdown.
1. Expand the **Client** group to list the servers with a dependency on the selected server.
1. Expand the **Port** group to list the servers that have a dependency from the selected server.
1. To navigate to the map view of any of the dependent servers, click on the server name > **Load server map**
:::image type="content" source="./media/how-to-create-group-machine-dependencies-agentless/load-server-map.png" alt-text="Expand Server port group and load server map":::
:::image type="content" source="./media/how-to-create-group-machine-dependencies-agentless/expand-client-group.png" alt-text="Expand client group":::

8. Expand the selected server to view process-level details for each dependency.
:::image type="content" source="./media/how-to-create-group-machine-dependencies-agentless/expand-server-processes.png" alt-text="Expand server to show processes":::

> [!NOTE]
> Process information for a dependency is not always available. If it's not available, the dependency is depicted with the process marked as "Unknown process".

## Export dependency data

1. In **Azure Migrate: Discovery and assessment**, click **Discovered servers**.
2. Click the **Dependency analysis** drop-down.
3. Click **Export application dependencies**.
4. In the **Export application dependencies** page, choose the appliance name that is discovering the desired servers.
5. Select the start time and end time. Note that you can download the data only for the last 30 days.
6. Click **Export dependency**.

The dependency data is exported and downloaded in a CSV format. The downloaded file contains the dependency data across all servers enabled for dependency analysis. 
:::image type="content" source="./media/how-to-create-group-machine-dependencies-agentless/export.png" alt-text="Export dependencies":::

### Dependency information

Each row in the exported CSV corresponds to a dependency observed in the specified time slot.

The following table summarizes the fields in the exported CSV. Note that server name, application and process fields are populated only for servers that have agentless dependency analysis enabled.

**Field name** | **Details**
--- | --- 
Timeslot | The timeslot during which the dependency was observed. <br/> Dependency data is captured over 6-hour slots currently.
Source server name | Name of the source server 
Source application | Name of the application on the source server  
Source process | Name of the process on the source server  
Destination server name | Name of the destination server
Destination IP | IP address of the destination server
Destination application | Name of the application on the destination server
Destination process | Name of the process on the destination server  
Destination port | Port number on the destination server

## Stop dependency discovery

Select the servers on which you want to stop dependency discovery.

1. In **Azure Migrate: Discovery and assessment**, click **Discovered servers**.
1. Choose the **Appliance name** whose discovery you want to review.
1. Click the **Dependency analysis** drop-down.
1. Click **Remove servers**.
1. In the **Remove servers** page, select the server which you want to stop for dependency analysis.
1. After selecting the servers,click **Remove servers**.

If you want to stop dependency simultaneously on multiple servers, you can use [PowerShell](#start-or-stop-dependency-analysis-using-powershell) to do so.

## Start or stop dependency analysis using PowerShell

Download the PowerShell module from [Azure PowerShell Samples](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/dependencies-at-scale) repo on GitHub.

### Log in to Azure

1. Log into your Azure subscription using the Connect-AzAccount cmdlet.

    ```PowerShell
    Connect-AzAccount
    ```
    If using Azure Government, use the following command.
    ```PowerShell
    Connect-AzAccount -EnvironmentName AzureUSGovernment
    ```

2. Select the subscription in which you have created the project 

    ```PowerShell
    select-azsubscription -subscription "Fabrikam Demo Subscription"
    ```

3. Import the downloaded AzMig_Dependencies PowerShell module

    ```PowerShell
    Import-Module .\AzMig_Dependencies.psm1
    ```

### Enable or disable dependency data collection

1. Get the list of discovered servers in your project using the following commands. In the example below, the project name is FabrikamDemoProject, and the resource group it belongs to is FabrikamDemoRG. The list of servers will be saved in FabrikamDemo_VMs.csv

    ```PowerShell
    Get-AzMigDiscoveredVMwareVMs -ResourceGroupName "FabrikamDemoRG" -ProjectName "FabrikamDemoProject" -OutputCsvFile "FabrikamDemo_VMs.csv"
    ```

    In the file, you can see the server display name, current status of dependency collection and the ARM ID of all discovered servers. 

2. To enable or disable dependencies, create an input CSV file. The file is required to have a column with header "ARM ID". Any additional headers in the CSV file will be ignored. You can create the CSV using the file generated in the previous step. Create a copy of the file retaining the servers you want to enable or disable dependencies on. 

    In the following example, dependency analysis is being enabled on the list of servers in the input file FabrikamDemo_VMs_Enable.csv.

    ```PowerShell
    Set-AzMigDependencyMappingAgentless -InputCsvFile .\FabrikamDemo_VMs_Enable.csv -Enable
    ```

    In the following example, dependency analysis is being disabled on the list of servers in the input file FabrikamDemo_VMs_Disable.csv.

    ```PowerShell
    Set-AzMigDependencyMappingAgentless -InputCsvFile .\FabrikamDemo_VMs_Disable.csv -Disable
    ```

## Visualize network connections in Power BI

Azure Migrate offers a Power BI template that you can use to visualize network connections of many servers at once, and filter by process and server. To visualize, load the Power BI with dependency data as per the below instructions.

1. Download the PowerShell module and the Power BI template from [Azure PowerShell Samples](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/dependencies-at-scale) repo on GitHub.

2. Log in to Azure using the below instructions: 
    - Log into your Azure subscription using the Connect-AzAccount cmdlet.

        ```PowerShell
        Connect-AzAccount
        ```

    - If using Azure Government, use the following command.

        ```PowerShell
        Connect-AzAccount -EnvironmentName AzureUSGovernment
        ```

    - Select the subscription in which you have created the project

        ```PowerShell
        select-azsubscription -subscription "Fabrikam Demo Subscription"
        ```

3. Import the downloaded AzMig_Dependencies PowerShell module

    ```PowerShell
    Import-Module .\AzMig_Dependencies.psm1
    ```

4. Run the following command. This command downloads the dependencies data in a CSV and processes it to generate a list of unique dependencies that can be used for visualization in Power BI. In the example below the project name is FabrikamDemoProject, and the resource group it belongs to is FabrikamDemoRG. The dependencies will be downloaded for servers discovered by FabrikamAppliance. The unique dependencies will be saved in FabrikamDemo_Dependencies.csv

    ```PowerShell
    Get-AzMigDependenciesAgentless -ResourceGroup FabrikamDemoRG -Appliance FabrikamAppliance -ProjectName FabrikamDemoProject -OutputCsvFile "FabrikamDemo_Dependencies.csv"
    ```

5. Open the downloaded Power BI template

6. Load the downloaded dependency data in Power BI.
    - Open the template in Power BI.
    - Click on **Get Data** on the tool bar. 
    - Choose **Text/CSV** from Common data sources.
    - Choose the dependencies file downloaded.
    - Click **Load**.
    - You will see a table is imported with the name of the CSV file. You can see the table in the fields bar on the right. Rename it to AzMig_Dependencies
    - Click on refresh from the tool bar.

    The Network Connections chart and the Source server name, Destination server name, Source process name, Destination process name slicers should light up with the imported data.

7. Visualize the map of network connections filtering by servers and processes. Save your file.

## Next steps

[Group servers](how-to-create-a-group.md) for assessment.