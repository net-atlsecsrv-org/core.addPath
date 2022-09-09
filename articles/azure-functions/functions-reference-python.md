---
title: Python developer reference for Azure Functions
description: Understand how to develop functions with Python
ms.topic: article
ms.date: 11/4/2020
ms.custom: devx-track-python
---

# Azure Functions Python developer guide

This article is an introduction to developing Azure Functions using Python. The content below assumes that you've already read the [Azure Functions developers guide](functions-reference.md).

As a Python developer, you may also be interested in one of the following articles:

| Getting started | Concepts| Scenarios/Samples |
| -- | -- | -- | 
| <ul><li>[Python function using Visual Studio Code](./create-first-function-vs-code-csharp.md?pivots=programming-language-python)</li><li>[Python function with terminal/command prompt](./create-first-function-cli-csharp.md?pivots=programming-language-python)</li></ul> | <ul><li>[Developer guide](functions-reference.md)</li><li>[Hosting options](functions-scale.md)</li><li>[Performance&nbsp;considerations](functions-best-practices.md)</li></ul> | <ul><li>[Image classification with PyTorch](machine-learning-pytorch.md)</li><li>[Azure automation sample](/samples/azure-samples/azure-functions-python-list-resource-groups/azure-functions-python-sample-list-resource-groups/)</li><li>[Machine learning with TensorFlow](functions-machine-learning-tensorflow.md)</li><li>[Browse Python samples](/samples/browse/?products=azure-functions&languages=python)</li></ul> |

## Programming model

