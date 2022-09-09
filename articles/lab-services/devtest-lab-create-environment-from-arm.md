---
title: Create multi-VM environments and PaaS resources with Azure Resource Manager templates | Microsoft Docs
description: Learn how to create multi-VM environments and PaaS resources in Azure DevTest Labs from an Azure Resource Manager template
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''

ms.assetid: 
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2018
ms.author: spelluru
---

# Create multi-VM environments and PaaS resources with Azure Resource Manager templates

The [Azure portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) enables you to easily [add one VM at a time to a lab](https://docs.microsoft.com/azure/devtest-lab/devtest-lab-add-vm). However, if the environment contains multiple VMs, each VM must be individually created. For scenarios such as a multi-tier Web app or a SharePoint farm, a mechanism is needed to allow for the creation of multiple VMs in a single step. By using Azure Resource Manager templates, you can now define the infrastructure and configuration of your Azure solution and repeatedly deploy multiple VMs in a consistent state. This feature provides the following benefits:

- Azure Resource Manager templates are loaded directly from your source control repository (GitHub or Azure DevOps Services Git).
- Once configured, your users can create an environment by picking an Azure Resource Manager template from the Azure portal, just as they do with other types of [VM bases](./devtest-lab-comparing-vm-base-image-types.md).
- Azure PaaS resources can be provisioned in an environment from an Azure Resource Manager template in addition to IaaS VMs.
- The cost of environments can be tracked in the lab in addition to individual VMs created by other types of bases.
- PaaS resources are created and will appear in cost tracking; however, VM auto shutdown does not apply to PaaS resources.

Learn more about the many [benefits of using Resource Manager templates](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#the-benefits-of-using-resource-manager) to deploy, update, or delete all of your lab resources in a single operation.

> [!NOTE]
> When you use a Resource Manager template as a basis to create more lab VMs, there are some differences to keep in mind whether you are creating Multi-VMs or single-VMs. [Use a virtual machine's Azure Resource Manager template](devtest-lab-use-resource-manager-template.md) explains these differences in greater detail.
>

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## DevTest Labs public environments
Azure DevTest Labs has a [public repository of Azure Resource Manager templates](https://github.com/Azure/azure-devtestlab/tree/master/Environments) that you can use to create environments without having to connect to an external GitHub source by yourself. This repository includes frequently used templates such as Azure Web Apps, Service Fabric Cluster, and development SharePoint Farm environment. This feature is similar to the public repository of artifacts that is included for every lab that you create. The environment repository allows you to quickly get started with pre-authored environment templates with minimum input parameters to provide you with a smooth getting started experience for PaaS resources within labs. For more information, see [Configure and use public environments in DevTest Labs](devtest-lab-configure-use-public-environments.md).

## Configure your own template repositories
As one of the best practices with infrastructure-as-code and configuration-as-code, environment templates should be managed in source control. Azure DevTest Labs follows this practice and loads all Azure Resource Manager templates directly from your GitHub or Azure DevOps Services Git repositories. As a result, Resource Manager templates can be used across the entire release cycle, from the test environment to the production environment.

Check out the templates created by the DevTest Labs team in the [public GitHub repository](https://github.com/Azure/azure-devtestlab/tree/master/Environments). In this public repository, you can view templates shared by others that you can use directly or customize them to suit your needs. After you create your template, store it in this repository to share it with others. You can also set up your own Git repository with templates that can be used to set up environments in the cloud. 

There are a couple of rules to follow to organize your Azure Resource Manager templates in a repository:

- The master template file must be named `azuredeploy.json`. 

	![Key Azure Resource Manager template files](./media/devtest-lab-create-environment-from-arm/master-template.png)

- If you want to use parameter values defined in a parameter file, the parameter file must be named `azuredeploy.parameters.json`.
- You can use the parameters `_artifactsLocation` and `_artifactsLocationSasToken` to construct the parametersLink URI value, allowing DevTest Labs to automatically manage nested templates. For more information, see [Deploy nested Azure Resource Manager templates for testing environments](deploy-nested-template-environments.md).
- Metadata can be defined to specify the template display name and description. This metadata must be in a file named `metadata.json`. The following example metadata file illustrates how to specify the display name and description: 

    ```json
    { 
        "itemDisplayName": "<your template name>", 
        "description": "<description of the template>" 
	}
    ```

The following steps guide you through adding a repository to your lab using the Azure portal. 

1. Sign in to the [Azure portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Select **All Services**, and then select **DevTest Labs** from the list.
1. From the list of labs, select the desired lab.   
1. On the lab's **Overview** pane, select **Configuration and Policies**.

	![Configuration and policies](./media/devtest-lab-create-environment-from-arm/configuration-and-policies-menu.png)

1. From the **Configuration and Policies** settings list, select **Repositories**. The **Repositories** pane lists the repositories that have been added to the lab. A repository named `Public Repo` is automatically generated for all labs, and connects to the [DevTest Labs GitHub repo](https://github.com/Azure/azure-devtestlab) that contains several VM artifacts for your use.

	![Public repo](./media/devtest-lab-create-environment-from-arm/public-repo.png)

1. Select **Add+** to add your Azure Resource Manager template repository.
1. When the second **Repositories** pane opens, enter the necessary information as follows:
	- **Name** - Enter the repository name that is used in the lab.
	- **Git clone URL** - Enter the GIT HTTPS clone URL from GitHub or Azure DevOps Services.  
	- **Branch** - Enter the branch name to access your Azure Resource Manager template definitions. 
	- **Personal access token** - The personal access token is used to securely access your repository. To get your token from Azure DevOps Services, select **&lt;YourName> > My profile > Security > Public access token**. To get your token from GitHub, select your avatar followed by selecting **Settings > Public access token**. 
	- **Folder paths** - Using one of the two input fields, enter the folder path that starts with a forward slash - / - and is relative to your Git clone URI to either your artifact definitions (first input field) or your Azure Resource Manager template definitions.   
	
		![Public repo](./media/devtest-lab-create-environment-from-arm/repo-values.png)


1. Once all the required fields are entered and pass the validation, select **Save**.

The next section will walk you through creating environments from an Azure Resource Manager template.

## Create an environment from a Resource Manager template using the Azure portal

Once an Azure Resource Manager template repository has been configured in the lab, your lab users can create an environment using Azure portal with the following steps:

1. Sign in to the [Azure portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Select **All Services**, and then select **DevTest Labs** from the list.
1. From the list of labs, select the desired lab.   
1. On the lab's pane, select **Add+**.
1. The **Choose a base** pane displays the base images you can use with the Azure Resource Manager templates listed first. Select the desired Azure Resource Manager template.

	![Choose a base](./media/devtest-lab-create-environment-from-arm/choose-a-base.png)
  
1. On the **Add** pane, enter the **Environment name** value. The environment name is what is displayed to your users in the lab. The remaining input fields are defined in the Azure Resource Manager template. If default values are defined in the template or the `azuredeploy.parameter.json` file is present, default values are displayed in those input fields. For parameters of type *secure string*, you can use the secrets stored in your Azure key vault. To learn about saving secrets in a key vault and using them when creating lab resources, see [Store secrets in Azure Key Vault](devtest-lab-store-secrets-in-key-vault.md).  

	![Add pane](./media/devtest-lab-create-environment-from-arm/add.png)

	> [!NOTE]
	> There are several parameter values that - even if specified - are displayed as empty values. Therefore, if users assign those values to parameters in an Azure Resource Manager template, DevTest Labs does not display the values. Instead, blank input fields are shown where lab users must enter a value when creating the environment.
	> 
	> - GEN-UNIQUE
	> - GEN-UNIQUE-[N]
	> - GEN-SSH-PUB-KEY
	> - GEN-PASSWORD 
 
1. Select **Add** to create the environment. The environment starts provisioning immediately with the status displaying in the **My virtual machines** list. A new resource group is automatically created by the lab to provision all the resources defined in the Azure Resource Manager template.
1. Once the environment is created, select the environment in the **My virtual machines** list to open the resource group pane and browse all of the resources provisioned in the environment.
	
	![My virtual machines list](./media/devtest-lab-create-environment-from-arm/all-environment-resources.png)
   
   You can also expand the environment to view just the list of VMs that are provisioned in the environment.
	
	![My virtual machines list](./media/devtest-lab-create-environment-from-arm/my-vm-list.png)

1. Click any of the environments to view the available actions - such as applying artifacts, attaching data disks, changing auto-shutdown time, and more.

	![Environment actions](./media/devtest-lab-create-environment-from-arm/environment-actions.png)

## Automate deployment of environments
Azure DevTest Labs provides the ability to use an [Azure Resource Management Manager template](../azure-resource-manager/resource-group-authoring-templates.md) to create an environment with a set of resources in the lab. These environments can contain any Azure resources that can be created using Resource Manager templates. DevTest Labs environments allow users to readily deploy complex infrastructures in a consistent way within the confines of the lab. Currently, adding an environment to a lab using the Azure portal is feasible when creating it once, but in a development or a testing situation, where multiple creations occur, an automated deployment allows for an improved experience.

Complete the following steps in the [Configure your own template repositories](#configure-your-own-template-repositories) section before proceeding further: 

1. Create the Resource Manager template that defines the resources being created. 
2. Set up the Resource Manager template in Git a repository. 
3. Connect the Git repository to the lab. 

### PowerShell script to deploy the Resource Manager template
Save the PowerShell script in the next section to your hard disk (for example: deployenv.ps1) and run the script after you specify values for SubscriptionId, ResourceGroupName, LabName, RepositoryName, TemplateName (folder) in the Git repo, EnvironmentName.

```powershell
./deployenv.ps1 -SubscriptionId "000000000-0000-0000-0000-0000000000000" -LabName "mydevtestlab" -ResourceGroupName "mydevtestlabRG994248" -RepositoryName "SP Repository" -TemplateName "My Environment template name" -EnvironmentName "SPResourceGroupEnv"  
```

#### Sample script
Here is the sample script to create an environment in your lab. The comments in the script help you understand the script better. 

```powershell
#Requires -Module Az.Resources

[CmdletBinding()]

param (
# ID of the Azure Subscription where the lab is created.
[string] [Parameter(Mandatory=$true)] $SubscriptionId,

# Name of the lab (existing) in which to create the environment.
[string] [Parameter(Mandatory=$true)] $LabName,

# Name of the connected repository in the lab. 
[string] [Parameter(Mandatory=$true)] $RepositoryName,

# Name of the template (folder name in the Git repository) based on which the environment will be created.
[string] [Parameter(Mandatory=$true)] $TemplateName,

# Name of the environment to be created in the lab.
[string] [Parameter(Mandatory=$true)] $EnvironmentName,

# The parameters to be passed to the template. Each parameter is prefixed with “-param_”. 
# For example, if the template has a parameter named “TestVMName” with a value of “MyVMName”, the string in $Params will have the form: `-param_TestVMName MyVMName`. 
# This convention allows the script to dynamically handle different templates.
[Parameter(ValueFromRemainingArguments=$true)]
    $Params
)

# Save this script as the deployenv.ps1 file
# Run the script after you specify values for SubscriptionId, ResourceGroupName, LabName, RepositoryName, TemplateName (folder) in the Git repo, EnvironmentName
# ./deployenv.ps1 -SubscriptionId "000000000-0000-0000-0000-0000000000000" -LabName "mydevtestlab" -ResourceGroupName "mydevtestlabRG994248" -RepositoryName "SP Repository" -TemplateName "My Environment template name" -EnvironmentName "SPResourceGroupEnv"    

# Comment this statement to completely automate the environment creation.    
# Sign in to Azure. 
Connect-AzAccount

# Select the subscription that has the lab.  
Set-AzContext -SubscriptionId $SubscriptionId | Out-Null

# Get information about the user, specifically the user ID, which is used later in the script.  
$UserId = $((Get-AzADUser -UserPrincipalName (Get-AzContext).Account).Id.Guid)
        
# Get information about the lab such as lab location. 
$lab = Get-AzResource -ResourceType "Microsoft.DevTestLab/labs" -Name $LabName -ResourceGroupName $ResourceGroupName 
if ($lab -eq $null) { throw "Unable to find lab $LabName in subscription $SubscriptionId." } 
    
# Get information about the repository in the lab. 
$repository = Get-AzResource -ResourceGroupName $lab.ResourceGroupName `
    -ResourceType 'Microsoft.DevTestLab/labs/artifactsources' `
    -ResourceName $LabName `
    -ApiVersion 2016-05-15 `
    | Where-Object { $RepositoryName -in ($_.Name, $_.Properties.displayName) } `
    | Select-Object -First 1
if ($repository -eq $null) { throw "Unable to find repository $RepositoryName in lab $LabName." } 

# Get information about the Resource Manager template based on which the environment will be created. 
$template = Get-AzResource -ResourceGroupName $lab.ResourceGroupName `
    -ResourceType "Microsoft.DevTestLab/labs/artifactSources/armTemplates" `
    -ResourceName "$LabName/$($repository.Name)" `
    -ApiVersion 2016-05-15 `
    | Where-Object { $TemplateName -in ($_.Name, $_.Properties.displayName) } `
    | Select-Object -First 1
if ($template -eq $null) { throw "Unable to find template $TemplateName in lab $LabName." } 

# Build the template parameters with parameter name and values.     
$parameters = Get-Member -InputObject $template.Properties.contents.parameters -MemberType NoteProperty | Select-Object -ExpandProperty Name
$templateParameters = @()

# The custom parameters need to be extracted from $Params and formatted as name/value pairs.
$Params | ForEach-Object {
    if ($_ -match '^-param_(.*)' -and $Matches[1] -in $parameters) {
        $name = $Matches[1]                
    } elseif ( $name ) {
        $templateParameters += @{ "name" = "$name"; "value" = "$_" }
        $name = $null #reset name variable
    }
}

# Once name/value pairs are isolated, create an object to hold the necessary template properties
$templateProperties = @{ "deploymentProperties" = @{ "armTemplateId" = "$($template.ResourceId)"; "parameters" = $templateParameters }; } 

# Now, create or deploy the environment in the lab by using the New-AzResource command. 
New-AzResource -Location $Lab.Location `
    -ResourceGroupName $lab.ResourceGroupName `
    -Properties $templateProperties `
    -ResourceType 'Microsoft.DevTestLab/labs/users/environments' `
    -ResourceName "$LabName/$UserId/$EnvironmentName" `
    -ApiVersion '2016-05-15' -Force 
 
Write-Output "Environment $EnvironmentName completed."
```

You can also use Azure CLI to deploy resources with Resource Manager templates. For more information, see [Deploy resources with Resource Manager templates and Azure CLI](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-cli).

> [!NOTE]
> Only a user with lab owner permissions can create VMs from a Resource Manager template by using Azure PowerShell. If you want to automate VM creation using a Resource Manager template and you only have user permissions, you can use the [**az lab vm create** command in the CLI](https://docs.microsoft.com/cli/azure/lab/vm#az-lab-vm-create).

### General limitations 

Consider these limitations when using a Resource Manager template in DevTest Labs:

- Any Resource Manager template you create cannot refer to existing resources; it can only refer to new resources. In addition, if you have an existing Resource Manager template that you typically deploy outside of DevTest Labs and it contains references to existing resources, it can't be used in the lab.

   The only exception to this is that you **can** reference an existing virtual network. 

- Formulas can't be created from lab VMs that were created from a Resource Manager template. 

- Custom images can't be created from lab VMs that were created from a Resource Manager template. 

- Most policies are not evaluated when you deploy Resource Manager templates.

   For example, you might have a lab policy specifying that a user can only create five VMs. However, a user can deploy a Resource Manager template that creates dozens of VMs. Policies that are not evaluated include:

   - Number of VMs per user
   - Number of premium VMs per lab user
   - Number of premium disks per lab user


### Configure environment resource group access rights for lab users

Lab users can deploy a Resource Manager template. But by default, they have Reader access rights, which means they can’t change the resources in an environment resource group. For example, they cannot stop or start their resources.

Follow these steps to give your lab users Contributor access rights. Then, when they deploy a Resource Manager template, they will be able to edit the resources in that environment. 


1. On your lab's **Overview** pane, select **Configuration and policies**.
1. Select **Lab settings**.
1. In the Lab Settings pane, select **Contributor** to grant write permissions to lab users.

	![Configure lab user access rights](./media/devtest-lab-create-environment-from-arm/configure-access-rights.png)

1. Select **Save**.

## Next steps
* Once a VM has been created, you can connect to the VM by selecting **Connect** on the VM's management pane.
* View and manage resources in an environment by selecting the environment in the **My virtual machines** list in your lab. 
* Explore the [Azure Resource Manager templates from Azure Quickstart template gallery](https://github.com/Azure/azure-quickstart-templates).
