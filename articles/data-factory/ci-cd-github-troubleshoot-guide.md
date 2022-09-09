---
title: Troubleshoot CI-CD, Azure DevOps, and GitHub issues in ADF
description: Use different methods to troubleshoot CI-CD issues in ADF. 
author: ssabat
ms.author: susabat
ms.reviewer: susabat
ms.service: data-factory
ms.workload: data-services
ms.topic: troubleshooting
ms.date: 12/03/2020
---

# Troubleshoot CI-CD, Azure DevOps, and GitHub issues in ADF 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

This article explores common troubleshooting methods for Continuous Integration-Continuous Deployment (CI-CD), Azure DevOps and GitHub issues in Azure Data Factory.

If you have questions or issues in using source control or DevOps techniques, here are a few articles you may find useful:

- Refer to [Source Control in ADF](source-control.md) to learn how source control is practiced in ADF. 
- Refer to  [CI-CD in ADF](continuous-integration-deployment.md) to learn more about how DevOps CI-CD is practiced in ADF.
 
## Common errors and messages

### Connect to Git repository failed due to different tenant

#### Issue
    
Sometimes you encounter Authentication issues like HTTP status 401. Especially when you have multiple tenants with guest account, things could become more complicated.

#### Cause

What we have observed is that the token was obtained from the original tenant, but ADF is in guest tenant and trying to use the token to visit DevOps in guest tenant. This is not the expected behavior.

#### Recommendation

You should use the token issued from guest tenant instead. For example, you have to assign the same Azure Active Directory to be your guest tenant as well as your DevOps, so it can correctly set token behavior and use the correct tenant.

### Template parameters in the parameters file are not valid

#### Issue

If we delete a trigger in Dev branch, which is already available in Test or Production branch with **same** configuration (like frequency and interval), then release pipeline deployment succeeds and corresponding trigger will be deleted in respective environments. But if you have **different** configuration (like frequency and interval) for trigger in Test/Production environments and if you delete the same trigger in Dev, then deployment fails with an error.

#### Cause

CI/CD Pipeline fails with the following error:

`
2020-07-20T11:19:02.1276769Z ##[error]Deployment template validation failed: 'The template parameters 'Trigger_Salesforce_properties_typeProperties_recurrence_frequency, Trigger_Salesforce_properties_typeProperties_recurrence_interval, Trigger_Salesforce_properties_typeProperties_recurrence_startTime, Trigger_Salesforce_properties_typeProperties_recurrence_timeZone' in the parameters file are not valid; they are not present in the original template and can therefore not be provided at deployment time. The only supported parameters for this template are 'factoryName, PlanonDWH_connectionString, PlanonKeyVault_properties_typeProperties_baseUrl
`

#### Recommendation

The error occurs because we often delete a trigger, which is parameterized, therefore, the parameters will not be available in the ARM template (because the trigger does not exist anymore). Since the parameter is not in the ARM template anymore, we have to update the overridden parameters in the DevOps pipeline. Otherwise, each time the parameters in the ARM template change, they must update the overridden parameters in the DevOps pipeline (in the deployment task).

### Updating property type is not supported

#### Issue

CI/CD release pipeline failing with the following error:

`
2020-07-06T09:50:50.8716614Z There were errors in your deployment. Error code: DeploymentFailed.
2020-07-06T09:50:50.8760242Z ##[error]At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/DeployOperations for usage details.
2020-07-06T09:50:50.8771655Z ##[error]Details:
2020-07-06T09:50:50.8772837Z ##[error]DataFactoryPropertyUpdateNotSupported: Updating property type is not supported.
2020-07-06T09:50:50.8774148Z ##[error]DataFactoryPropertyUpdateNotSupported: Updating property type is not supported.
2020-07-06T09:50:50.8775530Z ##[error]Check out the troubleshooting guide to see if your issue is addressed: https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-resource-group-deployment?view=azure-devops#troubleshooting
2020-07-06T09:50:50.8776801Z ##[error]Task failed while creating or updating the template deployment.
`

#### Cause

This is due to an Integration Runtime with the same name in the target factory but with a different type. Integration Runtime needs to be of the same type when deploying.

#### Recommendation

- Refer to this Best Practices for CI/CD below:

    https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#best-practices-for-cicd 
- Integration runtimes don't change often and are similar across all stages in your CI/CD, so Data Factory expects you to have the same name and type of integration runtime across all stages of CI/CD. If the name and types & properties are different, make sure to match the source and target IR configuration and then deploy the release pipeline.
- If you want to share integration runtimes across all stages, consider using a ternary factory just to contain the shared integration runtimes. You can use this shared factory in all of your environments as a linked integration runtime type.

### Document creation or update failed because of invalid reference

#### Issue

When trying to publish changes to a Data Factory,you get following error message:

`
"error": {
        "code": "BadRequest",
        "message": "The document creation or update failed because of invalid reference '<entity>'.",
        "target": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/<rgname>/providers/Microsoft.DataFactory/factories/<datafactory>/pipelines/<pipeline>",
        "details": null
    }
`

#### Symptom

You have detached the Git configuration and set it up again with the "Import resources" flag selected, which sets the Data Factory as "in sync". This means no changes to publish.

#### Resolution

Detach Git configuration and set it up again, and make sure NOT to check the "import existing resources" checkbox.

### Data Factory move failing from one resource group to another

#### Issue

You are unable to move Data Factory from one Resource Group to another, failing with the following error:

`
{
	"code": "ResourceMoveProviderValidationFailed",
	"message": "Resource move validation failed. Please see details. Diagnostic information: timestamp 'xxxxxxxxxxxxZ', subscription id 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx', tracking id 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx', request correlation id 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'.",
	"details": [
		{
			"code": "BadRequest",
			"target": "Microsoft.DataFactory/factories",
			"message": "One of the resources contain integration runtimes that are either SSIS-IRs in starting/started/stopping state, or Self-Hosted IRs which are shared with other resources. Resource move is not supported for those resources."
		}
	]
}
`

#### Resolution

You need to delete the SSIS-IR and Shared IRs to allow the move operation. If you do not want to delete the IRs, then the best way is to follow the copy and clone document to do the copy and after it's done, delete the old Data factory.

###  Unable to export and import ARM template

#### Issue

Unable to export and import ARM template. No error was on the portal, however, in the browser trace, you  could see the following error:

`Failed to load resource: the server responded with a status code of 401 (Unauthorized)`

#### Cause

You have created a customer role as the user and it did not have the necessary permission. When the factory is loaded in the UI, a series of exposure control values for the factory is checked. In this case, the user's access role does not have permission to access *queryFeaturesValue* API. To access this API, the global parameters feature is turned off. The ARM export code path is partly relying on the global parameters feature.

#### Resolution

In order to resolve the issue, you need to add the following permission to your role: *Microsoft.DataFactory/factories/queryFeaturesValue/action*. This permission should be included by default in the "Data Factory Contributor" role.

## Next steps

For more help with troubleshooting, try the following resources:

*  [Data Factory blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory feature requests](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure videos](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Stack overflow forum for Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter information about Data Factory](https://twitter.com/hashtag/DataFactory)