Azure Functions expects a function to be a stateless method in your Python script that processes input and produces output. By default, the runtime expects the method to be implemented as a global method called `main()` in the `__init__.py` file. You can also [specify an alternate entry point](#alternate-entry-point).

Data from triggers and bindings is bound to the function via method attributes using the `name` property defined in the *function.json* file. For example, the  _function.json_ below describes a simple function triggered by an HTTP request named `req`:

:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json":::

Based on this definition, the `__init__.py` file that contains the function code might look like the following example:

```python
def main(req):
    user = req.params.get('user')
    return f'Hello, {user}!'
```

You can also explicitly declare the attribute types and return type in the function using Python type annotations. This helps you use the intellisense and autocomplete features provided by many Python code editors.

```python
import azure.functions


def main(req: azure.functions.HttpRequest) -> str:
    user = req.params.get('user')
    return f'Hello, {user}!'
```

Use the Python annotations included in the [azure.functions.*](/python/api/azure-functions/azure.functions?view=azure-python&preserve-view=true) package to bind input and outputs to your methods.

## Alternate entry point

You can change the default behavior of a function by optionally specifying the `scriptFile` and `entryPoint` properties in the *function.json* file. For example, the _function.json_ below tells the runtime to use the `customentry()` method in the _main.py_ file, as the entry point for your Azure Function.

```json
{
  "scriptFile": "main.py",
  "entryPoint": "customentry",
  "bindings": [
      ...
  ]
}
```

## Folder structure

The recommended folder structure for a Python Functions project looks like the following example:

```
 <project_root>/
 | - .venv/
 | - .vscode/
 | - my_first_function/
 | | - __init__.py
 | | - function.json
 | | - example.py
 | - my_second_function/
 | | - __init__.py
 | | - function.json
 | - shared_code/
 | | - __init__.py
 | | - my_first_helper_function.py
 | | - my_second_helper_function.py
 | - tests/
 | | - test_my_second_function.py
 | - .funcignore
 | - host.json
 | - local.settings.json
 | - requirements.txt
 | - Dockerfile
```
The main project folder (<project_root>) can contain the following files:

* *local.settings.json*: Used to store app settings and connection strings when running locally. This file doesn't get published to Azure. To learn more, see [local.settings.file](functions-run-local.md#local-settings-file).
* *requirements.txt*: Contains the list of Python packages the system installs when publishing to Azure.
* *host.json*: Contains global configuration options that affect all functions in a function app. This file does get published to Azure. Not all options are supported when running locally. To learn more, see [host.json](functions-host-json.md).
* *.vscode/*: (Optional) Contains store VSCode configuration. To learn more, see [VSCode setting](https://code.visualstudio.com/docs/getstarted/settings).
* *.venv/*: (Optional) Contains a Python virtual environment used by local development.
* *Dockerfile*: (Optional) Used when publishing your project in a [custom container](functions-create-function-linux-custom-image.md).
* *tests/*: (Optional) Contains the test cases of your function app.
* *.funcignore*: (Optional) Declares files that shouldn't get published to Azure. Usually, this file contains `.vscode/` to ignore your editor setting, `.venv/` to ignore local Python virtual environment, `tests/` to ignore test cases, and `local.settings.json` to prevent local app settings being published.

Each function has its own code file and binding configuration file (function.json).

When deploying your project to a function app in Azure, the entire contents of the main project (*<project_root>*) folder should be included in the package, but not the folder itself, which means `host.json` should be in the package root. We recommend that you maintain your tests in a folder along with other functions, in this example `tests/`. For more information, see [Unit Testing](#unit-testing).

## Import behavior

You can import modules in your function code using both absolute and relative references. Based on the folder structure shown above, the following imports work from within the function file *<project_root>\my\_first\_function\\_\_init\_\_.py*:

```python
from shared_code import my_first_helper_function #(absolute)
```

```python
import shared_code.my_second_helper_function #(absolute)
```

```python
from . import example #(relative)
```

> [!NOTE]
>  The *shared_code/* folder needs to contain an \_\_init\_\_.py file to mark it as a Python package when using absolute import syntax.

The following \_\_app\_\_ import and beyond top-level relative import are deprecated, since it is not supported by static type checker and not supported by Python test frameworks:

```python
from __app__.shared_code import my_first_helper_function #(deprecated __app__ import)
```

```python
from ..shared_code import my_first_helper_function #(deprecated beyond top-level relative import)
```

## Triggers and Inputs

Inputs are divided into two categories in Azure Functions: trigger input and additional input. Although they are different in the `function.json` file, usage is identical in Python code.  Connection strings or secrets for trigger and input sources map to values in the `local.settings.json` file when running locally, and the application settings when running in Azure.

For example, the following code demonstrates the difference between the two:

```json
// function.json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "req",
      "direction": "in",
      "type": "httpTrigger",
      "authLevel": "anonymous",
      "route": "items/{id}"
    },
    {
      "name": "obj",
      "direction": "in",
      "type": "blob",
      "path": "samples/{id}",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

```json
// local.settings.json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "python",
    "AzureWebJobsStorage": "<azure-storage-connection-string>"
  }
}
```

```python
# __init__.py
import azure.functions as func
import logging


def main(req: func.HttpRequest,
         obj: func.InputStream):

    logging.info(f'Python HTTP triggered function processed: {obj.read()}')
```

When the function is invoked, the HTTP request is passed to the function as `req`. An entry will be retrieved from the Azure Blob Storage based on the _ID_ in the route URL and made available as `obj` in the function body.  Here, the storage account specified is the connection string found in the AzureWebJobsStorage app setting, which is the same storage account used by the function app.


## Outputs

Output can be expressed both in return value and output parameters. If there's only one output, we recommend using the return value. For multiple outputs, you'll have to use output parameters.

To use the return value of a function as the value of an output binding, the `name` property of the binding should be set to `$return` in `function.json`.

To produce multiple outputs, use the `set()` method provided by the [`azure.functions.Out`](/python/api/azure-functions/azure.functions.out?view=azure-python&preserve-view=true) interface to assign a value to the binding. For example, the following function can push a message to a queue and also return an HTTP response.

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "req",
      "direction": "in",
      "type": "httpTrigger",
      "authLevel": "anonymous"
    },
    {
      "name": "msg",
      "direction": "out",
      "type": "queue",
      "queueName": "outqueue",
      "connection": "AzureWebJobsStorage"
    },
    {
      "name": "$return",
      "direction": "out",
      "type": "http"
    }
  ]
}
```

```python
import azure.functions as func


def main(req: func.HttpRequest,
         msg: func.Out[func.QueueMessage]) -> str:

    message = req.params.get('body')
    msg.set(message)
    return message
```

## Logging

Access to the Azure Functions runtime logger is available via a root [`logging`](https://docs.python.org/3/library/logging.html#module-logging) handler in your function app. This logger is tied to Application Insights and allows you to flag warnings and errors encountered during the function execution.

The following example logs an info message when the function is invoked via an HTTP trigger.

```python
import logging


def main(req):
    logging.info('Python HTTP trigger function processed a request.')
```

Additional logging methods are available that let you write to the console at different trace levels:

| Method                 | Description                                |
| ---------------------- | ------------------------------------------ |
| **`critical(_message_)`**   | Writes a message with level CRITICAL on the root logger.  |
| **`error(_message_)`**   | Writes a message with level ERROR on the root logger.    |
| **`warning(_message_)`**    | Writes a message with level WARNING on the root logger.  |
| **`info(_message_)`**    | Writes a message with level INFO on the root logger.  |
| **`debug(_message_)`** | Writes a message with level DEBUG on the root logger.  |

To learn more about logging, see [Monitor Azure Functions](functions-monitoring.md).

## HTTP Trigger and bindings

The HTTP trigger is defined in the function.json file. The `name` of the binding must match the named parameter in the function.
In the previous examples, a binding name `req` is used. This parameter is an [HttpRequest] object, and an [HttpResponse] object is returned.

From the [HttpRequest] object, you can get request headers, query parameters, route parameters, and the message body.

The following example is from the [HTTP trigger template for Python](https://github.com/Azure/azure-functions-templates/tree/dev/Functions.Templates/Templates/HttpTrigger-Python).

```python
def main(req: func.HttpRequest) -> func.HttpResponse:
    headers = {"my-http-header": "some-value"}

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        return func.HttpResponse(f"Hello {name}!", headers=headers)
    else:
        return func.HttpResponse(
             "Please pass a name on the query string or in the request body",
             headers=headers, status_code=400
        )
```

In this function, the value of the `name` query parameter is obtained from the `params` parameter of the [HttpRequest] object. The JSON-encoded message body is read using the `get_json` method.

Likewise, you can set the `status_code` and `headers` for the response message in the returned [HttpResponse] object.

## Scaling and Performance

It's important to understand how your functions perform and how that performance affects the way your function app gets scaled. This is particularly important when designing highly performant apps. The following are several factors to consider when designing, writing and configuring your functions apps.

### Horizontal scaling
By default, Azure Functions automatically monitors the load on your application and creates additional host instances for Python as needed. Functions uses built-in thresholds for different trigger types to decide when to add instances, such as the age of messages and queue size for QueueTrigger. These thresholds aren't user configurable. For more information, see [How the Consumption and Premium plans work](functions-scale.md#how-the-consumption-and-premium-plans-work).

### Improving throughput performance

A key to improving performance is understanding how your app uses resources and being able to configure your function app accordingly.

#### Understanding your workload

The default configurations are suitable for most of Azure Functions applications. However, you can improve the performance of your applications' throughput by employing configurations based on your workload profile. The first step is to understand the type of workload that you are running.

| | I/O-bound workload | CPU-bound workload |
|--| -- | -- |
|**Function app characteristics**| <ul><li>App needs to handle many concurrent invocations.</li> <li> App processes a large number of I/O events, such as network calls and disk read/writes.</li> </ul>| <ul><li>App does long-running computations, such as image resizing.</li> <li>App does data transformation.</li> </ul> |
|**Examples**| <ul><li>Web APIs</li><ul> | <ul><li>Data processing</li><li> Machine learning inference</li><ul>|


> [!NOTE]
>  As real world functions workload are most of often a mix of I/O and CPU bound, we recommend to profile the workload under realistic production loads.


#### Performance-specific configurations

After understanding the workload profile of your function app, the following are configurations that you can use to improve the throughput performance of your functions.

##### Async

Because [Python is a single-threaded runtime](https://wiki.python.org/moin/GlobalInterpreterLock), a host instance for Python can process only one function invocation at a time. For applications that process a large number of I/O events and/or is I/O bound, you can improve performance significantly by running functions asynchronously.

To run a function asynchronously, use the `async def` statement, which runs the function with [asyncio](https://docs.python.org/3/library/asyncio.html) directly:

```python
async def main():
    await some_nonblocking_socket_io_op()
```
Here is an example of a function with HTTP trigger that uses [aiohttp](https://pypi.org/project/aiohttp/) http client:

```python
import aiohttp

import azure.functions as func

async def main(req: func.HttpRequest) -> func.HttpResponse:
    async with aiohttp.ClientSession() as client:
        async with client.get("PUT_YOUR_URL_HERE") as response:
            return func.HttpResponse(await response.text())

    return func.HttpResponse(body='NotFound', status_code=404)
```


A function without the `async` keyword is run automatically in an asyncio thread-pool:

```python
# Runs in an asyncio thread-pool

def main():
    some_blocking_socket_io()
```

In order to achieve the full benefit of running functions asynchronously, the I/O operation/library that is used in your code needs to have async implemented as well. Using synchronous I/O operations in functions that are defined as asynchronous **may hurt** the overall performance.

Here are a few examples of client libraries that has implemented async pattern:
- [aiohttp](https://pypi.org/project/aiohttp/) - Http client/server for asyncio 
- [Streams API](https://docs.python.org/3/library/asyncio-stream.html) - High-level async/await-ready primitives to work with network connection
- [Janus Queue](https://pypi.org/project/janus/) - Thread-safe asyncio-aware queue for Python
- [pyzmq](https://pypi.org/project/pyzmq/) - Python bindings for ZeroMQ
 

##### Use multiple language worker processes

By default, every Functions host instance has a single language worker process. You can increase the number of worker processes per host (up to 10) by using the [FUNCTIONS_WORKER_PROCESS_COUNT](functions-app-settings.md#functions_worker_process_count) application setting. Azure Functions then tries to evenly distribute simultaneous function invocations across these workers.

For CPU bound apps, you should set the number of language worker to be the same as or higher than the number of cores that are available per function app. To learn more, see [Available instance SKUs](functions-premium-plan.md#available-instance-skus). 

I/O-bound apps may also benefit from increasing the number of worker processes beyond the number of cores available. Keep in mind that setting the number of workers too high can impact overall performance due to the increased number of required context switches. 

The FUNCTIONS_WORKER_PROCESS_COUNT applies to each host that Functions creates when scaling out your application to meet demand.


## Context

To get the invocation context of a function during execution, include the [`context`](/python/api/azure-functions/azure.functions.context?view=azure-python&preserve-view=true) argument in its signature.

For example:

```python
import azure.functions


def main(req: azure.functions.HttpRequest,
         context: azure.functions.Context) -> str:
    return f'{context.invocation_id}'
```

The [**Context**](/python/api/azure-functions/azure.functions.context?view=azure-python&preserve-view=true) class has the following string attributes:

`function_directory`
The directory in which the function is running.

`function_name`
Name of the function.

`invocation_id`
ID of the current function invocation.

## Global variables

It is not guaranteed that the state of your app will be preserved for future executions. However, the Azure Functions runtime often reuses the same process for multiple executions of the same app. In order to cache the results of an expensive computation, declare it as a global variable.

```python
CACHED_DATA = None


def main(req):
    global CACHED_DATA
    if CACHED_DATA is None:
        CACHED_DATA = load_json()

    # ... use CACHED_DATA in code
```

## Environment variables

In Functions, [application settings](functions-app-settings.md), such as service connection strings, are exposed as environment variables during execution. You can access these settings by declaring `import os` and then using, `setting = os.environ["setting-name"]`.

The following example gets the [application setting](functions-how-to-use-azure-function-app-settings.md#settings), with the key named `myAppSetting`:

```python
import logging
import os
import azure.functions as func

def main(req: func.HttpRequest) -> func.HttpResponse:

    # Get the setting named 'myAppSetting'
    my_app_setting_value = os.environ["myAppSetting"]
    logging.info(f'My app setting value:{my_app_setting_value}')
```

For local development, application settings are [maintained in the local.settings.json file](functions-run-local.md#local-settings-file).

## Python version

Azure Functions supports the following Python versions:

| Functions version | Python<sup>*</sup> versions |
| ----- | ----- |
| 3.x | 3.8<br/>3.7<br/>3.6 |
| 2.x | 3.7<br/>3.6 |

<sup>*</sup>Official CPython distributions

To request a specific Python version when you create your function app in Azure, use the `--runtime-version` option of the [`az functionapp create`](/cli/azure/functionapp#az-functionapp-create) command. The Functions runtime version is set by the `--functions-version` option. The Python version is set when the function app is created and can't be changed.

When running locally, the runtime uses the available Python version.

## Package management

When developing locally using the Azure Functions Core Tools or Visual Studio Code, add the names and versions of the required packages to the `requirements.txt` file and install them using `pip`.

For example, the following requirements file and pip command can be used to install the `requests` package from PyPI.

```txt
requests==2.19.1
```

```bash
pip install -r requirements.txt
```

## Publishing to Azure

When you're ready to publish, make sure that all your publicly available dependencies are listed in the requirements.txt file, which is located at the root of your project directory.

Project files and folders that are excluded from publishing, including the virtual environment folder, are listed in the .funcignore file.

There are three build actions supported for publishing your Python project to Azure: remote build, local build, and builds using custom dependencies.

You can also use Azure Pipelines to build your dependencies and publish using continuous delivery (CD). To learn more, see [Continuous delivery by using Azure DevOps](functions-how-to-azure-devops.md).

### Remote build

When using remote build, dependencies restored on the server and native dependencies match the production environment. This results in a smaller deployment package to upload. Use remote build when developing Python apps on Windows. If your project has custom dependencies, you can [use remote build with extra index URL](#remote-build-with-extra-index-url).

Dependencies are obtained remotely based on the contents of the requirements.txt file. [Remote build](functions-deployment-technologies.md#remote-build) is the recommended build method. By default, the Azure Functions Core Tools requests a remote build when you use the following [func azure functionapp publish](functions-run-local.md#publish) command to publish your Python project to Azure.

```bash
func azure functionapp publish <APP_NAME>
```

Remember to replace `<APP_NAME>` with the name of your function app in Azure.

The [Azure Functions Extension for Visual Studio Code](./create-first-function-vs-code-csharp.md#publish-the-project-to-azure) also requests a remote build by default.

### Local build

Dependencies are obtained locally based on the contents of the requirements.txt file. You can prevent doing a remote build by using the following [func azure functionapp publish](functions-run-local.md#publish) command to publish with a local build.

```command
func azure functionapp publish <APP_NAME> --build local
```

Remember to replace `<APP_NAME>` with the name of your function app in Azure.

Using the `--build local` option, project dependencies are read from the requirements.txt file and those dependent packages are downloaded and installed locally. Project files and dependencies are deployed from your local computer to Azure. This results in a larger deployment package being uploaded to Azure. If for some reason, dependencies in your requirements.txt file can't be acquired by Core Tools, you must use the custom dependencies option for publishing.

We don't recommend using local builds when developing locally on Windows.

### Custom dependencies

When your project has dependencies not found in the [Python Package Index](https://pypi.org/), there are two ways to build the project. The build method depends on how you build the project.

#### Remote build with extra index URL

When your packages are available from an accessible custom package index, use a remote build. Before publishing, make sure to [create an app setting](functions-how-to-use-azure-function-app-settings.md#settings) named `PIP_EXTRA_INDEX_URL`. The value for this setting is the URL of your custom package index. Using this setting tells the remote build to run `pip install` using the `--extra-index-url` option. To learn more, see the [Python pip install documentation](https://pip.pypa.io/en/stable/reference/pip_install/#requirements-file-format).

You can also use basic authentication credentials with your extra package index URLs. To learn more, see [Basic authentication credentials](https://pip.pypa.io/en/stable/user_guide/#basic-authentication-credentials) in Python documentation.

#### Install local packages

If your project uses packages not publicly available to our tools, you can make them available to your app by putting them in the \_\_app\_\_/.python_packages directory. Before publishing, run the following command to install the dependencies locally:

```command
pip install  --target="<PROJECT_DIR>/.python_packages/lib/site-packages"  -r requirements.txt
```

When using custom dependencies, you should use the `--no-build` publishing option, since you have already installed the dependencies into the project folder.

```command
func azure functionapp publish <APP_NAME> --no-build
```

Remember to replace `<APP_NAME>` with the name of your function app in Azure.

## Unit Testing

Functions written in Python can be tested like other Python code using standard testing frameworks. For most bindings, it's possible to create a mock input object by creating an instance of an appropriate class from the `azure.functions` package. Since the [`azure.functions`](https://pypi.org/project/azure-functions/) package is not immediately available, be sure to install it via your `requirements.txt` file as described in the [package management](#package-management) section above.

Take *my_second_function* as an example, following is a mock test of an HTTP triggered function:

First we need to create *<project_root>/my_second_function/function.json* file and define this function as an http trigger.

```json
{
  "scriptFile": "__init__.py",
  "entryPoint": "main",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
}
```

Now, we can implement the *my_second_function* and the *shared_code.my_second_helper_function*.

```python
# <project_root>/my_second_function/__init__.py
import azure.functions as func
import logging

# Use absolute import to resolve shared_code modules
from shared_code import my_second_helper_function

# Define an http trigger which accepts ?value=<int> query parameter
# Double the value and return the result in HttpResponse
def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Executing my_second_function.')

    initial_value: int = int(req.params.get('value'))
    doubled_value: int = my_second_helper_function.double(initial_value)

    return func.HttpResponse(
      body=f"{initial_value} * 2 = {doubled_value}",
      status_code=200
    )
```

```python
# <project_root>/shared_code/__init__.py
# Empty __init__.py file marks shared_code folder as a Python package
```

```python
# <project_root>/shared_code/my_second_helper_function.py

def double(value: int) -> int:
  return value * 2
```

We can start writing test cases for our http trigger.

```python
# <project_root>/tests/test_my_second_function.py
import unittest

import azure.functions as func
from my_second_function import main

class TestFunction(unittest.TestCase):
    def test_my_second_function(self):
        # Construct a mock HTTP request.
        req = func.HttpRequest(
            method='GET',
            body=None,
            url='/api/my_second_function',
            params={'value': '21'})

        # Call the function.
        resp = main(req)

        # Check the output.
        self.assertEqual(
            resp.get_body(),
            b'21 * 2 = 42',
        )
```

Inside your `.venv` Python virtual environment, install your favorite Python test framework (e.g. `pip install pytest`). Simply run `pytest tests` to check the test result.

## Temporary files

The `tempfile.gettempdir()` method returns a temporary folder, which on Linux is `/tmp`. Your application can use this directory to store temporary files generated and used by your functions during execution.

> [!IMPORTANT]
> Files written to the temporary directory aren't guaranteed to persist across invocations. During scale out, temporary files aren't shared between instances.

The following example creates a named temporary file in the temporary directory (`/tmp`):

```python
import logging
import azure.functions as func
import tempfile
from os import listdir

#---
   tempFilePath = tempfile.gettempdir()
   fp = tempfile.NamedTemporaryFile()
   fp.write(b'Hello world!')
   filesDirListInTemp = listdir(tempFilePath)
```

We recommend that you maintain your tests in a folder separate from the project folder. This keeps you from deploying test code with your app.

## Preinstalled libraries

There are a few libraries come with the Python Functions runtime.

### Python Standard Library

The Python Standard Library contain a list of built-in Python modules that are shipped with each Python distribution. Most of these libraries help you access system functionality, like file I/O. On Windows systems, these libraries are installed with Python. On the Unix-based systems, they are provided by package collections.

To view the full details of the list of these libraries, please visit the links below:

* [Python 3.6 Standard Library](https://docs.python.org/3.6/library/)
* [Python 3.7 Standard Library](https://docs.python.org/3.7/library/)
* [Python 3.8 Standard Library](https://docs.python.org/3.8/library/)

### Azure Functions Python worker dependencies

The Functions Python worker requires a specific set of libraries. You can also use these libraries in your functions, but they aren't a part of the Python standard. If your functions rely on any of these libraries, they may not be available to your code when running outside of Azure Functions. You can find a detailed list of dependencies in the **install\_requires** section in the [setup.py](https://github.com/Azure/azure-functions-python-worker/blob/dev/setup.py#L282) file.

> [!NOTE]
> If your function app's requirements.txt contains an `azure-functions-worker` entry, remove it. The functions worker is automatically managed by Azure Functions platform, and we regularly update it with new features and bug fixes. Manually installing an old version of worker in requirements.txt may cause unexpected issues.

### Azure Functions Python library

Every Python worker update includes a new version of [Azure Functions Python library (azure.functions)](https://github.com/Azure/azure-functions-python-library). This approach makes it easier to continuously update your Python function apps, because each update is backwards-compatible. A list of releases of this library can be found in [azure-functions PyPi](https://pypi.org/project/azure-functions/#history).

The runtime library version is fixed by Azure, and it can't be overridden by requirements.txt. The `azure-functions` entry in requirements.txt is only for linting and customer awareness.

Use the following code to track the actual version of the Python Functions library in your runtime:

```python
getattr(azure.functions, '__version__', '< 1.2.1')
```

### Runtime system libraries

For a list of preinstalled system libraries in Python worker Docker images, please follow the links below:

|  Functions runtime  | Debian version | Python versions |
|------------|------------|------------|
| Version 2.x | Stretch  | [Python 3.6](https://github.com/Azure/azure-functions-docker/blob/master/host/2.0/stretch/amd64/python/python36/python36.Dockerfile)<br/>[Python 3.7](https://github.com/Azure/azure-functions-docker/blob/master/host/2.0/stretch/amd64/python/python37/python37.Dockerfile) |
| Version 3.x | Buster | [Python 3.6](https://github.com/Azure/azure-functions-docker/blob/master/host/3.0/buster/amd64/python/python36/python36.Dockerfile)<br/>[Python 3.7](https://github.com/Azure/azure-functions-docker/blob/master/host/3.0/buster/amd64/python/python37/python37.Dockerfile)<br />[Python 3.8](https://github.com/Azure/azure-functions-docker/blob/master/host/3.0/buster/amd64/python/python38/python38.Dockerfile) |

## Cross-origin resource sharing

[!INCLUDE [functions-cors](../../includes/functions-cors.md)]

CORS is fully supported for Python function apps.

## Known issues and FAQ

Following is a list of troubleshooting guides for common issues:

* [ModuleNotFoundError and ImportError](recover-python-functions.md#troubleshoot-modulenotfounderror)
* [Cannot import 'cygrpc'](recover-python-functions.md#troubleshoot-cannot-import-cygrpc)

All known issues and feature requests are tracked using [GitHub issues](https://github.com/Azure/azure-functions-python-worker/issues) list. If you run into a problem and can't find the issue in GitHub, open a new issue and include a detailed description of the problem.

## Next steps

For more information, see the following resources:

* [Azure Functions package API documentation](/python/api/azure-functions/azure.functions?view=azure-python&preserve-view=true)
* [Best practices for Azure Functions](functions-best-practices.md)
* [Azure Functions triggers and bindings](functions-triggers-bindings.md)
* [Blob storage bindings](functions-bindings-storage-blob.md)
* [HTTP and Webhook bindings](functions-bindings-http-webhook.md)
* [Queue storage bindings](functions-bindings-storage-queue.md)
* [Timer trigger](functions-bindings-timer.md)

[Having issues? Let us know.](https://aka.ms/python-functions-ref-survey)


[HttpRequest]: /python/api/azure-functions/azure.functions.httprequest?view=azure-python&preserve-view=true
[HttpResponse]: /python/api/azure-functions/azure.functions.httpresponse?view=azure-python&preserve-view=true