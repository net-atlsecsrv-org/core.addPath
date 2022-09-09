---
title: Secure SQL Database connection with managed identity - Azure App Service | Microsoft Docs 
description: Learn how to make database connectivity more secure by using a managed identity, and also how to apply it to other Azure services.
services: app-service\web
documentationcenter: dotnet
author: cephalin
manager: syntaxc4
editor: ''

ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 09/16/2019
ms.author: cephalin
ms.custom: mvc
---
# Tutorial: Secure Azure SQL Database connection from App Service using a managed identity

[App Service](overview.md) provides a highly scalable, self-patching web hosting service in Azure. It also provides a [managed identity](overview-managed-identity.md) for your app, which is a turn-key solution for securing access to [Azure SQL Database](/azure/sql-database/) and other Azure services. Managed identities in App Service make your app more secure by eliminating secrets from your app, such as credentials in the connection strings. In this tutorial, you will add managed identity to the sample web app you built in one of the following tutorials: 

- [Tutorial: Build an ASP.NET app in Azure with SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md)
- [Tutorial: Build an ASP.NET Core and SQL Database app in Azure App Service](app-service-web-tutorial-dotnetcore-sqldb.md)

When you're finished, your sample app will connect to SQL Database securely without the need of username and passwords.

> [!NOTE]
> The steps covered in this tutorial support the following versions:
> 
> - .NET Framework 4.7.2 and above.
> - .NET Core 2.2 and above.
>

What you will learn:

> [!div class="checklist"]
> * Enable managed identities
> * Grant SQL Database access to the managed identity
> * Configure Entity Framework to use Azure AD authentication with SQL Database
> * Connect to SQL Database from Visual Studio using Azure AD authentication

