---
title: 'Tutorial: Linux Ruby app with Postgres' 
description: Learn how to get a Linux Ruby app working in Azure App Service, with connection to a PostgreSQL database in Azure. Rails is used in the tutorial.
ms.devlang: ruby
ms.topic: tutorial
ms.date: 06/18/2020
ms.custom: mvc, cli-validate, seodec18
---

# Build a Ruby and Postgres app in Azure App Service on Linux

[Azure App Service](overview.md) provides a highly scalable, self-patching web hosting service. This tutorial shows how to create a Ruby app and connect it to a PostgreSQL database. When you're finished, you'll have a [Ruby on Rails](https://rubyonrails.org/) app running on App Service on Linux.

:::image type="content" source="./media/tutorial-ruby-postgres-app/complete-checkbox-published.png" alt-text="Screenshot of a Ruby on Rails app example titled Tasks.":::

In this tutorial, you learn how to:

> [!div class="checklist"]
> * Create a PostgreSQL database in Azure
> * Connect a Ruby on Rails app to PostgreSQL
> * Deploy the app to Azure
> * Update the data model and redeploy the app
> * Stream diagnostic logs from Azure
> * Manage the app in the Azure portal

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## Prerequisites

To complete this tutorial:

* [Install Git](https://git-scm.com/)
* [Install Ruby 2.6](https://www.ruby-lang.org/en/documentation/installation/)
* [Install Ruby on Rails 5.1](https://guides.rubyonrails.org/v5.1/getting_started.html)
* [Install and run PostgreSQL](https://www.postgresql.org/download/)

## Prepare local Postgres

In this step, you create a database in your local Postgres server for your use in this tutorial.

### Connect to local Postgres server

Open the terminal window and run `psql` to connect to your local Postgres server.

```bash
sudo -u postgres psql
```

If your connection is successful, your Postgres database is running. If not, make sure that your local Postgres database is started by following the steps at [Downloads - PostgreSQL Core Distribution](https://www.postgresql.org/download/).

Type `\q` to exit the Postgres client. 

Create a Postgres user that can create databases by running the following command, using your signed-in Linux username.

```bash
sudo -u postgres createuser -d <signed-in-user>
```

<a name="step2"></a>

## Create a Ruby on Rails app locally
In this step, you get a Ruby on Rails sample application, configure its database connection, and run it locally. 

### Clone the sample

In the terminal window, `cd` to a working directory.

Run the following command to clone the sample repository.

```bash
git clone https://github.com/Azure-Samples/rubyrails-tasks.git
```

`cd` to your cloned directory. Install the required packages.

```bash
cd rubyrails-tasks
bundle install --path vendor/bundle
```

### Run the sample locally

Run [the Rails migrations](https://guides.rubyonrails.org/active_record_migrations.html#running-migrations) to create the tables the application needs. To see which tables are created in the migrations, look in the _db/migrate_ directory in the Git repository.

```bash
rake db:create
rake db:migrate
```

Run the application.

```bash
rails server
```

Navigate to `http://localhost:3000` in a browser. Add a few tasks in the page.

![Ruby on Rails connects successfully to Postgres](./media/tutorial-ruby-postgres-app/postgres-connect-success.png)

To stop the Rails server, type `Ctrl + C` in the terminal.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## Create Postgres in Azure

In this step, you create a Postgres database in [Azure Database for PostgreSQL](../postgresql/index.yml). Later, you configure the Ruby on Rails application to connect to this database.

### Create a resource group

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-linux-no-h.md)] 

## Create Postgres database in Azure

<!-- > [!NOTE]
> Before you create an Azure Database for PostgreSQL server, check which [compute generation](../postgresql/concepts-pricing-tiers.md#compute-generations-and-vcores) is available in your region. If your region doesn't support Gen4 hardware, change *--sku-name* in the following command line to a value that's supported in your region, such as B_Gen4_1.  -->

In this section, you create an Azure Database for PostgreSQL server and database. To start, install the `db-up` extension with the following command:

```azurecli
az extension add --name db-up
```

Create the Postgres database in Azure with the [`az postgres up`](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up) command, as shown in the following example. Replace *\<postgresql-name>* with a *unique* name (the server endpoint is *https://\<postgresql-name>.postgres.database.azure.com*). For *\<admin-username>* and *\<admin-password>*, specify credentials to create an administrator user for this Postgres server.

<!-- Issue: without --location -->
```azurecli
az postgres up --resource-group myResourceGroup --location westeurope --server-name <postgresql-name> --database-name sampledb --admin-user <admin-username> --admin-password <admin-password> --ssl-enforcement Enabled
```

This command may take a while because it's doing the following:

- Creates a [resource group](../azure-resource-manager/management/overview.md#terminology) called `myResourceGroup`, if it doesn't exist. Every Azure resource needs to be in one of these. `--resource-group` is optional.
- Creates a Postgres server with the administrative user.
- Creates a `sampledb` database.
- Allows access from your local IP address.
- Allows access from Azure services.
- Create a database user with access to the `sampledb` database.

You can do all the steps separately with other `az postgres` commands and `psql`, but `az postgres up` does all of them in one step for you.

When the command finishes, find the output lines that being with `Ran Database Query:`. They show the database user that's created for you, with the username `root` and password `Sampledb1`. You'll use them later to connect your app to the database.

<!-- not all locations support az postgres up -->
> [!TIP]
> `--location <location-name>`, can be set to any one of the [Azure regions](https://azure.microsoft.com/global-infrastructure/regions/). You can get the regions available to your subscription with the [`az account list-locations`](/cli/azure/account#az-account-list-locations) command. For production apps, put your database and your app in the same location.

## Connect app to Azure Postgres

In this step, you connect the Ruby on Rails application to the Postgres database you created in Azure Database for PostgreSQL.

<a name="devconfig"></a>

### Configure the database connection

In the repository, open _config/database.yml_. At the bottom of the file, replace the production variables with the following code. 

```txt
production:
  <<: *default
  host: <%= ENV['DB_HOST'] %>
  database: <%= ENV['DB_DATABASE'] %>
  username: <%= ENV['DB_USERNAME'] %>
  password: <%= ENV['DB_PASSWORD'] %>
```

Save the changes.

### Test the application locally

Back in the local terminal, set the following environment variables:

```bash
export DB_HOST=<postgres-server-name>.postgres.database.azure.com
export DB_DATABASE=sampledb 
export DB_USERNAME=root@<postgres-server-name>
export DB_PASSWORD=Sampledb1
```

Run Rails database migrations with the production values you just configured to create the tables in your Postgres database in Azure Database for PostgreSQL.

```bash
rake db:migrate RAILS_ENV=production
```

When running in the production environment, the Rails application needs precompiled assets. Generate the required assets with the following command:

```bash
rake assets:precompile
```

The Rails production environment also uses secrets to manage security. Generate a secret key.

```bash
rails secret
```

Save the secret key to the respective variables used by the Rails production environment. For convenience, you use the same key for both variables.

```bash
export RAILS_MASTER_KEY=<output-of-rails-secret>
export SECRET_KEY_BASE=<output-of-rails-secret>
```

Enable the Rails production environment to serve JavaScript and CSS files.

```bash
export RAILS_SERVE_STATIC_FILES=true
```

Run the sample application in the production environment.

```bash
rails server -e production
```

Navigate to `http://localhost:3000`. If the page loads without errors, the Ruby on Rails application is connecting to the Postgres database in Azure.

Add a few tasks in the page.

![Ruby on Rails connects successfully to Azure Database for PostgreSQL](./media/tutorial-ruby-postgres-app/azure-postgres-connect-success.png)

To stop the Rails server, type `Ctrl + C` in the terminal.

### Commit your changes

Run the following Git commands to commit your changes:

```bash
git add .
git commit -m "database.yml updates"
```

Your app is ready to be deployed.

## Deploy to Azure

In this step, you deploy the Postgres-connected Rails application to Azure App Service.

### Configure a deployment user

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user-no-h.md)]

### Create an App Service plan

[!INCLUDE [Create app service plan no h](../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### Create a web app

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-ruby-linux-no-h.md)] 

### Configure database settings

In App Service, you set environment variables as _app settings_ by using the [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest&preserve-view=true#az-webapp-config-appsettings-set) command in the Cloud Shell.

The following Cloud Shell command configures the app settings `DB_HOST`, `DB_DATABASE`, `DB_USERNAME`, and `DB_PASSWORD`. Replace the placeholders _&lt;appname>_ and _&lt;postgres-server-name>_.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings DB_HOST="<postgres-server-name>.postgres.database.azure.com" DB_DATABASE="sampledb" DB_USERNAME="root@<postgres-server-name>" DB_PASSWORD="Sampledb1"
```

### Configure Rails environment variables

In the local terminal, [generate a new secret](configure-language-ruby.md#set-secret_key_base-manually) for the Rails production environment in Azure.

```bash
rails secret
```

Configure the variables required by Rails production environment.

In the following Cloud Shell command, replace the two _&lt;output-of-rails-secret>_ placeholders with the new secret key you generated in the local terminal.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings RAILS_MASTER_KEY="<output-of-rails-secret>" SECRET_KEY_BASE="<output-of-rails-secret>" RAILS_SERVE_STATIC_FILES="true" ASSETS_PRECOMPILE="true"
```

`ASSETS_PRECOMPILE="true"` tells the default Ruby container to precompile assets at each Git deployment. For more information, see [Precompile assets](configure-language-ruby.md#precompile-assets) and [Serve static assets](configure-language-ruby.md#serve-static-assets).

### Push to Azure from Git

In the local terminal, add an Azure remote to your local Git repository.

```bash
git remote add azure <paste-copied-url-here>
```

Push to the Azure remote to deploy the Ruby on Rails application. You are prompted for the password you supplied earlier as part of the creation of the deployment user.

```bash
git push azure master
```

During deployment, Azure App Service communicates its progress with Git.

<pre>
Counting objects: 3, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 291 bytes | 0 bytes/s, done.
Total 3 (delta 2), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'a5e076db9c'.
remote: Running custom deployment command...
remote: Running deployment command...
...
&lt; Output has been truncated for readability &gt;
</pre>

### Browse to the Azure app

Browse to `http://<app-name>.azurewebsites.net` and add a few tasks to the list.

:::image type="content" source="./media/tutorial-ruby-postgres-app/ruby-postgres-in-azure.png" alt-text="Screenshot of the Azure app example titled Tasks showing tasks added to the list.":::

Congratulations, you're running a data-driven Ruby on Rails app in Azure App Service.

## Update model locally and redeploy

In this step, you make a simple change to the `task` data model and the webapp, and then publish the update to Azure.

For the tasks scenario, you modify the application so that you can mark a task as complete.

### Add a column

In the terminal, navigate to the root of the Git repository.

Generate a new migration that adds a boolean column called `Done` to the `Tasks` table:

```bash
rails generate migration AddDoneToTasks Done:boolean
```

This command generates a new migration file in the _db/migrate_ directory.


In the terminal, run Rails database migrations to make the change in the local database.

```bash
rake db:migrate
```

### Update application logic

Open the *app/controllers/tasks_controller.rb* file. At the end of the file, find the following line:

```rb
params.require(:task).permit(:Description)
```

Modify this line to include the new `Done` parameter.

```rb
params.require(:task).permit(:Description, :Done)
```

### Update the views

Open the *app/views/tasks/_form.html.erb* file, which is the Edit form.

Find the line `<%=f.error_span(:Description) %>` and insert the following code directly below it:

```erb
<%= f.label :Done, :class => 'control-label col-lg-2' %>
<div class="col-lg-10">
  <%= f.check_box :Done, :class => 'form-control' %>
</div>
```

Open the *app/views/tasks/show.html.erb* file, which is the single-record View page. 

Find the line `<dd><%= @task.Description %></dd>` and insert the following code directly below it:

```erb
  <dt><strong><%= model_class.human_attribute_name(:Done) %>:</strong></dt>
  <dd><%= check_box "task", "Done", {:checked => @task.Done, :disabled => true}%></dd>
```

Open the *app/views/tasks/index.html.erb* file, which is the Index page for all records.

Find the line `<th><%= model_class.human_attribute_name(:Description) %></th>` and insert the following code directly below it:

```erb
<th><%= model_class.human_attribute_name(:Done) %></th>
```

In the same file, find the line `<td><%= task.Description %></td>` and insert the following code directly below it:

```erb
<td><%= check_box "task", "Done", {:checked => task.Done, :disabled => true} %></td>
```

### Test the changes locally

In the local terminal, run the Rails server.

```bash
rails server
```

To see the task status change, navigate to `http://localhost:3000` and add or edit items.

![Added check box to task](./media/tutorial-ruby-postgres-app/complete-checkbox.png)

To stop the Rails server, type `Ctrl + C` in the terminal.

### Publish changes to Azure

In the terminal, run Rails database migrations for the production environment to make the change in the Azure database.

```bash
rake db:migrate RAILS_ENV=production
```

Commit all the changes in Git, and then push the code changes to Azure.

```bash
git add .
git commit -m "added complete checkbox"
git push azure master
```

Once the `git push` is complete, navigate to the Azure app and test the new functionality.

![Model and database changes published to Azure](media/tutorial-ruby-postgres-app/complete-checkbox-published.png)

If you added any tasks, they are retained in the database. Updates to the data schema leave existing data intact.

## Stream diagnostic logs

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

## Manage the Azure app

Go to the [Azure portal](https://portal.azure.com) to manage the app you created.

From the left menu, click **App Services**, and then click the name of your Azure app.

![Portal navigation to Azure app](./media/tutorial-php-mysql-app/access-portal.png)

You see your app's Overview page. Here, you can perform basic management tasks like  stop, start, restart, browse, and delete.

The left menu provides pages for configuring your app.

![App Service page in Azure portal](./media/tutorial-php-mysql-app/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

<a name="next"></a>

## Next steps

In this tutorial, you learned how to:

> [!div class="checklist"]
> * Create a Postgres database in Azure
> * Connect a Ruby on Rails app to Postgres
> * Deploy the app to Azure
> * Update the data model and redeploy the app
> * Stream diagnostic logs from Azure
> * Manage the app in the Azure portal

Advance to the next tutorial to learn how to map a custom DNS name to your app.

> [!div class="nextstepaction"]
> [Tutorial: Map custom DNS name to your app](app-service-web-tutorial-custom-domain.md)

Or, check out other resources:

> [!div class="nextstepaction"]
> [Configure Ruby app](configure-language-ruby.md)