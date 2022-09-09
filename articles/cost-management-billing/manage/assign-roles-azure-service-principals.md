---
title: Assign roles to Azure Enterprise Agreement service principal names
description: This article helps you assign roles to service principal names by using PowerShell and REST APIs.
author: bandersmsft
ms.reviewer: ruturajd
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 04/05/2021
ms.author: banders
---

# Assign roles to Azure Enterprise Agreement service principal names

You can manage your Enterprise Agreement (EA) enrollment in the [Azure Enterprise portal](https://ea.azure.com/). You can create different roles to manage your organization, view costs, and create subscriptions. This article helps you automate some of those tasks by using Azure PowerShell and REST APIs with Azure service principal names (SPNs).

Before you begin, ensure that you're familiar with the following articles:

- [Enterprise agreement roles](understand-ea-roles.md)
- [Sign in with Azure PowerShell](/powershell/azure/authenticate-azureps)
- [How to call REST APIs with Postman](/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

## Create and authenticate your service principal

To automate EA actions by using an SPN, you need to create an Azure Active Directory (Azure AD) application. It can authenticate in an automated manner.

Follow the steps in these articles to create and authenticate your service principal.

- [Create a service principal](../../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)
- [Get tenant and app ID values for signing in](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)

Here's an example of the application registration page.

:::image type="content" source="./media/assign-roles-azure-service-principals/register-an-application.png" alt-text="Screenshot showing Register an application." lightbox="./media/assign-roles-azure-service-principals/register-an-application.png" :::

### Find your SPN and tenant ID

You also need the object ID of the SPN and the tenant ID of the app. You need this information for permission assignment operations later in this article.

1. Open Azure Active Directory, and then select **Enterprise applications**.
1. Find your app in the list.

   :::image type="content" source="./media/assign-roles-azure-service-principals/enterprise-application.png" alt-text="Screenshot showing an example enterprise application." lightbox="./media/assign-roles-azure-service-principals/enterprise-application.png" :::

1. Select the app to find the application ID and object ID:

   :::image type="content" source="./media/assign-roles-azure-service-principals/application-id-object-id.png" alt-text="Screenshot showing an application ID and object ID for an enterprise application." lightbox="./media/assign-roles-azure-service-principals/application-id-object-id.png" :::

1. Go to the Microsoft Azure AD **Overview** page to find the tenant ID.

   :::image type="content" source="./media/assign-roles-azure-service-principals/tenant-id.png" alt-text="Screenshot showing the tenant ID." lightbox="./media/assign-roles-azure-service-principals/tenant-id.png" :::

>[!NOTE]
>Your tenant ID might be called a principal ID, SPN, or object ID in other locations. The value of your Azure AD tenant ID looks like a GUID with the following format: `11111111-1111-1111-1111-111111111111`.

## Permissions that can be assigned to the SPN

Later in this article, you'll give permission to the Azure AD app to act by using an EA role. You can assign only the following roles to the SPN, and you need the role definition ID, exactly as shown.

| Role | Actions allowed | Role definition ID |
| --- | --- | --- |
| EnrollmentReader | Can view usage and charges across all accounts and subscriptions. Can view the Azure Prepayment (previously called monetary commitment) balance associated with the enrollment. | 24f8edb6-1668-4659-b5e2-40bb5f3a7d7e |
| EA purchaser | Purchase reservation orders and view reservation transactions. Can view usage and charges across all accounts and subscriptions. Can view the Azure Prepayment (previously called monetary commitment) balance associated with the enrollment. | da6647fb-7651-49ee-be91-c43c4877f0c4  |
| DepartmentReader | Download the usage details for the department they administer. Can view the usage and charges associated with their department. | db609904-a47f-4794-9be8-9bd86fbffd8a |
| SubscriptionCreator | Create new subscriptions in the given scope of Account. | a0bcee42-bf30-4d1b-926a-48d21664ef71 |

- An EnrollmentReader role can be assigned to an SPN only by a user who has an enrollment writer role.
- A DepartmentReader role can be assigned to an SPN only by a user who has an enrollment writer or department writer role.
- A SubscriptionCreator role can be assigned to an SPN only by a user who is the owner of the enrollment account. The role isn't shown in the EA portal. It's created by programmatic means and is only for programmatic use.
- The EA purchaser role isn't shown in the EA portal. It's created by programmatic means and is only for programmatic use.

## Assign enrollment account role permission to the SPN

1. Read the [Role Assignments - Put](/rest/api/billing/2019-10-01-preview/roleassignments/put) REST API article. While you read the article, select **Try it** to get started by using the SPN.

   :::image type="content" source="./media/assign-roles-azure-service-principals/put-try-it.png" alt-text="Screenshot showing the Try It option in the Put article." lightbox="./media/assign-roles-azure-service-principals/put-try-it.png" :::

1. Use your account credentials to sign in to the tenant with the enrollment access that you want to assign.

1. Provide the following parameters as part of the API request.

   - `billingAccountName`: This parameter is the **Billing account ID**. You can find it in the Azure portal on the **Cost Management + Billing** overview page.

      :::image type="content" source="./media/assign-roles-azure-service-principals/billing-account-id.png" alt-text="Screenshot showing Billing account ID." lightbox="./media/assign-roles-azure-service-principals/billing-account-id.png" :::

   - `billingRoleAssignmentName`: This parameter is a unique GUID that you need to provide. You can generate a GUID using the [New-Guid](/powershell/module/microsoft.powershell.utility/new-guid) PowerShell command. You can also use the [Online GUID / UUID Generator](https://guidgenerator.com/) website to generate a unique GUID.

   - `api-version`: Use the **2019-10-01-preview** version. Use the sample request body at [Role Assignments - Put - Examples](/rest/api/billing/2019-10-01-preview/roleassignments/put#examples).

      The request body has JSON code with three parameters that you need to use.

      | Parameter | Where to find it |
      | --- | --- |
      | `properties.principalId` | See [Find your SPN and tenant ID](#find-your-spn-and-tenant-id). |
      | `properties.principalTenantId` | See [Find your SPN and tenant ID](#find-your-spn-and-tenant-id). |
      | `properties.roleDefinitionId` | `/providers/Microsoft.Billing/billingAccounts/{BillingAccountName}/billingRoleDefinitions/24f8edb6-1668-4659-b5e2-40bb5f3a7d7e` |

      The billing account name is the same parameter that you used in the API parameters. It's the enrollment ID that you see in the EA portal and Azure portal.

      Notice that `24f8edb6-1668-4659-b5e2-40bb5f3a7d7e` is a billing role definition ID for an EnrollmentReader.

1. Select **Run** to start the command.

   :::image type="content" source="./media/assign-roles-azure-service-principals/roleassignments-put-try-it-run.png" alt-text="Screenshot showing an example role assignment put Try It with example information ready to run." lightbox="./media/assign-roles-azure-service-principals/roleassignments-put-try-it-run.png" :::

   A `200 OK` response shows that the SPN was successfully added.

Now you can use the SPN to automatically access EA APIs. The SPN has the EnrollmentReader role.

## Assign EA Purchaser role permission to the SPN

For the EA purchaser role, use the same steps for the enrollment reader. Specify the `roleDefinitionId`, using the following example:

`"/providers/Microsoft.Billing/billingAccounts/1111111/billingRoleDefinitions/ da6647fb-7651-49ee-be91-c43c4877f0c4"`

## Assign the department reader role to the SPN

1. Read the [Enrollment Department Role Assignments - Put](/rest/api/billing/2019-10-01-preview/enrollmentdepartmentroleassignments/put) REST API article. While you read the article, select **Try it**.

   :::image type="content" source="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it.png" alt-text="Screenshot showing the Try It option in the Enrollment Department Role Assignments Put article." lightbox="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it.png" :::

1. Use your account credentials to sign in to the tenant with the enrollment access that you want to assign.

1. Provide the following parameters as part of the API request.

   - `billingAccountName`: This parameter is the **Billing account ID**. You can find it in the Azure portal on the **Cost Management + Billing** overview page.

      :::image type="content" source="./media/assign-roles-azure-service-principals/billing-account-id.png" alt-text="Screenshot showing Billing account ID." lightbox="./media/assign-roles-azure-service-principals/billing-account-id.png" :::

   - `billingRoleAssignmentName`: This parameter is a unique GUID that you need to provide. You can generate a GUID using the [New-Guid](/powershell/module/microsoft.powershell.utility/new-guid) PowerShell command. You can also use the [Online GUID / UUID Generator](https://guidgenerator.com/) website to generate a unique GUID.

   - `departmentName`: This parameter is the department ID. You can see department IDs in the Azure portal on the **Cost Management + Billing** > **Departments** page.

      For this example, we used the ACE department. The ID for the example is `84819`.

      :::image type="content" source="./media/assign-roles-azure-service-principals/department-id.png" alt-text="Screenshot showing an example department ID." lightbox="./media/assign-roles-azure-service-principals/department-id.png" :::

   - `api-version`: Use the **2019-10-01-preview** version. Use the sample at [Enrollment Department Role Assignments - Put](/billing/2019-10-01-preview/enrollmentdepartmentroleassignments/put).

      The request body has JSON code with three parameters that you need to use.

      | Parameter | Where to find it |
      | --- | --- |
      | `properties.principalId` | See [Find your SPN and tenant ID](#find-your-spn-and-tenant-id). |
      | `properties.principalTenantId` | See [Find your SPN and tenant ID](#find-your-spn-and-tenant-id). |
      | `properties.roleDefinitionId` | `/providers/Microsoft.Billing/billingAccounts/{BillingAccountName}/billingRoleDefinitions/db609904-a47f-4794-9be8-9bd86fbffd8a` |

      The billing account name is the same parameter that you used in the API parameters. It's the enrollment ID that you see in the EA portal and Azure portal.

      The billing role definition ID of `db609904-a47f-4794-9be8-9bd86fbffd8a` is for a department reader.

1. Select **Run** to start the command.

   :::image type="content" source="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it-run.png" alt-text="Screenshot showing an example Enrollment Department Role Assignments – Put REST Try It with example information ready to run." lightbox="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it-run.png" :::

   A `200 OK` response shows that the SPN was successfully added.

Now you can use the SPN to automatically access EA APIs. The SPN has the DepartmentReader role.

## Assign the subscription creator role to the SPN

1. Read the [Enrollment Account Role Assignments - Put](/rest/api/billing/2019-10-01-preview/enrollmentaccountroleassignments/put) article. While you read it, select **Try It** to assign the subscription creator role to the SPN.

   :::image type="content" source="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it.png" alt-text="Screenshot showing the Try It option in the Enrollment Account Role Assignments Put article." lightbox="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it.png" :::

1. Use your account credentials to sign in to the tenant with the enrollment access that you want to assign.

1. Provide the following parameters as part of the API request. Read the article at [Enrollment Account Role Assignments - Put - URI Parameters](/rest/api/billing/2019-10-01-preview/enrollmentaccountroleassignments/put#uri-parameters).

   - `billingAccountName`: This parameter is the **Billing account ID**. You can find it in the Azure portal on the **Cost Management + Billing overview** page.

      :::image type="content" source="./media/assign-roles-azure-service-principals/billing-account-id.png" alt-text="Screenshot showing the Billing account ID." lightbox="./media/assign-roles-azure-service-principals/billing-account-id.png" :::

   - `billingRoleAssignmentName`: This parameter is a unique GUID that you need to provide. You can generate a GUID using the [New-Guid](/powershell/module/microsoft.powershell.utility/new-guid) PowerShell command. You can also use the [Online GUID/UUID Generator](https://guidgenerator.com/) website to generate a unique GUID.

   - `enrollmentAccountName`: This parameter is the account **ID**. Find the account ID for the account name in the Azure portal on the **Cost Management + Billing** page.

      For this example, we used the GTM Test Account. The ID is `196987`.

      :::image type="content" source="./media/assign-roles-azure-service-principals/account-id.png" alt-text="Screenshot showing the account ID." lightbox="./media/assign-roles-azure-service-principals/account-id.png" :::

   - `api-version`: Use the **2019-10-01-preview** version. Use the sample at [Enrollment Department Role Assignments - Put - Examples](/rest/api/billing/2019-10-01-preview/enrollmentdepartmentroleassignments/put#putenrollmentdepartmentadministratorroleassignment).

      The request body has JSON code with three parameters that you need to use.

      | Parameter | Where to find it |
      | --- | --- |
      | `properties.principalId` | See [Find your SPN and tenant ID](#find-your-spn-and-tenant-id). |
      | `properties.principalTenantId` | See [Find your SPN and tenant ID](#find-your-spn-and-tenant-id). |
      | `properties.roleDefinitionId` | `/providers/Microsoft.Billing/billingAccounts/{BillingAccountID}/enrollmentAccounts/196987/billingRoleDefinitions/a0bcee42-bf30-4d1b-926a-48d21664ef71` |

      The billing account name is the same parameter that you used in the API parameters. It's the enrollment ID that you see in the EA portal and the Azure portal.

      The billing role definition ID of `a0bcee42-bf30-4d1b-926a-48d21664ef71` is for the subscription creator role.

1. Select **Run** to start the command.

   :::image type="content" source="./media/assign-roles-azure-service-principals/enrollment-account-role-assignments-put-try-it.png" alt-text="Screenshot showing the Try It option in the Enrollment Account Role Assignments - Put article" lightbox="./media/assign-roles-azure-service-principals/enrollment-account-role-assignments-put-try-it.png" :::

   A `200 OK` response shows that the SPN has been successfully added.

Now you can use the SPN to automatically access EA APIs. The SPN has the SubscriptionCreator role.

## Next steps

Learn more about [Azure EA portal administration](ea-portal-administration.md).
