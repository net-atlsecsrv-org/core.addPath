---
title: 'Quickstart: Create a Linux Python app'
description: Get started with Linux apps on Azure App Service by deploying your first Python app to a Linux container in App Service.
ms.topic: quickstart
ms.date: 06/30/2020
ms.custom: seo-python-october2019, cli-validate, tracking-python

---

# Quickstart: Create a Python app in Azure App Service on Linux

In this quickstart, you deploy a Python web app to [App Service on Linux](app-service-linux-intro.md), Azure's highly scalable, self-patching web hosting service. You use the local [Azure command-line interface (CLI)](/cli/azure/install-azure-cli) on a Mac, Linux, or Windows computer. The web app you configure uses a free App Service tier, so you incur no costs in the course of this article.

If you prefer to deploy apps through an IDE, see [Deploy Python apps to App Service from Visual Studio Code](/azure/developer/python/tutorial-deploy-app-service-on-linux-01).

## Set up your initial environment

Before you begin, you must have the following:

1. Have an Azure account with an active subscription. [Create an account for free](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
1. Install <a href="https://www.python.org/downloads/" target="_blank">Python 3.6 or higher</a>.
1. Install the <a href="/cli/azure/install-azure-cli" target="_blank">Azure CLI</a> 2.0.80 or higher, with which you run commands in any shell to provision and configure Azure resources.

Open a terminal window and check your Python version is 3.6 or higher:

# [Bash](#tab/bash)

```bash
python3 --version
```

# [PowerShell](#tab/powershell)

```cmd
py -3 --version
```

# [Cmd](#tab/cmd)

```cmd
py -3 --version
```

---

Check that your Azure CLI version is 2.0.80 or higher:

```azurecli
az --version
```

Then sign in to Azure through the CLI:

```azurecli
az login
```

This command opens a browser to gather your credentials. When the command finishes, it shows JSON output containing information about your subscriptions.

Once signed in, you can run Azure commands with the Azure CLI to work with resources in your subscription.

## Clone the sample

Clone the sample repository with the following command. ([Install git](https://git-scm.com/downloads) if you don't have git already.)

```terminal
git clone https://github.com/Azure-Samples/python-docs-hello-world
```

Then go into that folder:

```terminal
cd python-docs-hello-world
```

The sample code contains an *application.py* file, which tells App Service that the code contains a Flask app. For more information, see [Container startup process and customizations](how-to-configure-python.md).

## Run the sample

# [Bash](#tab/bash)

First create a virtual environment and install dependencies:

```bash
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
```

Then set the `FLASK_APP` environment variable to the app's entry module and run the Flask development server:

```
export FLASK_APP=application.py
flask run
```

# [PowerShell](#tab/powershell)

First create a virtual environment and install dependencies:

```powershell
py -3 -m venv env
env\scripts\activate
pip install -r requirements.txt
```

Then set the `FLASK_APP` environment variable to the app's entry module and run the Flask development server:

```powershell
Set-Item Env:FLASK_APP ".\application.py"
flask run
```

# [Cmd](#tab/cmd)

First create a virtual environment and install dependencies:

```cmd
py -3 -m venv env
env\scripts\activate
pip install -r requirements.txt
```

Then set the `FLASK_APP` environment variable to the app's entry module and run the Flask development server:

```cmd
SET FLASK_APP=application.py
flask run
```

---

Open a web browser, and go to the sample app at `http://localhost:5000/`. The app displays the message **Hello World!**.

![Run a sample Python app locally](./media/quickstart-python/run-hello-world-sample-python-app-in-browser-localhost.png)

In your terminal window, press **Ctrl**+**C** to exit the Flask development server.

## Deploy the sample

Deploy the code in your local folder (*python-docs-hello-world*) using the `az webapp up` command:

```azurecli
az webapp up --sku F1 -n <app-name>
```

- If the `az` command is not recognized, be sure you have the Azure CLI installed as described in [Set up your initial environment](#set-up-your-initial-environment).
- Replace `<app_name>` with a name that's unique across all of Azure (*valid characters are `a-z`, `0-9`, and `-`*). A good pattern is to use a combination of your company name and an app identifier.
- The `--sku F1` argument creates the web app on the Free pricing tier. Omit this argument to use a faster premium tier, which incurs an hourly cost.
- You can optionally include the argument `-l <location-name>` where `<location_name>` is an Azure region such as **centralus**, **eastasia**, **westeurope**, **koreasouth**, **brazilsouth**, **centralindia**, and so on. You can retrieve a list of allowable regions for your Azure account by running the [`az account list-locations`](/cli/azure/appservice?view=azure-cli-latest.md#az-appservice-list-locations) command.

The command may take a few minutes to complete. While running, it provides messages about creating the resource group, the App Service plan and hosting app, configuring logging, then performing ZIP deployment. It then gives the message, "You can launch the app at http://&lt;app-name&gt;.azurewebsites.net", which is the app's URL on Azure.

![Example output of the az webapp up command](./media/quickstart-python/az-webapp-up-output.png)

[!INCLUDE [AZ Webapp Up Note](../../../includes/app-service-web-az-webapp-up-note.md)]

## Browse to the app

Browse to the deployed application in your web browser at the URL `http://<app-name>.azurewebsites.net`.

The Python sample code is running a Linux container in App Service using a built-in image.

![Run a sample Python app in Azure](./media/quickstart-python/run-hello-world-sample-python-app-in-browser.png)

**Congratulations!** You've deployed your Python app to App Service on Linux.

## Redeploy updates

In your favorite code editor, open *application.py* and update the `hello` function as follows. This change adds a `print` statement to generate logging output that you work with in the next section. 

```python
def hello():
    print("Handling request to home page.")
    return "Hello Azure!"
```

Save your changes and exit the editor. 

Redeploy the app using the `az webapp up` command again:

```azurecli
az webapp up
```

This command uses values that are cached locally in the *.azure/config* file, including the app name, resource group, and App Service plan.

Once deployment is complete, switch back to the browser window open to `http://<app-name>.azurewebsites.net`. Refresh the page, which should display the modified message:

![Run an updated sample Python app in Azure](./media/quickstart-python/run-updated-hello-world-sample-python-app-in-browser.png)

> [!TIP]
> Visual Studio Code provides powerful extensions for Python and Azure App Service, which simplify the process of deploying Python web apps to App Service. For more information, see [Deploy Python apps to App Service from Visual Studio Code](/azure/python/tutorial-deploy-app-service-on-linux-01).

## Stream logs

You can access the console logs generated from inside the app and the container in which it runs. Logs include any output generated using `print` statements.

To stream logs, run the following command:

```azurecli
az webapp log tail
```

Refresh the app in the browser to generate console logs, which include messages describing HTTP requests to the app. If no output appears immediately, try again in 30 seconds.

You can also inspect the log files from the browser at `https://<app-name>.scm.azurewebsites.net/api/logs/docker`.

To stop log streaming at any time, type **Ctrl**+**C**.

## Manage the Azure app

Go to the <a href="https://portal.azure.com" target="_blank">Azure portal</a> to manage the app you created. Search for and select **App Services**.

![Navigate to App Services in the Azure portal](./media/quickstart-python/navigate-to-app-services-in-the-azure-portal.png)

Select the name of your Azure app.

![Navigate to your Python app in App Services in the Azure portal](./media/quickstart-python/navigate-to-app-in-app-services-in-the-azure-portal.png)

Selecting the app opens its **Overview** page, where you can perform basic management tasks like browse, stop, start, restart, and delete.

![Manage your Python app in the Overview page in the Azure portal](./media/quickstart-python/manage-an-app-in-app-services-in-the-azure-portal.png)

The App Service menu provides different pages for configuring your app.

## Clean up resources

In the preceding steps, you created Azure resources in a resource group. The resource group has a name like "appsvc_rg_Linux_CentralUS" depending on your location. If you use an App Service SKU other than the free F1 tier, these resources incur ongoing costs (see [App Service pricing](https://azure.microsoft.com/pricing/details/app-service/linux/)).

If you don't expect to need these resources in the future, delete the resource group by running the following command:

```azurecli
az group delete
```

The command uses the resource group name cached in the *.azure/config* file.

The command may take a minute to complete.

## Next steps

> [!div class="nextstepaction"]
> [Tutorial: Python (Django) web app with PostgreSQL](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [Add user sign-in to a Python web app](../../active-directory/develop/quickstart-v2-python-webapp.md)

> [!div class="nextstepaction"]
> [Configure Python app](how-to-configure-python.md)

> [!div class="nextstepaction"]
> [Tutorial: Run Python app in custom container](tutorial-custom-docker-image.md)
