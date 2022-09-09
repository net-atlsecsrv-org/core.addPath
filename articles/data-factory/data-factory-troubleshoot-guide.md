---
title: Troubleshoot Azure Data Factory | Microsoft Docs
description: Learn how to troubleshoot external control activities in Azure Data Factory.
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 8/26/2019
ms.author: abnarain
ms.reviewer: craigg
---

# Troubleshoot Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

This article explores common troubleshooting methods for external control activities in Azure Data Factory.

## Connector and copy activity

For connector issues e.g. encounter error using copy activity, refer to [Troubleshoot Azure Data Factory Connectors](connector-troubleshoot-guide.md).
  

## Azure Databricks

### Error code:  3200

- **Message**: Error 403.

- **Cause**: `The Databricks access token has expired.`

- **Recommendation**: By default, the Azure Databricks access token is valid for 90 days. Create a new token and update the linked service.


### Error code:  3201

- **Message**: `Missing required field: settings.task.notebook_task.notebook_path.`

- **Cause**: `Bad authoring: Notebook path not specified correctly.`

- **Recommendation**: Specify the notebook path in the Databricks activity.

<br/>  

- **Message**: `Cluster... does not exist.`

- **Cause**: `Authoring error: Databricks cluster does not exist or has been deleted.`

- **Recommendation**: Verify that the Databricks cluster exists.

<br/>  

- **Message**: `Invalid Python file URI... Please visit Databricks user guide for supported URI schemes.`

- **Cause**: `Bad authoring.`

- **Recommendation**: Specify either absolute paths for workspace-addressing schemes, or `dbfs:/folder/subfolder/foo.py` for files stored in Databricks File System.

<br/>  

- **Message**: `{0} LinkedService should have domain and accessToken as required properties.`

- **Cause**: `Bad authoring.`

