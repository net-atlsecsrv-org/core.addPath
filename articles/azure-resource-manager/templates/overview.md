---
title: Templates overview
description: Describes the benefits using Azure Resource Manager templates for deployment of resources.
ms.topic: conceptual
ms.date: 01/02/2020
---
# Azure Resource Manager templates overview

With the move to the cloud, many teams have adopted agile development methods. These teams iterate quickly. They need to repeatedly deploy their solutions to the cloud, and know their infrastructure is in a reliable state. As infrastructure has become part of the iterative process, the division between operations and development has disappeared. Teams need to manage infrastructure and application code through a unified process.

To meet these challenges, you can automate deployments and use the practice of infrastructure as code. In code, you define the infrastructure that needs to be deployed. The infrastructure code becomes part of your project. Just like application code, you store the infrastructure code in a source repository and version it. Any one on your team can run the code and deploy similar environments.

To implement infrastructure as code for your Azure solutions, use Azure Resource Manager templates. The template is a JavaScript Object Notation (JSON) file that defines the infrastructure and configuration for your project. The template uses declarative syntax, which lets you state what you intend to deploy without having to write the sequence of programming commands to create it. In the template, you specify the resources to deploy and the properties for those resources.

## Why choose Resource Manager templates?

If you're trying to decide between using Resource Manager templates and one of the other infrastructure as code services, consider the following advantages of using templates:

* **Declarative syntax**: Resource Manager templates allow you to create and deploy an entire Azure infrastructure declaratively. For example, you can deploy not only virtual machines, but also the network infrastructure, storage systems and any other resources you may need.

* **Repeatable results**: Repeatedly deploy your infrastructure throughout the development lifecycle and have confidence your resources are deployed in a consistent manner. Templates are idempotent, which means you can deploy the same template many times and get the same resource types in the same state. You can develop one template that represents the desired state, rather than developing lots of separate templates to represent updates.

* **Orchestration**: You don't have to worry about the complexities of ordering operations. Resource Manager orchestrates the deployment of interdependent resources so they're created in the correct order. When possible, Resource Manager deploys resources in parallel so your deployments finish faster than serial deployments. You deploy the template through one command, rather than through multiple imperative commands.

   ![Template deployment comparison](./media/overview/template-processing.png)

* **Built-in validation**: Your template is deployed only after passing validation. Resource Manager checks the template before starting the deployment to make sure the deployment will succeed. Your deployment is less likely to stop in a half-finished state.

* **Modular files**: You can break your templates into smaller, reusable components and link them together at deployment time. You can also nest one template inside another templates.

* **Create any Azure resource**: You can immediately use new Azure services and features in templates. As soon as a resource provider introduces new resources, you can deploy those resources through templates. You don't have to wait for tools or modules to be updated before using the new services.

* **Tracked deployments**: In the Azure portal, you can review the deployment history and get information about the template deployment. You can see the template that was deployed, the parameter values passed in, and any output values. Other infrastructure as code services aren't tracked through the portal.

   ![Deployment history](./media/overview/deployment-history.png)

* **Policy as code**: [Azure Policy](../../governance/policy/overview.md) is a policy as code framework to automate governance. If you're using Azure policies, policy remediation is done on non-compliant resources when deployed through templates.

* **Deployment Blueprints**: You can take advantage of [Blueprints](../../governance/blueprints/overview.md) provided by Microsoft to meet regulatory and compliance standards. These blueprints include pre-built templates for various architectures.

* **CI/CD integration**: You can integrate templates into your continuous integration and continuous deployment (CI/CD) tools, which can automate your release pipelines for fast and reliable application and infrastructure updates. By using Azure DevOps and Resource Manager template task, you can use Azure Pipelines to continuously build and deploy Azure Resource Manager template projects. To learn more, see [VS project with pipelines](add-template-to-azure-pipelines.md) and [Continuous integration with Azure Pipelines](template-tutorial-use-azure-pipelines.md).

* **Exportable code**: You can get a template for an existing resource group by either exporting the current state of the resource group, or viewing the template used for a particular deployment. Viewing the [exported template](export-template-portal.md) is a helpful way to learn about the template syntax.

* **Authoring tools**: You can author templates with [Visual Studio Code](use-vs-code-to-create-template.md) and the template tool extension. You get intellisense, syntax highlighting, in-line help, and many other language functions. In addition to Visual Studio code, you can also use [Visual Studio](create-visual-studio-deployment-project.md).

## Template file

Within your template, you can write [template expressions](template-expressions.md) that extend the capabilities of JSON. These expressions make use of the [functions](template-functions.md) provided by Resource Manager.

The template has the following sections:

* [Parameters](template-parameters.md) - Provide values during deployment that allow the same template to be used with different environments.

* [Variables](template-variables.md) - Define values that are reused in your templates. They can be constructed from parameter values.

* [User-defined functions](template-user-defined-functions.md) - Create customized functions that simplify your template.

* [Resources](template-syntax.md#resources) - Specify the resources to deploy.

* [Outputs](template-outputs.md) - Return values from the deployed resources.

## Template deployment process

When you deploy a template, Resource Manager converts the template into REST API operations. For example, when Resource Manager receives a template with the following resource definition:

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2016-01-01",
    "name": "mystorageaccount",
    "location": "westus",
    "sku": {
      "name": "Standard_LRS"
    },
    "kind": "Storage",
    "properties": {}
  }
]
```

It converts the definition to the following REST API operation, which is sent to the Microsoft.Storage resource provider:

```HTTP
PUT
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/mystorageaccount?api-version=2016-01-01
REQUEST BODY
{
  "location": "westus",
  "sku": {
    "name": "Standard_LRS"
  },
  "kind": "Storage",
  "properties": {}
}
```

## Template design

How you define templates and resource groups is entirely up to you and how you want to manage your solution. For example, you can deploy your three tier application through a single template to a single resource group.

![three tier template](./media/overview/3-tier-template.png)

But, you don't have to define your entire infrastructure in a single template. Often, it makes sense to divide your deployment requirements into a set of targeted, purpose-specific templates. You can easily reuse these templates for different solutions. To deploy a particular solution, you create a master template that links all the required templates. The following image shows how to deploy a three tier solution through a parent template that includes three nested templates.

![nested tier template](./media/overview/nested-tiers-template.png)

If you envision your tiers having separate lifecycles, you can deploy your three tiers to separate resource groups. Notice the resources can still be linked to resources in other resource groups.

![tier template](./media/overview/tier-templates.png)

For information about nested templates, see [Using linked templates with Azure Resource Manager](linked-templates.md).

## Next steps

* For a step-by-step tutorial that guides you through the process of creating a template, see [Tutorial: Create and deploy your first Azure Resource Manager template](template-tutorial-create-first-template.md).
* For information about the properties in template files, see [Understand the structure and syntax of Azure Resource Manager templates](template-syntax.md).
* To learn about exporting templates, see [Quickstart: Create and deploy Azure Resource Manager templates by using the Azure portal](quickstart-create-templates-use-the-portal.md).
