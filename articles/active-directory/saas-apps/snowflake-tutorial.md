---
title: 'Tutorial: Azure Active Directory integration with Snowflake | Microsoft Docs'
description: Learn how to configure single sign-on between Azure Active Directory and Snowflake.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/27/2018
ms.author: jeedes
---
# Tutorial: Azure Active Directory integration with Snowflake

In this tutorial, you learn how to integrate Snowflake with Azure Active Directory (Azure AD).
Integrating Snowflake with Azure AD provides you with the following benefits:

* You can control in Azure AD who has access to Snowflake.
* You can enable your users to be automatically signed-in to Snowflake (Single Sign-On) with their Azure AD accounts.
* You can manage your accounts in one central location - the Azure portal.

To learn more about SaaS app integration with Azure AD, see [What is application access and single sign-on with Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
If you don't have an Azure subscription, [create a free account](https://azure.microsoft.com/free/) before you begin.

## Prerequisites

To configure Azure AD integration with Snowflake, you need the following items:

* An Azure AD subscription. If you don't have an Azure AD environment, you can get one-month trial [here](https://azure.microsoft.com/pricing/free-trial/)
* Snowflake single sign-on enabled subscription

## Scenario description

In this tutorial, you will configure and test Azure AD single sign-on in a test environment.

- Snowflake supports **SP and IDP** initiated SSO
- Snowflake supports [Automated user provisioning and deprovisioning](snowflake-provisioning-tutorial.md) (recommended)

## Adding Snowflake from the gallery

To configure the integration of Snowflake into Azure AD, you need to add Snowflake from the gallery to your list of managed SaaS apps.

**Add Snowflake from the gallery:**

1. In the **[Azure portal](https://portal.azure.com)**, on the left navigation panel, click **Azure Active Directory** icon.

	![The Azure Active Directory button](common/select-azuread.png)

2. Navigate to **Enterprise Applications** and then select the **All Applications** option.

	![The Enterprise applications blade](common/enterprise-applications.png)

3. To add new application, click **New application** button on the top of dialog.

	![The New application button](common/add-new-app.png)

4. In the search box, type **Snowflake**, select **Snowflake** from result panel then click **Add** button to add the application.

	 ![Snowflake in the results list](common/search-new-app-snowflake.png)

## Configure and test Azure AD single sign-on

In this section, you will configure and test Azure AD single sign-on with Snowflake based on a test user called **Britta Simon**.
For single sign-on to work, a link relationship between an Azure AD user and the related user in Snowflake needs to be established.

To configure and test Azure AD single sign-on with Snowflake, you need to complete the following building blocks:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - to enable your users to use this feature.
2. **[Configure Snowflake Single Sign-On](#configure-snowflake-single-sign-on)** - to configure the Single Sign-On settings on application side.
3. **[Create an Azure AD test user](#create-an-azure-ad-test-user)** - to test Azure AD single sign-on with Britta Simon.
4. **[Assign the Azure AD test user](#assign-the-azure-ad-test-user)** - to enable Britta Simon to use Azure AD single sign-on.
5. **[Create Snowflake test user](#create-snowflake-test-user)** - to have a counterpart of Britta Simon in Snowflake that is linked to the Azure AD representation of user.
6. **[Test single sign-on](#test-single-sign-on)** - to verify whether the configuration works.

### Configure Azure AD single sign-on

In this section, you enable Azure AD single sign-on in the Azure portal.

To configure Azure AD single sign-on with Snowflake, perform the following steps:

1. In the [Azure portal](https://portal.azure.com/), on the **Snowflake** application integration page, select **Single sign-on**.

    ![Configure single sign-on link](common/select-sso.png)

2. On the **Select a Single sign-on method** dialog, select **SAML/WS-Fed** mode to enable single sign-on.

    ![Single sign-on select mode](common/select-saml-option.png)

3. On the **Set up Single Sign-On with SAML** page, click **Edit** icon to open **Basic SAML Configuration** dialog.

	![Edit Basic SAML Configuration](common/edit-urls.png)

4. In the **Basic SAML Configuration** section, perform the following steps, if you wish to configure the application in **IDP** initiated mode:

    ![Screenshot shows the Basic SAML Configuration, where you can enter Identifier, Reply U R L, and select Save.](common/idp-intiated.png)

    a. In the **Identifier** text box, type a URL using the following pattern:
    `https://<SNOWFLAKE-URL>.snowflakecomputing.com`

    b. In the **Reply URL** text box, type a URL using the following pattern:
    `https://<SNOWFLAKE-URL>.snowflakecomputing.com/fed/login`

    c. Click **Set additional URLs** and perform the following step if you wish to configure the application in SP initiated mode:

    ![Screenshot shows Set additional U R Ls where you can enter a Sign on U R L.](common/metadata-upload-additional-signon.png)

	In the **Sign-on URL** text box, type a URL using the following pattern:
    `https://<SNOWFLAKE-URL>.snowflakecomputing.com`
    
	In the **Logout URL** text box, type a URL using the following pattern:
    `https://<SNOWFLAKE-URL>.snowflakecomputing.com/fed/logout`

    > [!NOTE]
	> These values are not real. Update these values with the actual Identifier, Reply URL and Sign-on URL. Contact [Snowflake Client support team](https://support.snowflake.net/s/) to get these values. You can also refer to the patterns shown in the **Basic SAML Configuration** section in the Azure portal.

4. On the **Set up Single Sign-On with SAML** page, in the **SAML Signing Certificate** section, click **Download** to download the **Certificate (Base64)** from the given options as per your requirement and save it on your computer.

	![The Certificate download link](common/certificatebase64.png)

6. On the **Set up Snowflake** section, copy the appropriate URL(s) as per your requirement.

	![Copy configuration URLs](common/copy-configuration-urls.png)

	a. Login URL

	b. Azure Ad Identifier

	c. Logout URL

### Configure Snowflake Single Sign-On

1. In a different web browser window, login to Snowflake as a Security Administrator.

1. **Switch Role** to **ACCOUNTADMIN**, by clicking on **profile** on the top right side of page.

	> [!NOTE]
	> This is separate from the context you have selected in the top-right corner under your User Name
    
	![The Snowflake admin](./media/snowflake-tutorial/tutorial_snowflake_accountadmin.png)

1. Open the **downloaded Base 64 certificate** in notepad. Copy the value between “-----BEGIN CERTIFICATE-----” and “-----END CERTIFICATE-----" and paste this into the quotation marks next to **certificate** below. In the **ssoUrl**, paste **Login URL** value which you have copied from the Azure portal. Select the **All Queries** and click **Run**.

   ![Snowflake sql](./media/snowflake-tutorial/tutorial_snowflake_sql.png)

   ```
   use role accountadmin;
   alter account set saml_identity_provider = '{
   "certificate": "<Paste the content of downloaded certificate from Azure portal>",
   "ssoUrl":"<Login URL value which you have copied from the Azure portal>",
   "type":"custom",
   "label":"AzureAD"
   }';
   alter account set sso_login_page = TRUE;
   ```

### Create an Azure AD test user 

The objective of this section is to create a test user in the Azure portal called Britta Simon.

1. In the Azure portal, in the left pane, select **Azure Active Directory**, select **Users**, and then select **All users**.

    ![The "Users and groups" and "All users" links](common/users.png)

2. Select **New user** at the top of the screen.

    ![New user Button](common/new-user.png)

3. In the User properties, perform the following steps.

    ![The User dialog box](common/user-properties.png)

    a. In the **Name** field enter **BrittaSimon**.
  
    b. In the **User name** field type **brittasimon\@yourcompanydomain.extension**  
    For example, BrittaSimon@contoso.com

    c. Select **Show password** check box, and then write down the value that's displayed in the Password box.

    d. Click **Create**.

### Assign the Azure AD test user

In this section, you enable Britta Simon to use Azure single sign-on by granting access to Snowflake.

1. In the Azure portal, select **Enterprise Applications**, select **All applications**, then select **Snowflake**.

	![Enterprise applications blade](common/enterprise-applications.png)

2. In the applications list, type and select **Snowflake**.

	![The Snowflake link in the Applications list](common/all-applications.png)

3. In the menu on the left, select **Users and groups**.

    ![The "Users and groups" link](common/users-groups-blade.png)

4. Click the **Add user** button, then select **Users and groups** in the **Add Assignment** dialog.

    ![The Add Assignment pane](common/add-assign-user.png)

5. In the **Users and groups** dialog select **Britta Simon** in the Users list, then click the **Select** button at the bottom of the screen.

6. If you are expecting any role value in the SAML assertion then in the **Select Role** dialog select the appropriate role for the user from the list, then click the **Select** button at the bottom of the screen.

7. In the **Add Assignment** dialog click the **Assign** button.

### Create Snowflake test user

To enable Azure AD users to log in to Snowflake, they must be provisioned into Snowflake. In Snowflake, provisioning is a manual task.

**To provision a user account, perform the following steps:**

1. Log in to Snowflake as a Security Administrator.

2. **Switch Role** to **ACCOUNTADMIN**, by clicking on **profile** on the top right side of page.  

	![The Snowflake admin](./media/snowflake-tutorial/tutorial_snowflake_accountadmin.png)

3. Create the user by running the below SQL query, ensuring "Login name" is set to the Azure AD username on the worksheet as shown below.

	![The Snowflake adminsql](./media/snowflake-tutorial/tutorial_snowflake_usersql.png)

    ```
	use role accountadmin;
	CREATE USER britta_simon PASSWORD = '' LOGIN_NAME = 'BrittaSimon@contoso.com' DISPLAY_NAME = 'Britta Simon';
    ```

### Test single sign-on 

In this section, you test your Azure AD single sign-on configuration using the Access Panel.

When you click the Snowflake tile in the Access Panel, you should be automatically signed in to the Snowflake for which you set up SSO. For more information about the Access Panel, see [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md).

## Additional Resources

- [List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [What is Conditional Access in Azure Active Directory?](../conditional-access/overview.md)