- **Recommendation**: Verify the [linked service definition](compute-linked-services.md#azure-databricks-linked-service).

<br/>  

- **Message**: `{0} LinkedService should specify either existing cluster ID or new cluster information for creation.`

- **Cause**:  `Bad authoring.`

- **Recommendation**: Verify the [linked service definition](compute-linked-services.md#azure-databricks-linked-service).

<br/>  

- **Message**: `Node type Standard_D16S_v3 is not supported. Supported node types:   Standard_DS3_v2, Standard_DS4_v2, Standard_DS5_v2, Standard_D8s_v3,   Standard_D16s_v3, Standard_D32s_v3, Standard_D64s_v3, Standard_D3_v2,   Standard_D8_v3, Standard_D16_v3, Standard_D32_v3, Standard_D64_v3,   Standard_D12_v2, Standard_D13_v2, Standard_D14_v2, Standard_D15_v2,   Standard_DS12_v2, Standard_DS13_v2, Standard_DS14_v2, Standard_DS15_v2,   Standard_E8s_v3, Standard_E16s_v3, Standard_E32s_v3, Standard_E64s_v3,   Standard_L4s, Standard_L8s, Standard_L16s, Standard_L32s, Standard_F4s,   Standard_F8s, Standard_F16s, Standard_H16, Standard_F4s_v2, Standard_F8s_v2,   Standard_F16s_v2, Standard_F32s_v2, Standard_F64s_v2, Standard_F72s_v2,   Standard_NC12, Standard_NC24, Standard_NC6s_v3, Standard_NC12s_v3,   Standard_NC24s_v3, Standard_L8s_v2, Standard_L16s_v2, Standard_L32s_v2,   Standard_L64s_v2, Standard_L80s_v2.`

- **Cause**: `Bad authoring.`

- **Recommendation**: Refer to the error message.

<br/>

### Error code:  3202

- **Message**: `There were already 1000 jobs created in past 3600 seconds, exceeding rate limit:   1000 job creations per 3600 seconds.`

- **Cause**: `Too many Databricks runs in an hour.`

- **Recommendation**: Check all pipelines that use this Databricks workspace for their job   creation rate.  If pipelines launched   too many Databricks runs in aggregate, migrate some pipelines to a new   workspace.

<br/>  

- **Message**: `Could not parse request object: Expected 'key' and 'value' to be set for JSON map field base_parameters, got 'key: "..."' instead.`

- **Cause**: `Authoring error: No value provided for the parameter.`

- **Recommendation**: Inspect the pipeline JSON and ensure all parameters in the baseParameters notebook specify a nonempty value.

<br/>  

- **Message**: `User: `SimpleUserContext{userId=..., name=user@company.com, orgId=...}` is not   authorized to access cluster.`

- **Cause**: The user who generated the access token isn't allowed to access the   Databricks cluster specified in the linked service.

- **Recommendation**: Ensure the user has the required permissions in the workspace.


### Error code:  3203

- **Message**: `The cluster is in Terminated state, not available to receive jobs. Please fix the cluster or retry later.`

- **Cause**: The cluster was terminated. For interactive clusters, this might be a race condition.

- **Recommendation**: The best way to avoid this error is to use job clusters.


### Error code:  3204

- **Message**: `Job execution failed.`

- **Cause**:  Error messages indicate various issues, such as an unexpected cluster state or a specific activity. Most often no error   message appears at all.

- **Recommendation**: N/A
            

## Azure Data Lake Analytics

The following table applies to U-SQL.
      
### Error code:  2709

- **Message**: `The access token is from the wrong tenant.`

- **Cause**:  Incorrect Azure Active Directory (Azure AD) tenant.

- **Recommendation**: Incorrect Azure Active Directory (Azure AD) tenant.

<br/>

- **Message**: `We cannot accept your job at this moment. The maximum number of queued jobs for   your account is 200. `

- **Cause**:  This error is caused by throttling on Data Lake Analytics.

- **Recommendation**: Reduce the number of submitted jobs to Data Lake Analytics by changing Data Factory triggers and concurrency settings on activities. Or increase the limits on Data Lake Analytics.

<br/>  

- **Message**: `This job was rejected because it requires 24 AUs. This account's administrator-defined policy prevents a job from using more than 5 AUs.`

- **Cause**:  This error is caused by throttling on Data Lake Analytics.

- **Recommendation**: Reduce the number of submitted jobs to Data Lake Analytics by changing Data Factory triggers and concurrency settings on activities. Or increase the limits on Data Lake Analytics.


### Error code:  2705

- **Message**: `Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/>  <br/>  User is   not able to access Data Lake Store.  <br/>  <br/>  User is  not authorized to use Data Lake Analytics.`

- **Cause**:  The service principal or certificate doesn't have access to the file in storage.

- **Recommendation**: Make sure the service principal or certificate the user provides for Data Lake Analytics jobs has access to the Data Lake Analytics account and the default Data Lake Storage instance from the root folder.


### Error code:  2711

- **Message**: `Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/>  <br/>  User is   not able to access Data Lake Store.  <br/>  <br/>  User is  not authorized to use Data Lake Analytics.`

- **Cause**:  The service principal or certificate doesn't have access to the file in storage.

- **Recommendation**: Make sure the service principal or certificate the user provides for Data Lake Analytics jobs has access to the Data Lake Analytics account and the default Data Lake Storage instance from the root folder.

<br/>  

- **Message**: `Cannot find the 'Azure Data Lake Store' file or folder.`

- **Cause**:  The   path to the U-SQL file is wrong, or the linked service credentials don't have access.

- **Recommendation**: Verify the path and credentials provided in the linked service.


### Error code:  2704

- **Message**: `Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/>  <br/>  User is   not able to access Data Lake Store.  <br/>  <br/>  User is  not authorized to use Data Lake Analytics.`

- **Cause**:  The service principal or certificate doesn't have access to the file in storage.

- **Recommendation**: Make sure the service principal or certificate the user provides for Data Lake Analytics jobs has access to the Data Lake Analytics account and the default Data Lake Storage instance from the root folder.


### Error code:  2707

- **Message**: `Cannot resolve the account of AzureDataLakeAnalytics. Please check 'AccountName' and   'DataLakeAnalyticsUri'.`

- **Cause**:  The Data Lake Analytics account in the linked service is wrong.

- **Recommendation**: Verify that the right account is provided.


### Error code:  2703

- **Message**: `Error Id: E_CQO_SYSTEM_INTERNAL_ERROR (or any error that starts with "Error   Id:").`

- **Cause**:  The error is from Data Lake Analytics.

- **Recommendation**: An error like the example means the job was submitted to Data Lake Analytics, and the   script there failed. Investigate in Data Lake Analytics. In the portal, go to the Data Lake Analytics account, and look for the job by using the Data Factory activity run ID (not the pipeline run ID). The job there provides more   information about the error and will help you troubleshoot. If the resolution isn't clear, contact the Data Lake Analytics support team and provide the job URL, which   includes your account name and the job ID.
          

## Azure functions

### Error code:  3602

- **Message**: `Invalid HttpMethod: '%method;'.`

- **Cause**: Http method specified in the activity payload is not supported by Azure Function Activity.

- **Recommendation**:  The Http methods that are supported are PUT, POST, GET, DELETE, OPTIONS, HEAD, and TRACE.


### Error code:  3603

- **Message**: `Response Content is not a valid JObject.`

- **Cause**: Azure function that was called did not return a JSON Payload in the response. ADF Azure function activity only supports JSON response content.

- **Recommendation**:  Update Azure function to return a valid JSON Payload e.g. a C# function may return (ActionResult)new OkObjectResult("{\"Id\":\"123\"}");


### Error code:  3606

- **Message**: Azure function activity missing function key.

- **Cause**: Azure function activity definition is not complete.

- **Recommendation**:  Please check the input AzureFunction activity JSON definition has property named 'functionKey'.


### Error code:  3607

- **Message**: `Azure function activity missing function name.`

- **Cause**: Azure function activity definition is not complete.

- **Recommendation**:  Please check the input AzureFunction activity JSON definition has property named 'functionName'.


### Error code:  3608

- **Message**: `Call to provided Azure function '%FunctionName;' failed with status-'%statusCode;' and message - '%message;'.`

- **Cause**: Azure function details in activity definition may be incorrect.

- **Recommendation**:  Fix the azure function details and retry again.


### Error code:  3609

- **Message**: `Azure function activity missing functionAppUrl.`

- **Cause**: Azure function activity definition is not complete.

- **Recommendation**:  Please check the input AzureFunction activity JSON definition has property named 'functionAppUrl'.


### Error code:  3610

- **Message**: `There was an error while calling endpoint.`

- **Cause**: Function URL may be incorrect.

- **Recommendation**:  Please make sure the value for 'functionAppUrl' in the activity JSON is correct and try again.


### Error code:  3611

- **Message**: `Azure function activity missing Method in JSON.`

- **Cause**: Azure function activity definition is not complete.

- **Recommendation**:  Please check the input AzureFunction activity JSON definition has property named 'method'.


### Error code:  3612

- **Message**: `Azure function activity missing LinkedService definition in JSON.`

- **Cause**: Azure function activity definition is not complete.

- **Recommendation**:  Please check the input AzureFunction activity JSON definition has linked service details.



## Azure Machine Learning

### Error code:  4101

- **Message**: `AzureMLExecutePipeline activity '%activityName;' has invalid value for property '%propertyName;'.`

- **Cause**: Bad format or missing definition of property '%propertyName;'.

- **Recommendation**:  Please check if the activity '%activityName;' has property '%propertyName;' defined with correct data.


### Error code:  4110

- **Message**: `AzureMLExecutePipeline activity missing LinkedService definition in JSON.`

- **Cause**: AzureMLExecutePipeline activity definition is not complete.

- **Recommendation**:  Please check if the input AzureMLExecutePipeline activity JSON definition has linked service details.


### Error code:  4111

- **Message**: `AzureMLExecutePipeline activity has wrong LinkedService type in JSON. Expected LinkedService type: '%expectedLinkedServiceType;', current LinkedService type: Expected LinkedService type: '%currentLinkedServiceType;'.`

- **Cause**: Incorrect activity definition.

- **Recommendation**:  Please check if the input AzureMLExecutePipeline activity JSON definition has correct linked service details.


### Error code:  4112

- **Message**: `AzureMLService linked service has invalid value for property '%propertyName;'.`

- **Cause**: Bad format or missing definition of property '%propertyName;'.

- **Recommendation**:  Please check if the linked service has property '%propertyName;' defined with correct data.


### Error code:  4121

- **Message**: `Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Cause**: Credential used to access Azure Machine Learning has expired.

- **Recommendation**:  Please verify credential is valid and retry


### Error code:  4122

- **Message**: `Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Cause**: Credential provided in Azure Machine Learning Linked Service is invalid or does not have permission for the operation.

- **Recommendation**:  Please verify credential in Linked Service is valid and has permission to access Azure Machine Learning.


### Error code:  4123

- **Message**: `Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Cause**: Properties of the activity such as pipelineParameters are invalid for the Azure ML pipeline.

- **Recommendation**:  Please check the value of activity properties to match expected payload of the published Azure ML pipeline specified in Linked Service.


### Error code:  4124

- **Message**: `Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Cause**: The published Azure ML pipeline endpoint does not exist.

- **Recommendation**:  Please verify the published Azure Machine Learning pipeline endpoint specified in Linked Service exists in Azure Machine Learning.


### Error code:  4125

- **Message**: `Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Cause**: Server error on Azure Machine Learning.

- **Recommendation**:  Please retry later. Contact Azure Machine Learning team for help if issue remains.


### Error code:  4126

- **Message**: `Azure ML pipeline run failed with status: '%amlPipelineRunStatus;'. Azure ML pipeline run Id: '%amlPipelineRunId;'. Please check in Azure Machine Learning for more error logs.`

- **Cause**: Azure ML pipeline run failed.

- **Recommendation**:  Please check in Azure Machine Learning for more error logs and fix the ML pipeline.



## Common

### Error code:  2103

- **Message**: `Please provide value for the required property '%propertyName;'.`

- **Cause**: The value for the property has not been provided, however it is required in the scenario.

- **Recommendation**:  Provide the value from the message and try again.


### Error code:  2104

- **Message**: `The type of the property '%propertyName;' is incorrect.`

- **Cause**: The type of the provided property is not as expected.

- **Recommendation**:  Please fix the type of the property and try again.


### Error code:  2105

- **Message**: `An invalid json is provided for property '%propertyName;'. Encountered an error while trying to parse: '%message;'.`

- **Cause**: The value for the property is invalid or doesn't have the expected format.

- **Recommendation**:  Please lookup the documentation for the property and make sure the value provided has expected format and type.


### Error code:  2106

- **Message**: `The storage connection string is invalid. %errorMessage;`

- **Cause**: The connection string for the storage is invalid or has incorrect format.

- **Recommendation**:  Please go to the Azure portal, find your storage, copy the connection string and paste in your linked service and try again.


### Error code:  2108

- **Message**: `Error calling the endpoint '%url;'. Response status code: '%code;'`

- **Cause**: The request failed due to an underlying issue such as network connectivity, DNS failure, server certificate validation or timeout.

- **Recommendation**:  Use Fiddler/Postman to validate the request.


### Error code:  2110

- **Message**: `The linked service type '%linkedServiceType;' is not supported for '%executorType;' activities.`

- **Cause**: The linked service specified in the activity was wrong.

- **Recommendation**:  Please make sure the linked service type is one of the supported types for the activity. For example, for HDI activities the linked service type can be HDInsight or HDInsightOnDemand.


### Error code:  2111

- **Message**: `The type of the property '%propertyName;' is incorrect. The expected type is %expectedType;.`

- **Cause**: The type of the provided property is not as expected.

- **Recommendation**:  Please fix the type of the property and try again.


### Error code:  2112

- **Message**: `The cloud type is unsupported or could not be determined for storage from the EndpointSuffix '%endpointSuffix;'.`

- **Cause**: The cloud type is unsupported or could not be determined for storage from the EndpointSuffix.

- **Recommendation**:  Please use storage in another cloud and try again.


### Error code:  2128

- **Message**: `No response from the endpoint. Possible causes: network connectivity, DNS failure, server certificate validation or timeout.`

- **Cause**: Network connectivity, DNS failure, server certificate validation or timeout.

- **Recommendation**:  Validate that the endpoint you are trying to hit is responding to requests. You may use tools like Fiddler/Postman.



## Custom

The following table applies to Azure Batch.
      
### Error code:  2500

- **Message**: `Hit unexpected exception and execution failed.`

- **Cause**: `Can't launch command, or the program returned an error code.`

- **Recommendation**:  Ensure that the executable file exists. If the program started, make sure *stdout.txt* and *stderr.txt* were uploaded to the storage account. It's a good practice to emit copious logs in your code for debugging.


### Error code:  2501

- **Message**: `Cannot access user batch account; please check batch account settings.`

- **Cause**: Incorrect Batch access key or pool name.

- **Recommendation**: Verify the pool name and the Batch access key in the linked service.


### Error code:  2502

- **Message**: `Cannot access user storage account; please check storage account settings.`

- **Cause**: Incorrect storage account name or access key.

- **Recommendation**: Verify the storage account name and the access key in the linked service.


### Error code:  2504

- **Message**:  `Operation returned an invalid status code 'BadRequest'.`

- **Cause**: Too many files in the folderPath of the custom activity. The total   size of resourceFiles can't be more than 32,768 characters.

- **Recommendation**: Remove unnecessary files. Or zip them and add an unzip command to extract them. For example, use  `powershell.exe -nologo -noprofile   -command "& { Add-Type -A 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ;  $folder\yourProgram.exe`


### Error code:  2505

- **Message**: `Cannot create Shared Access Signature unless Account Key credentials are used.`

- **Cause**: Custom activities support only storage accounts that use an access key.

- **Recommendation**: Refer to the error description.


### Error code:  2507

- **Message**: `The folder path does not exist or is empty: ...`

- **Cause**: No files are in the storage account at the specified path.

- **Recommendation**: The folder path must contain the executable files you want to run.


### Error code:  2508

- **Message**:  `There are duplicate files in the resource folder.`

- **Cause**: Multiple files of the same name are in different subfolders   of folderPath.

- **Recommendation**: Custom activities flatten folder structure under folderPath.  If you need to preserve the folder structure, zip the files and extract them in Azure Batch by using an unzip command. For   example, use `powershell.exe -nologo -noprofile   -command "& { Add-Type -A 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ;   $folder\yourProgram.exe`


### Error code:  2509

- **Message**: `Batch   url ... is invalid; it must be in Uri format.`

- **Cause**: Batch URLs must be similar to `https://mybatchaccount.eastus.batch.azure.com`

- **Recommendation**: Refer to the error description.


### Error code:  2510

- **Message**: `An   error occurred while sending the request.`

- **Cause**: The batch URL is invalid.

- **Recommendation**: Verify the batch URL.
            

## HDInsight

### Error code:  200

- **Message**: `Unexpected error happened: '%error;'.`

- **Cause**: There is an internal service issue.

- **Recommendation**:  Please contact ADF support for further assistance.


### Error code:  201

- **Message**: `JobType %jobType; is not found.`

- **Cause**: There is a new job type that is not supported by ADF.

- **Recommendation**:  Please contact ADF support team for further assistance.


### Error code:  202

- **Message**: `Failed to create on demand HDI cluster. Cluster name or linked service name: '%clusterName;', error: '%message;'`

- **Cause**: The error message should show the details of what went wrong.

- **Recommendation**:  The error message should help to troubleshoot the issue. If there is no enough information, please contact ADF support for further help.


### Error code:  203

- **Message**: `Failed to delete on demand HDI cluster. Cluster name or linked service name: '%clusterName;', error: '%message;'`

- **Cause**: The error message should show the details of what went wrong.

- **Recommendation**:  The error message should help to troubleshoot the issue. If there is no enough information, please contact ADF support for further help.


### Error code:  204

- **Message**: `The resumption token is missing for runId '%runId;'.`

- **Cause**: There is an internal service issue.

- **Recommendation**:  Please contact ADF support for further assistance.


### Error code:  205

- **Message**: `Failed to prepare cluster for LinkedService '%linkedServiceName;', the current resource status is '%status;'.`

- **Cause**: There was an error when creating the HDI on demand cluster.

- **Recommendation**:  Please contact ADF support for further assistance.


### Error code:  206

- **Message**: `The batch ID for Spark job is invalid. Please retry your job, and if the problem persists, contact the ADF support for further assistance.`

- **Cause**: There was an internal problem with the service that caused this.

- **Recommendation**:  This could be a transient issue. Please retry your job, and if the problem persists, contact the ADF support for further assistance.


### Error code:  207

- **Message**: `Could not determine the region from the provided storage account. Please try using another primary storage account for the on demand HDI or contact ADF support team and provide the activity run ID.`

- **Cause**: There was an internal error while trying to determine the region from the primary storage account.

- **Recommendation**:  Try another storage. In case this is not an acceptable solution, contact ADF support team for further assistance.


### Error code:  208

- **Message**: `Service Principal or the MSI authenticator are not instantiated. Please consider providing a Service Principal in the HDI on demand linked service which has permissions to create an HDInsight cluster in the provided subscription and try again. In case if this is not an acceptable solution, contact ADF support team for further assistance.`

- **Cause**: There was an internal error while trying to read the Service Principal or instantiating the MSI authentication.

- **Recommendation**:  Please consider providing a Service Principal which has permissions to create an HDInsight cluster in the provided subscription and try again. In case if this is not an acceptable solution, contact ADF support team for further assistance.


### Error code:  2300

- **Message**: `Failed to submit the job '%jobId;' to the cluster '%cluster;'. Error: %errorMessage;.`

<br>

- **Cause**: When error message contains a message similar to 'The remote name could not be resolved.', this could mean the provided cluster URI is invalid.


- **Recommendation**: Make sure that the cluster hasn't been deleted and that the provided URI is correct. When you open the URI in a browser, you should see the Ambari UI. If the cluster is in a virtual network, the URI should be the private URI. To open it, use a VM that's part of the same virtual network. For more information, see [this](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment#directly-connect-to-apache-hadoop-services).
                  

<br>

- **Cause**: When error message contains a message similar to 'A task was canceled.', this means that the job submission timed out.

- **Recommendation**:  The problem could be either general HDInsight connectivity or network connectivity. First confirm that the HDInsight Ambari UI is available from any browser. Confirm that your credentials are still valid. If you're using self-hosted integrated runtime (IR), make sure to do this from the VM or machine where the self-hosted IR is installed. Then try submitting the job from Data Factory again. If it still fails, contact the Data Factory team for support.

<br>

- **Cause**: When error message contains a message similar to 'User admin is locked out in Ambari' or 'Unauthorized: Ambari user name or password is incorrect', this means the credentials for HDInsight are incorrect or have expired.

- **Recommendation**:  Correct the credentials and redeploy the linked service. First make sure the credentials work on HDInsight by opening the cluster URI on any browser and trying to sign in. If the credentials don't work, you can reset them from the Azure portal.

<br>

- **Cause**: When error message contains a message similar to '502 - Web server received an invalid response while acting as a gateway or proxy server', this error is returned by HDInsight service.


- **Recommendation**: Look through Azure HDInsight troubleshooting documentation, for example,  https://hdinsight.github.io/ambari/ambari-ui-502-error.html, https://hdinsight.github.io/spark/spark-thriftserver-errors.html, https://docs.microsoft.com/azure/application-gateway/application-gateway-troubleshooting-502.
                  

<br>

- **Cause**: When error message contains a message similar to 'Unable to service the submit job request as templeton service is busy with too many submit job requests' or 'Queue root.joblauncher already has 500 applications, cannot accept submission of application', this means that too many jobs are being submitted to HDInsight at the same time.

- **Recommendation**:  Consider limiting the number of concurrent jobs submitted to HDInsight. Refer to Data Factory activity concurrency if the jobs are being submitted by the same activity. Change the triggers so the concurrent pipeline runs are spread out over time. Refer to HDInsight documentation to adjust templeton.parallellism.job.submit as the error suggests.


### Error code:  2301

- **Message**: `Could not get the status of the application '%physicalJobId;' from the HDInsight service. Received the following error: %message;. Please refer to HDInsight troubleshooting documentation or contact their support for further assistance.`

- **Cause**: HDInsight cluster or service has issues.


- **Recommendation**: This error happens when ADF does not get response from HDInsight cluster when attempting to get the status of the running job. It could be cause of issues on the cluster itself, or HDInsight service might have an outage. Please refer to HDInsight troubleshooting documentation  at https://docs.microsoft.com/azure/hdinsight/hdinsight-troubleshoot-guide, or contact their support for further assistance.
                


### Error code:  2302

- **Message**: `Hadoop job failed with exit code '%exitCode;'. See '%logPath;/stderr' for more details. Alternatively, open the Ambari UI on the HDI cluster and find the logs for the job '%jobId;'. Contact HDInsight team for further support.`

- **Cause**: The job was submitted to HDI cluster and failed there.

- **Recommendation**:  Follow the Yarn logs link in the activity run Output and look for the errors in HDI output. Contact HDInsight team for support if needed.


### Error code:  2303

- **Message**: `Hadoop job failed with transient exit code '%exitCode;'. See '%logPath;/stderr' for more details. Alternatively, open the Ambari UI on the HDI cluster and find the logs for the job '%jobId;'. Try again or contact HDInsight team for further support.`

- **Cause**: The job was submitted to HDI cluster and failed there.

- **Recommendation**:  Follow the Yarn logs link in the activity run Output and look for the errors in HDI output. Try again or contact HDInsight team for support if needed.


### Error code:  2304

- **Message**: `MSI authentication is not supported on storages for HDI activities.`

- **Cause**: The storage linked service(s) used in HDI linked service or HDI activity are configured with MSI authentication which is not supported.

- **Recommendation**:  Please provide full connection strings for storage accounts used in HDI linked service or HDI activity.


### Error code:  2305

- **Message**: `Failed to initialize the HDInsight client for the cluster '%cluster;'. Error: '%message;'`

- **Cause**: The connection information for HDI cluster is incorrect, the provided user doesn't have permissions to perform the required action, or HDInsight service had issues responding to requests from ADF.

- **Recommendation**:  Please make sure the user information is correct. Also verify that the Ambari UI for the HDI cluster can be opened in a browser from the VM where IR is installed on case of self-hosted IR, or can be opened from any machine in case Azure IR.


### Error code:  2306

- **Message**: `An invalid json is provided for script action '%scriptActionName;'. Error: '%message;'`

- **Cause**: The json provided for the script action is invalid.


- **Recommendation**: The error message should help to identify the issue. Please fix the json configuration and try again. Check https://docs.microsoft.com/azure/data-factory/compute-linked-services#azure-hdinsight-on-demand-linked-service for more information.
                


### Error code:  2310

- **Message**: `Failed to submit Spark job. Error: '%message;'`

- **Cause**: ADF tried to create a batch on a Spark cluster using Livy API (livy/batch), but received an error.

- **Recommendation**:  Please follow the error message to fix the issue. If there is not enough information to get it resolved, contact HDI team and provide them the batch ID and job ID which can be found in the activity run Output in ADF Monitoring page.


### Error code:  2312

- **Message**: `Spark job failed, batch id:%batchId;. Please follow the links in the activity run Output from ADF Monitoring page to troubleshoot the run on HDInsight Spark cluster. Please contact HDInsight support team for further assistance.`

- **Cause**: The job failed on HDInsight Spark cluster.

- **Recommendation**:  Please follow the links in the activity run Output in ADF Monitoring page to troubleshoot the run on HDInsight Spark cluster. Please contact HDInsight support team for further assistance.


### Error code:  2313

- **Message**: `The batch with ID '%batchId;' was not found on Spark cluster. Open the Spark History UI and try to find it there. Contact HDInsight support for further assistance.`

- **Cause**: The batch was deleted on the HDInsight Spark cluster.

- **Recommendation**:  Troubleshoot batches on the HDInsight Spark cluster. Contact HDInsight support for further assistance. 


### Error code:  2328

- **Message**: `Failed to create the on demand HDI cluster. Cluster or linked service name: '%clusterName;', error: '%message;'`

- **Cause**: `The error message should show the details of what went wrong.`

- **Recommendation**:  The error message should help to troubleshoot the issue.


### Error code:  2329

- **Message**: `Failed to delete the on demand HDI cluster. Cluster or linked service name: '%clusterName;', error: '%message;'`

- **Cause**: The error message should show the details of what went wrong.

- **Recommendation**:  The error message should help to troubleshoot the issue.


### Error code:  2331

- **Message**: `The file path should not be null or empty.`

- **Cause**: The provided file path is empty.

- **Recommendation**:  Please provide a path for a file that exists.


### Error code:  2340

- **Message**: `HDInsightOnDemand linked service does not support execution via SelfHosted IR. Your IR name is '%IRName;'. Please select an Azure IR instead.`

- **Cause**: HDInsightOnDemand linked service does not support execution via SelfHosted IR.

- **Recommendation**:  Please select an Azure IR and try again.


### Error code:  2341

- **Message**: `HDInsight cluster URL '%clusterUrl;' is incorrect, it must be in URI format and the scheme must be 'https'.`

- **Cause**: The provided URL is not in correct format.

- **Recommendation**:  Please fix the cluster URL and try again.


### Error code:  2342

- **Message**: `Failed to connect to HDInsight cluster: '%errorMessage;'.`

- **Cause**: The provided credentials are wrong for the cluster or there was a network configuration or connection issue or IR is having problems connecting to the cluster.

- **Recommendation**:  
      1. Verify that the credentials are correct by opening the HDInsight cluster's Ambari UI in a browser.
      2. If the cluster is in VNet and self-hosted IR is being used, the HDI URL to should be the private URL in VNets, that means it should have '-int' after the cluster name. For example "https://mycluster.azurehdinsight.net/" should be changed to "https://mycluster-int.azurehdinsight.net/".
      2. If the cluster is in VNet, self-hosted IR is being used, and the private URL was used, and the connection still failed, then the VM where the IR is installed had problems connecting to the HDI. Connect to the VM where IR is installed and open the Ambari UI in a browser. Use the private URL for the cluster. This connection should work from the browser. If it doesn't, contact HDInsight support team for further assistance.
      3. If self-hosted IR is not being used, then the HDI cluster should be accessible publicly. Open the Ambari UI in a browser and make sure it opens up. If there are any issues with the cluster or the services on it, contact HDInsight support team for assistance.
      So in general, the HDI cluster URL used in ADF linked service must be accessible for ADF IR (self-hosted or Azure) in order for the test connection to pass, and for runs to work. This can be easily verified by opening that URL from a browser either from VM or any public machine.
    


### Error code:  2343

- **Message**: `User name and password cannot be null or empty to connect to the HDInsight cluster.`

- **Cause**: Either user name or password are empty.

- **Recommendation**:  Provide correct credentials to connect to HDI and try again.


### Error code:  2345

- **Message**: `Failed to read the content of the hive script. Error: '%message;'`

- **Cause**: The script file doesn't exist or ADF could not connect to the location of the script.

- **Recommendation**:  Please verify that the script exists, and the associated linked service has proper credentials for connection.


### Error code:  2346

- **Message**: `Failed to create ODBC connection to the HDI cluster with error message '%message;'.`

- **Cause**: ADF tried to establish an ODBC connection to the HDI cluster and it failed with error.

- **Recommendation**:  The error message and error code should help in troubleshooting the ODBC connection errors. In case they are not enough to resolve the issue, please contact Azure HDInsight team for support.


### Error code:  2347

- **Message**: `Hive execution through ODBC failed with error message '%message;'.`

- **Cause**: ADF submitted the hive script for execution to the HDI cluster via ODBC connection and the script has failed on HDI.

- **Recommendation**:  The execution of the hive script failed on HDI cluster and error message and error code should help in troubleshooting. In case they are not enough to resolve the issue, please contact Azure HDInsight team for support.


### Error code:  2348

- **Message**: `The main storage has not been initialized. Please check the properties of the storage linked service in the HDI linked service.`

- **Cause**: The storage linked service properties are not set correctly.

- **Recommendation**:  Only full connection strings are supported in the main storage linked service for HDI activities. Please make sure you are not using MSI auth or applications.


### Error code:  2350

- **Message**: `Failed to prepare the files for the run '%jobId;'. HDI cluster: '%cluster;', Error: '%errorMessage;'`

- **Cause**: The credentials provided to connect to the storage where the files should be located are incorrect, or the files do not exist there.

- **Recommendation**:  This error happens when ADF does preparation steps for HDI activities. It tries to copy files to the main storage before submitting the job to HDI. Make sure that files exist in the provided location, the storage connection is correct. ADF HDI activities do not support MSI authentication on storage accounts related to HDI activities, so make sure that those linked services have full keys or are using Azure Key Vault.


### Error code:  2351

- **Message**: `Could not open the file '%filePath;' in container/fileSystem '%container;'.`

- **Cause**: The file does not exist at specified path.

- **Recommendation**:  Please check if the file actually exists, and the linked service with connection info pointing to this file has correct credentials.


### Error code:  2352

- **Message**: `The file storage has not been initialized. Please check the properties of the file storage linked service in the HDI activity.`

- **Cause**: The file storage linked service properties are not set correctly.

- **Recommendation**:  Please make sure the properties of the file storage linked service are properly configured.


### Error code:  2353

- **Message**: `The script storage has not been initialized. Please check the properties of the script storage linked service in the HDI activity.`

- **Cause**: The script storage linked service properties are not set correctly.

- **Recommendation**:  Please make sure the properties of the script storage linked service are properly configured.


### Error code:  2354

- **Message**: `The storage linked service type '%linkedServiceType;' is not supported for '%executorType;' activities for property '%linkedServicePropertyName;'.`

- **Cause**: The storage linked service type is not supported by the activity.

- **Recommendation**:  Please make sure the selected linked service has one of the supported types for the activity. HDI activities support AzureBlobStorage and AzureBlobFSStorage linked services.


### Error code:  2355

- **Message**: `The '%value' provided for commandEnvironment is incorrect. The expected value should be an array of strings where each string has the format CmdEnvVarName=CmdEnvVarValue.`

- **Cause**: The provided for commandEnvironment is incorrect.

- **Recommendation**:  
      Please make sure the provided value is similar to:
      \"commandEnvironment\": [
          \"variableName=variableValue\"
      ]
      And each variable appears in the list only once.
    


### Error code:  2356

- **Message**: `The commandEnvironment already contains a variable named '%variableName;'.`

- **Cause**: The variable was provided twice in the commandEnvironment .

- **Recommendation**:  
      Please make sure the provided value is similar to:
      \"commandEnvironment\": [
          \"variableName=variableValue\"
      ]
      And each variable appears in the list only once.
    


### Error code:  2357

- **Message**: `The certificate or password is wrong for ADLS Gen 1 storage.`

- **Cause**: The provided credentials are incorrect.

- **Recommendation**:  Please verify the connection information in ADLS Gen 1 linked service and make sure the test connection succeeds.


### Error code:  2358

- **Message**: `The value '%value;' for the required property 'TimeToLive' in the on demand HDInsight linked service '%linkedServiceName;' has invalid format. It should be a timespan between '00:05:00' and '24:00:00'.`

- **Cause**: The provided value for the required property 'TimeToLive' has invalid format. 

- **Recommendation**:  Please update the value to be in the suggested range and try again.


### Error code:  2359

- **Message**: `The value '%value;' for the property 'roles' is invalid. Expected types are 'zookeeper', 'headnode', and 'workernode'.`

- **Cause**: The provided value for the property 'roles' is invalid.

- **Recommendation**:  Please update the value to be one of the suggestions and try again.


### Error code:  2360

- **Message**: `The connection string in HCatalogLinkedService is invalid. Encountered an error while trying to parse: '%message;'.`

- **Cause**: The provided connection string for the HCatalogLinkedService is invalid.

- **Recommendation**:  Please update the value to a correct Azure SQL connection string and try again.


### Error code:  2361

- **Message**: `Failed to create on demand HDI cluster. Cluster name is '%clusterName;'.`

- **Cause**: The cluster creation failed, and ADF did not get an error back from HDInsight service.

- **Recommendation**:  Open the Azure portal, and try to find the HDI resource with provided name, and check the provisioning status. Contact HDInsight support team for further assistance.


### Error code:  2362

- **Message**: `Only Azure Blob storage accounts are supported as additional storages for HDInsight on demand linked service.`

- **Cause**: The provided additional storage was not Azure Blob storage.

- **Recommendation**:  Provide Azure Blob storage account as an additional storage for HDInsight on demand linked service.



## Web Activity

### Error code:  2128

- **Message**: `No response from the endpoint. Possible causes: network connectivity, DNS failure, server certificate validation or timeout.`

- **Cause**: Network connectivity, DNS failure, server certificate validation or timeout.

- **Recommendation**:  Validate that the endpoint you are trying to hit is responding to requests. You may use tools like Fiddler/Postman.


### Error code:  2108

- **Message**: `Error calling the endpoint '%url;'. Response status code: '%code;'`

- **Cause**: The request failed due to an underlying issue such as network connectivity, DNS failure, server certificate validation or timeout.

- **Recommendation**:  Use Fiddler/Postman to validate the request.
<br>


#### More details
To use Fiddler to create an HTTP session of the monitored web application:

1. Download, install, and open [Fiddler](https://www.telerik.com/download/fiddler).

1. If your web application uses HTTPS, go to **Tools** > **Fiddler Options** > **HTTPS**. Select **Capture HTTPS CONNECTs** and **Decrypt HTTPS traffic**.

   ![Fiddler options](media/data-factory-troubleshoot-guide/fiddler-options.png)

1. If your application uses TLS/SSL certificates, add the Fiddler certificate to your device. Go to **Tools** > **Fiddler Options** > **HTTPS** > **Actions** > **Export Root Certificate to Desktop**.

1. Turn off capturing by going to **File** > **Capture Traffic**. Or press **F12**.

1. Clear your browser's cache so that all cached items are removed and must be downloaded again.

1. Create a request:

   1. Select the **Composer** tab.

   1. Set the HTTP method and URL.
   
   1. Add headers and a request body if you need to.

   1. Select **Execute**.

1. Turn on traffic capturing again, and complete the problematic transaction on your page.

1. Go to **File** > **Save** > **All Sessions**.

For more information, see [Getting started with Fiddler](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/ConfigureFiddler).

## Next steps

For more troubleshooting help, try these resources:

*  [Data Factory blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory feature requests](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure videos](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [MSDN forum](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Stack Overflow forum for Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter information about Data Factory](https://twitter.com/hashtag/DataFactory)


            
