---
title: Use Azure Key Vault secrets in pipeline activities 
description: Learn how to fetch stored credentials from Azure key vault and use them during data factory pipeline runs. 
services: data-factory
author: ChrisLound
manager: anandsub
editor: ''

ms.service: data-factory
ms.workload: data-services


ms.topic: conceptual
ms.date: 10/31/2019
ms.author: chlound
---

# Use Azure Key Vault secrets in pipeline activities

You can store credentials or secret values in an Azure Key Vault and use them during pipeline execution to pass to your activities.

## Prerequisites

This feature relies on the data factory managed identity.  Learn how it works from [Managed identity for Data Factory](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity) and make sure your data factory has one associated.

## Steps

1. Open the properties of your data factory and copy the Managed Identity Application ID value.

    ![Managed Identity Value](media/how-to-use-azure-key-vault-secrets-pipeline-activities/managedidentity.png)

2. Open the key vault access policies and add the managed identity permissions to Get and List secrets.

    ![Key Vault access policies](media/how-to-use-azure-key-vault-secrets-pipeline-activities/akvaccesspolicies.png)

    ![Key Vault access policies](media/how-to-use-azure-key-vault-secrets-pipeline-activities/akvaccesspolicies-2.png)

    Click **Add**, then click **Save**.

3. Navigate to your Key Vault secret and copy the Secret Identifier.

    ![Secret Identifier](media/how-to-use-azure-key-vault-secrets-pipeline-activities/secretidentifier.png)

    Make a note of your secret URI that you want to get during your data factory pipeline run.

4. In your Data Factory pipeline, add a new Web activity and configure it as follows.  

    |Property  |Value  |
    |---------|---------|
    |Secure Output     |True         |
    |URL     |[Your secret URI value]?api-version=7.0         |
    |Method     |GET         |
    |Authentication     |MSI         |
    |Resource        |https://vault.azure.net       |

    ![Web activity](media/how-to-use-azure-key-vault-secrets-pipeline-activities/webactivity.png)

    > [!IMPORTANT]
    > You must add **?api-version=7.0** to the end of your secret URI.  

    > [!CAUTION]
    > Set the Secure Output option to true to prevent the secret value from being logged in plain text.  Any further activities that consume this value should have their Secure Input option set to true.

5. To use the value in another activity, use the following code expression **@activity("web").output.value)**.

    ![Code expression](media/how-to-use-azure-key-vault-secrets-pipeline-activities/usewebactivity.png)

## Next steps

To learn how to use Azure Key Vault to store credentials for data stores and computes, see [Store credentials in Azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)
