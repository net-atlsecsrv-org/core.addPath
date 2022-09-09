---
title: Use dependency injection in .NET Azure Functions
description: Learn how to use dependency injection for registering and using services in .NET functions
author: craigshoemaker

ms.topic: reference
ms.date: 09/05/2019
ms.author: cshoe
ms.reviewer: jehollan
---
# Use dependency injection in .NET Azure Functions

Azure Functions supports the dependency injection (DI) software design pattern, which is a technique to achieve [Inversion of Control (IoC)](https://docs.microsoft.com/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.

- Dependency injection in Azure Functions is built on the .NET Core Dependency Injection features. Familiarity with the [.NET Core dependency injection](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) is recommended. There are differences, however, in how you override dependencies and how configuration values are read with Azure Functions on the Consumption plan.

- Support for dependency injection begins with Azure Functions 2.x.

## Prerequisites

Before you can use dependency injection, you must install the following NuGet packages:

- [Microsoft.Azure.Functions.Extensions](https://www.nuget.org/packages/Microsoft.Azure.Functions.Extensions/)

- [Microsoft.NET.Sdk.Functions package](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/) version 1.0.28 or later

## Register services

To register services, create a method to configure and add components to an `IFunctionsHostBuilder` instance.  The Azure Functions host creates an instance of `IFunctionsHostBuilder` and passes it directly into your method.

To register the method, add the `FunctionsStartup` assembly attribute that specifies the type name used during startup.

```csharp
using System;
using Microsoft.Azure.Functions.Extensions.DependencyInjection;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Http;
using Microsoft.Extensions.Logging;

[assembly: FunctionsStartup(typeof(MyNamespace.Startup))]

namespace MyNamespace
{
    public class Startup : FunctionsStartup
    {
        public override void Configure(IFunctionsHostBuilder builder)
        {
            builder.Services.AddHttpClient();

            builder.Services.AddSingleton((s) => {
                return new MyService();
            });

            builder.Services.AddSingleton<ILoggerProvider, MyLoggerProvider>();
        }
    }
}
```

### Caveats

A series of registration steps run before and after the runtime processes the startup class. Therefore, the keep in mind the following items:

- *The startup class is meant for only setup and registration.* Avoid using services registered at startup during the startup process. For instance, don't try to log a message in a logger that is being registered during startup. This point of the registration process is too early for your services to be available for use. After the `Configure` method is run, the Functions runtime continues to register additional dependencies, which can affect how your services operate.

- *The dependency injection container only holds explicitly registered types*. The only services available as injectable types are what are setup in the `Configure` method. As a result, Functions-specific types like `BindingContext` and `ExecutionContext` aren't available during setup or as injectable types.

## Use injected dependencies

Constructor injection is used to make your dependencies available in a function. The use of constructor injection requires that you do not use static classes.

The following sample demonstrates how the `IMyService` and `HttpClient` dependencies are injected into an HTTP-triggered function. This example uses the [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) package required to register an `HttpClient` at startup.

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;

namespace MyNamespace
{
    public class HttpTrigger
    {
        private readonly IMyService _service;
        private readonly HttpClient _client;

        public HttpTrigger(IMyService service, IHttpClientFactory httpClientFactory)
        {
            _service = service;
            _client = httpClientFactory.CreateClient();
        }

        [FunctionName("GetPosts")]
        public async Task<IActionResult> Get(
            [HttpTrigger(AuthorizationLevel.Function, "get", Route = "posts")] HttpRequest req,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            var res = await _client.GetAsync("https://microsoft.com");
            await _service.AddResponse(res);

            return new OkResult();
        }
    }
}
```

## Service lifetimes

Azure Functions apps provide the same service lifetimes as [ASP.NET Dependency Injection](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection#service-lifetimes). For a Functions app, the different service lifetimes behave as follows:

- **Transient**: Transient services are created upon each request of the service.
- **Scoped**: The scoped service lifetime matches a function execution lifetime. Scoped services are created once per execution. Later requests for that service during the execution reuse the existing service instance.
- **Singleton**: The singleton service lifetime matches the host lifetime and is reused across function executions on that instance. Singleton lifetime services are recommended for connections and clients, for example `SqlConnection` or `HttpClient` instances.

View or download a [sample of different service lifetimes](https://aka.ms/functions/di-sample) on GitHub.

## Logging services

If you need your own logging provider, register a custom type as an `ILoggerProvider` instance. Application Insights is added by Azure Functions automatically.

> [!WARNING]
> - Do not add `AddApplicationInsightsTelemetry()` to the services collection as it registers services that conflict with services provided by the environment.
> - Do not register your own `TelemetryConfiguration` or `TelemetryClient` if you are using the built-in Application Insights functionality.

## Function app provided services

The function host registers many services. The following services are safe to take as a dependency in your application:

|Service Type|Lifetime|Description|
|--|--|--|
|`Microsoft.Extensions.Configuration.IConfiguration`|Singleton|Runtime configuration|
|`Microsoft.Azure.WebJobs.Host.Executors.IHostIdProvider`|Singleton|Responsible for providing the ID of the host instance|

If there are other services you want to take a dependency on, [create an issue and propose them on GitHub](https://github.com/azure/azure-functions-host).

### Overriding host services

Overriding services provided by the host is currently not supported.  If there are services you want to override, [create an issue and propose them on GitHub](https://github.com/azure/azure-functions-host).

## Working with options and settings

Values defined in [app settings](./functions-how-to-use-azure-function-app-settings.md#settings) are available in an `IConfiguration` instance, which allows you to read app settings values in the startup class.

You can extract values from the `IConfiguration` instance into a custom type. Copying the app settings values to a custom type makes it easy test your services by making these values injectable. Settings read into the configuration instance must be simple key/value pairs.

Consider the following class that includes a property named consistent with an app setting.

```csharp
public class MyOptions
{
    public string MyCustomSetting { get; set; }
}
```

From inside the `Startup.Configure` method, you can extract values from the `IConfiguration` instance into your custom type using the following code:

```csharp
builder.Services.AddOptions<MyOptions>()
                .Configure<IConfiguration>((settings, configuration) =>
                                           {
                                                configuration.Bind(settings);
                                           });
```

Calling `Bind` copies values that have matching property names from the configuration into the custom instance. The options instance is now available in the IoC container to inject into a function.

The options object is injected into the function as an instance of the generic `IOptions` interface. Use the `Value` property to access the values found in your configuration.

```csharp
using System;
using Microsoft.Extensions.Options;

public class HttpTrigger
{
    private readonly MyOptions _settings;

    public HttpTrigger(IOptions<MyOptions> options)
    {
        _settings = options.Value;
    }
}
```

Refer to [Options pattern in ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options) for more details regarding working with options.

> [!WARNING]
> Avoid attempting to read values from files like *local.settings.json* or *appsettings.{environment}.json* on the Consumption plan. Values read from these files related to trigger connections aren't available as the app scales because the hosting infrastructure has no access to the configuration information.

## Next steps

For more information, see the following resources:

- [How to monitor your function app](functions-monitoring.md)
- [Best practices for functions](functions-best-practices.md)