> [!NOTE]
>Azure AD authentication is _different_ from [Integrated Windows authentication](/previous-versions/windows/it-pro/windows-server-2003/cc758557(v=ws.10)) in on-premises Active Directory (AD DS). AD DS and Azure AD use completely different authentication protocols. For more information, see [Azure AD Domain Services documentation](https://docs.microsoft.com/azure/active-directory-domain-services/).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## Prerequisites

This article continues where you left off in [Tutorial: Build an ASP.NET app in Azure with SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md) or [Tutorial: Build an ASP.NET Core and SQL Database app in Azure App Service](app-service-web-tutorial-dotnetcore-sqldb.md). If you haven't already, follow one of the two tutorials first. Alternatively, you can adapt the steps for your own .NET app with SQL Database.

To debug your app using SQL Database as the back end, make sure that you've allowed client connection from your computer. If not, add the client IP by following the steps at [Manage server-level IP firewall rules using the Azure portal](../sql-database/sql-database-firewall-configure.md#use-the-azure-portal-to-manage-server-level-ip-firewall-rules).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## Grant database access to Azure AD user

First enable Azure AD authentication to SQL Database by assigning an Azure AD user as the Active Directory admin of the SQL Database server. This user is different from the Microsoft account you used to sign up for your Azure subscription. It must be a user that you created, imported, synced, or invited into Azure AD. For more information on allowed Azure AD users, see [Azure AD features and limitations in SQL Database](../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations).

If your Azure AD tenant doesn't have a user yet, create one by following the steps at [Add or delete users using Azure Active Directory](../active-directory/fundamentals/add-users-azure-active-directory.md).

Find the object ID of the Azure AD user using the [`az ad user list`](/cli/azure/ad/user?view=azure-cli-latest#az-ad-user-list) and replace *\<user-principal-name>*. The result is saved to a variable.

```azurecli-interactive
azureaduser=$(az ad user list --filter "userPrincipalName eq '<user-principal-name>'" --query [].objectId --output tsv)
```
> [!TIP]
> To see the list of all user principal names in Azure AD, run `az ad user list --query [].userPrincipalName`.
>

Add this Azure AD user as an Active Directory admin using [`az sql server ad-admin create`](/cli/azure/sql/server/ad-admin?view=azure-cli-latest#az-sql-server-ad-admin-create) command in the Cloud Shell. In the following command, replace *\<server-name>* with the SQL Database server name (without the `.database.windows.net` suffix).

```azurecli-interactive
az sql server ad-admin create --resource-group myResourceGroup --server-name <server-name> --display-name ADMIN --object-id $azureaduser
```

For more information on adding an Active Directory admin, see [Provision an Azure Active Directory administrator for your Azure SQL Database Server](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)

## Set up Visual Studio

### Windows
Visual Studio for Windows is integrated with Azure AD authentication. To enable development and debugging in Visual Studio, add your Azure AD user in Visual Studio by selecting **File** > **Account Settings** from the menu, and click **Add an account**.

To set the Azure AD user for Azure service authentication, select **Tools** > **Options** from the menu, then select **Azure Service Authentication** > **Account Selection**. Select the Azure AD user you added and click **OK**.

You're now ready to develop and debug your app with the SQL Database as the back end, using Azure AD authentication.

### MacOS

Visual Studio for Mac is not integrated with Azure AD authentication. However, the [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) library that you will use later can use tokens from Azure CLI. To enable development and debugging in Visual Studio, first you need to [install Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) on your local machine.

Once Azure CLI is installed on your local machine, sign in to Azure CLI with the following command using your Azure AD user:

```bash
az login --allow-no-subscriptions
```
You're now ready to develop and debug your app with the SQL Database as the back end, using Azure AD authentication.

## Modify your project

The steps you follow for your project depends on whether it's an ASP.NET project or an ASP.NET Core project.

- [Modify ASP.NET](#modify-aspnet)
- [Modify ASP.NET Core](#modify-aspnet-core)

### Modify ASP.NET

In Visual Studio, open the Package Manager Console and add the NuGet package [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication):

```powershell
Install-Package Microsoft.Azure.Services.AppAuthentication -Version 1.3.0
```

In *Web.config*, working from the top of the file and make the following changes:

- In `<configSections>`, add the following section declaration in it:

    ```xml
    <section name="SqlAuthenticationProviders" type="System.Data.SqlClient.SqlAuthenticationProviderConfigurationSection, System.Data, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" />
    ```

- below the closing `</configSections>` tag, add the following XML code for `<SqlAuthenticationProviders>`.

    ```xml
    <SqlAuthenticationProviders>
      <providers>
        <add name="Active Directory Interactive" type="Microsoft.Azure.Services.AppAuthentication.SqlAppAuthenticationProvider, Microsoft.Azure.Services.AppAuthentication" />
      </providers>
    </SqlAuthenticationProviders>
    ```    

- Find the connection string called `MyDbConnection` and replace its `connectionString` value with `"server=tcp:<server-name>.database.windows.net;database=<db-name>;UID=AnyString;Authentication=Active Directory Interactive"`. Replace _\<server-name>_ and _\<db-name>_ with your server name and database name.

That's every thing you need to connect to SQL Database. When debugging in Visual Studio, your code uses the Azure AD user you configured in [Set up Visual Studio](#set-up-visual-studio). You'll set up the SQL Database server later to allow connection from the managed identity of your App Service app.

Type `Ctrl+F5` to run the app again. The same CRUD app in your browser is now connecting to the Azure SQL Database directly, using Azure AD authentication. This setup lets you run database migrations from Visual Studio.

### Modify ASP.NET Core

In Visual Studio, open the Package Manager Console and add the NuGet package [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication):

```powershell
Install-Package Microsoft.Azure.Services.AppAuthentication -Version 1.3.0
```

In the [ASP.NET Core and SQL Database tutorial](app-service-web-tutorial-dotnetcore-sqldb.md), the `MyDbConnection` connection string isn't used at all because the local development environment uses a Sqlite database file, and the Azure production environment uses a connection string from App Service. With Active Directory authentication, you want both environments to use the same connection string. In *appsettings.json*, replace the value of the `MyDbConnection` connection string with:

```json
"Server=tcp:<server-name>.database.windows.net,1433;Database=<database-name>;"
```

In *Startup.cs*, remove the code section that you added before:

```csharp
// Use SQL Database if in Azure, otherwise, use SQLite
if (Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT") == "Production")
    services.AddDbContext<MyDatabaseContext>(options =>
            options.UseSqlServer(Configuration.GetConnectionString("MyDbConnection")));
else
    services.AddDbContext<MyDatabaseContext>(options =>
            options.UseSqlite("Data Source=localdatabase.db"));

// Automatically perform database migration
services.BuildServiceProvider().GetService<MyDatabaseContext>().Database.Migrate();
```

And replace it with the following code:

```csharp
services.AddDbContext<MyDatabaseContext>(options => {
    options.UseSqlServer(Configuration.GetConnectionString("MyDbConnection"));
});
```

Next, you supply the Entity Framework database context with the access token for the SQL Database. In *Data\MyDatabaseContext.cs*, add the following code inside the curly braces of the empty `MyDatabaseContext (DbContextOptions<MyDatabaseContext> options)` constructor:

```csharp
var conn = (System.Data.SqlClient.SqlConnection)Database.GetDbConnection();
conn.AccessToken = (new Microsoft.Azure.Services.AppAuthentication.AzureServiceTokenProvider()).GetAccessTokenAsync("https://database.windows.net/").Result;
```

> [!TIP]
> This demonstration code is synchronous for clarity. For more information, see [Asynchronous guide for constructors](https://github.com/davidfowl/AspNetCoreDiagnosticScenarios/blob/master/AsyncGuidance.md#constructors).

That's every thing you need to connect to SQL Database. When debugging in Visual Studio, your code uses the Azure AD user you configured in [Set up Visual Studio](#set-up-visual-studio). You'll set up the SQL Database server later to allow connection from the managed identity of your App Service app. The `AzureServiceTokenProvider` class caches the token in memory and retrieves it from Azure AD just before expiration. You don't need any custom code to refresh the token.

Type `Ctrl+F5` to run the app again. The same CRUD app in your browser is now connecting to the Azure SQL Database directly, using Azure AD authentication. This setup lets you run database migrations from Visual Studio.

## Use managed identity connectivity

Next, you configure your App Service app to connect to SQL Database with a system-assigned managed identity.

### Enable managed identity on app

To enable a managed identity for your Azure app, use the [az webapp identity assign](/cli/azure/webapp/identity?view=azure-cli-latest#az-webapp-identity-assign) command in the Cloud Shell. In the following command, replace *\<app-name>*.

```azurecli-interactive
az webapp identity assign --resource-group myResourceGroup --name <app-name>
```

Here's an example of the output:

```json
{
  "additionalProperties": {},
  "principalId": "21dfa71c-9e6f-4d17-9e90-1d28801c9735",
  "tenantId": "72f988bf-86f1-41af-91ab-2d7cd011db47",
  "type": "SystemAssigned"
}
```

### Add managed identity to an Azure AD group

To grant this identity access to your SQL Database, you need to add it to an [Azure AD group](../active-directory/fundamentals/active-directory-manage-groups.md). In the Cloud Shell, add it to a new group called _myAzureSQLDBAccessGroup_, shown in the following script:

```azurecli-interactive
groupid=$(az ad group create --display-name myAzureSQLDBAccessGroup --mail-nickname myAzureSQLDBAccessGroup --query objectId --output tsv)
msiobjectid=$(az webapp identity show --resource-group myResourceGroup --name <app-name> --query principalId --output tsv)
az ad group member add --group $groupid --member-id $msiobjectid
az ad group member list -g $groupid
```

If you want to see the full JSON output for each command, drop the parameters `--query objectId --output tsv`.

### Grant permissions to Azure AD group

In the Cloud Shell, sign in to SQL Database by using the SQLCMD command. Replace _\<server-name>_ with your SQL Database server name, _\<db-name>_ with the database name your app uses, and _\<aad-user-name>_ and _\<aad-password>_ with your Azure AD user's credentials.

```azurecli-interactive
sqlcmd -S <server-name>.database.windows.net -d <db-name> -U <aad-user-name> -P "<aad-password>" -G -l 30
```

In the SQL prompt for the database you want, run the following commands to add the Azure AD group and grant the permissions your app needs. For example, 

```sql
CREATE USER [myAzureSQLDBAccessGroup] FROM EXTERNAL PROVIDER;
ALTER ROLE db_datareader ADD MEMBER [myAzureSQLDBAccessGroup];
ALTER ROLE db_datawriter ADD MEMBER [myAzureSQLDBAccessGroup];
ALTER ROLE db_ddladmin ADD MEMBER [myAzureSQLDBAccessGroup];
GO
```

Type `EXIT` to return to the Cloud Shell prompt.

### Modify connection string

Remember that the same changes you made in *Web.config* or *appsettings.json* works with the managed identity, so the only thing to do is to remove the existing connection string in App Service, which Visual Studio created deploying your app the first time. Use the following command, but replace *\<app-name>* with the name of your app.

```azurecli-interactive
az webapp config connection-string delete --resource-group myResourceGroup --name <app-name> --setting-names MyDbConnection
```

## Publish your changes

All that's left now is to publish your changes to Azure.

**If you came from [Tutorial: Build an ASP.NET app in Azure with SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md)**, publish your changes in Visual Studio. In the **Solution Explorer**, right-click your **DotNetAppSqlDb** project and select **Publish**.

![Publish from Solution Explorer](./media/app-service-web-tutorial-dotnet-sqldatabase/solution-explorer-publish.png)

In the publish page, click **Publish**. 

**If you came from [Tutorial: Build an ASP.NET Core and SQL Database app in Azure App Service](app-service-web-tutorial-dotnetcore-sqldb.md)**, publish your changes using Git, with the following commands:

```bash
git commit -am "configure managed identity"
git push azure master
```

When the new webpage shows your to-do list, your app is connecting to the database using the managed identity.

![Azure app after Code First Migration](./media/app-service-web-tutorial-dotnet-sqldatabase/this-one-is-done.png)

You should now be able to edit the to-do list as before.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## Next steps

What you learned:

> [!div class="checklist"]
> * Enable managed identities
> * Grant SQL Database access to the managed identity
> * Configure Entity Framework to use Azure AD authentication with SQL Database
> * Connect to SQL Database from Visual Studio using Azure AD authentication

Advance to the next tutorial to learn how to map a custom DNS name to your web app.

> [!div class="nextstepaction"]
> [Map an existing custom DNS name to Azure App Service](app-service-web-tutorial-custom-domain.md)
