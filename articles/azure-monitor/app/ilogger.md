---
title: Explore .NET trace logs in Azure Application Insights with ILogger
description: Samples of using the Azure Application Insights ILogger provider with ASP.NET Core and Console applications.
services: application-insights
author: cijothomas
manager: carmonm
ms.service: application-insights
ms.topic: conceptual
ms.date: 02/19/2019
ms.reviewer: mbullwin
ms.author: cithomas
---

# ApplicationInsightsLoggerProvider for .NET Core ILogger logs

ASP.NET Core supports a logging API that works with different kinds of built-in and third-party logging providers. Logging is done by calling **Log()** or a variant of it on *ILogger* instances. This article demonstrates how to use *ApplicationInsightsLoggerProvider* to capture ILogger logs in  console and ASP.NET Core applications. This article also describes how ApplicationInsightsLoggerProvider integrates with other Application Insights telemetry.
To learn more, see [Logging in ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging).

## ASP.NET Core applications

ApplicationInsightsLoggerProvider is enabled by default in [Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) version 2.7.0-beta3 (and later) when you turn on regular Application Insights monitoring through either of the standard methods:
- By calling the **UseApplicationInsights** extension method on IWebHostBuilder 
- By calling the **AddApplicationInsightsTelemetry** extension method on IServiceCollection

ILogger logs that ApplicationInsightsLoggerProvider captures are subject to the same configuration as any other telemetry that's collected. They have the same set of TelemetryInitializers and TelemetryProcessors, use the same TelemetryChannel, and are correlated and sampled in the same way as other telemetry. If you use version 2.7.0-beta3 or later, no action is required to capture ILogger logs.

Only *Warning* or higher ILogger logs (from all categories) are sent to Application Insights by default. But you can [apply filters to modify this behavior](#control-logging-level). Additional steps are required to capture ILogger logs from **Program.cs** or **Startup.cs**. (See [Capturing ILogger logs from Startup.cs and Program.cs in ASP.NET Core applications](#capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps).)

If you use an earlier version of Microsoft.ApplicationInsights.AspNet SDK or you want to just use ApplicationInsightsLoggerProvider without any other Application Insights monitoring, use the following procedure:

1. Install the NuGet package:

   ```xml
       <ItemGroup>
         <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.9.1" />  
       </ItemGroup>
   ```

1. Modify **Program.cs** as shown here:

   ```csharp
   using Microsoft.AspNetCore;
   using Microsoft.AspNetCore.Hosting;
   using Microsoft.Extensions.Logging;

   public class Program
   {
       public static void Main(string[] args)
       {
           CreateWebHostBuilder(args).Build().Run();
       }

       public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
         WebHost.CreateDefaultBuilder(args)
           .UseStartup<Startup>()
         .ConfigureLogging(
               builder =>
               {
                   // Providing an instrumentation key here is required if you're using
                   // standalone package Microsoft.Extensions.Logging.ApplicationInsights
                   // or if you want to capture logs from early in the application startup
                   // pipeline from Startup.cs or Program.cs itself.
                   builder.AddApplicationInsights("ikey");

                   // Optional: Apply filters to control what logs are sent to Application Insights.
                   // The following configures LogLevel Information or above to be sent to
                   // Application Insights for all categories.
                   builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                                    ("", LogLevel.Information);
               }
           );
   }
   ```

The code in step 2 configures `ApplicationInsightsLoggerProvider`. The following code shows an example Controller class, which uses `ILogger` to send logs. The logs are captured by Application Insights.

```csharp
public class ValuesController : ControllerBase
{
    private readonly `ILogger` _logger;

    public ValuesController(ILogger<ValuesController> logger)
    {
        _logger = logger;
    }

    // GET api/values
    [HttpGet]
    public ActionResult<IEnumerable<string>> Get()
    {
        // All the following logs will be picked up by Application Insights.
        // and all of them will have ("MyKey", "MyValue") in Properties.
        using (_logger.BeginScope(new Dictionary<string, object> { { "MyKey", "MyValue" } }))
            {
                _logger.LogWarning("An example of a Warning trace..");
                _logger.LogError("An example of an Error level message");
            }
        return new string[] { "value1", "value2" };
    }
}
```

### Capture ILogger logs from Startup.cs and Program.cs in ASP.NET Core apps

> [!NOTE]
> In ASP.NET Core 3.0 and later, it is no longer possible to inject `ILogger` in Startup.cs and Program.cs. See https://github.com/aspnet/Announcements/issues/353 for more details.

The new ApplicationInsightsLoggerProvider can capture logs from early in the application-startup pipeline. Although ApplicationInsightsLoggerProvider is automatically enabled in  Application Insights (starting with version 2.7.0-beta3), it doesn't have an instrumentation key set up until later in the pipeline. So, only logs from **Controller**/other classes will be captured. To capture every log starting with **Program.cs** and **Startup.cs** itself, you must explicitly enable an instrumentation key for ApplicationInsightsLoggerProvider. Also, *TelemetryConfiguration* is not fully set up when you log from **Program.cs** or **Startup.cs** itself. So those logs will have a minimum configuration that uses InMemoryChannel, no sampling, and no standard telemetry initializers or processors.

The following examples demonstrate this capability with **Program.cs** and **Startup.cs**.

#### Example Program.cs

```csharp
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Logging;

public class Program
{
    public static void Main(string[] args)
    {
        var host = CreateWebHostBuilder(args).Build();
        var logger = host.Services.GetRequiredService<ILogger<Program>>();
        // This will be picked up by AI
        logger.LogInformation("From Program. Running the host now..");
        host.Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureLogging(
        builder =>
            {
            // Providing an instrumentation key here is required if you're using
            // standalone package Microsoft.Extensions.Logging.ApplicationInsights
            // or if you want to capture logs from early in the application startup 
            // pipeline from Startup.cs or Program.cs itself.
            builder.AddApplicationInsights("ikey");

            // Adding the filter below to ensure logs of all severity from Program.cs
            // is sent to ApplicationInsights.
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             (typeof(Program).FullName, LogLevel.Trace);

            // Adding the filter below to ensure logs of all severity from Startup.cs
            // is sent to ApplicationInsights.
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             (typeof(Startup).FullName, LogLevel.Trace);
            }
        );
}
```

#### Example Startup.cs

```csharp
public class Startup
{
    private readonly `ILogger` _logger;

    public Startup(IConfiguration configuration, ILogger<Startup> logger)
    {
        Configuration = configuration;
        _logger = logger;
    }

    public IConfiguration Configuration { get; }

    // This method gets called by the runtime. Use this method to add services to the container.
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();

        // The following will be picked up by Application Insights.
        _logger.LogInformation("Logging from ConfigureServices.");
    }

    // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        if (env.IsDevelopment())
        {
            // The following will be picked up by Application Insights.
            _logger.LogInformation("Configuring for Development environment");
            app.UseDeveloperExceptionPage();
        }
        else
        {
            // The following will be picked up by Application Insights.
            _logger.LogInformation("Configuring for Production environment");
        }

        app.UseMvc();
    }
}
```

## Migrate from the old ApplicationInsightsLoggerProvider

Microsoft.ApplicationInsights.AspNet SDK versions before 2.7.0-beta2 supported a logging provider that's now obsolete. This provider was enabled through the **AddApplicationInsights()** extension method of ILoggerFactory. We recommend that you migrate to the new provider, which involves two steps:

1. Remove the *ILoggerFactory.AddApplicationInsights()* call from the **Startup.Configure()** method to avoid double logging.
2. Reapply any filtering rules in code, because they will not be respected by the new provider. Overloads of *ILoggerFactory.AddApplicationInsights()* took minimum LogLevel or filter functions. With the new provider, filtering is part of the logging framework itself. It's not done by the Application Insights provider. So any filters that are provided via *ILoggerFactory.AddApplicationInsights()* overloads should be removed. And filtering rules should be provided by following the [Control logging level](#control-logging-level) instructions. If you use *appsettings.json* to filter logging, it will continue to work with the new provider, because both use the same provider alias, *ApplicationInsights*.

You can still use the old provider. (It will be removed only in a major version change to 3.*xx*.) But we recommend that you migrate to the new provider for the following reasons:

- The previous provider lacks support for [log scopes](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-scopes). In the new provider, properties from scope are automatically added as custom properties to the collected telemetry.
- Logs can now be captured much earlier in the application startup pipeline. Logs from the **Program** and **Startup** classes can now be captured.
- With the new provider, filtering is done at the framework level itself. You can filter logs to the Application Insights provider in the same way as for other providers, including built-in providers like Console, Debug, and so on. You can also apply the same filters to multiple providers.
- In ASP.NET Core (2.0 and later), the recommended way to  [enable logging providers](https://github.com/aspnet/Announcements/issues/255) is by using extension methods on ILoggingBuilder in **Program.cs** itself.

> [!Note]
> The new provider is available for applications that target NETSTANDARD2.0 or later. If your application targets older .NET Core versions, such as .NET Core 1.1, or if it targets the .NET Framework, continue to use the old provider.

## Console application

The following code shows a sample console application that's configured to send ILogger traces to Application Insights.

Packages installed:

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.Extensions.DependencyInjection" Version="2.1.0" />  
  <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.9.1" />
  <PackageReference Include="Microsoft.Extensions.Logging.Console" Version="2.1.0" />
</ItemGroup>
```

```csharp
class Program
{
    static void Main(string[] args)
    {
        // Create the DI container.
        IServiceCollection services = new ServiceCollection();

        // Channel is explicitly configured to do flush on it later.
        var channel = new InMemoryChannel();
        services.Configure<TelemetryConfiguration>(
            (config) =>
            {
                config.TelemetryChannel = channel;
            }
        );

        // Add the logging pipelines to use. We are using Application Insights only here.
        services.AddLogging(builder =>
        {
            // Optional: Apply filters to configure LogLevel Trace or above is sent to
            // Application Insights for all categories.
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             ("", LogLevel.Trace);
            builder.AddApplicationInsights("--YourAIKeyHere--");
        });

        // Build ServiceProvider.
        IServiceProvider serviceProvider = services.BuildServiceProvider();

        ILogger<Program> logger = serviceProvider.GetRequiredService<ILogger<Program>>();

        // Begin a new scope. This is optional.
        using (logger.BeginScope(new Dictionary<string, object> { { "Method", nameof(Main) } }))
        {
            logger.LogInformation("Logger is working"); // this will be captured by Application Insights.
        }

        // Explicitly call Flush() followed by sleep is required in Console Apps.
        // This is to ensure that even if application terminates, telemetry is sent to the back-end.
        channel.Flush();
        Thread.Sleep(1000);
    }
}
```

This example uses the standalone package `Microsoft.Extensions.Logging.ApplicationInsights`. By default, this configuration uses the "bare minimum" TelemetryConfiguration for sending data to
Application Insights. Bare minimum means that InMemoryChannel is the channel that's used. There's no sampling and no standard TelemetryInitializers. This behavior can be overridden for a console application, as the following example shows.

Install this additional package:

```xml
<PackageReference Include="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel" Version="2.9.1" />
```

The following section shows how to override the default TelemetryConfiguration by using the **services.Configure\<TelemetryConfiguration>()** method. This example sets up `ServerTelemetryChannel` and sampling. It adds a custom ITelemetryInitializer to the TelemetryConfiguration.

```csharp
    // Create the DI container.
    IServiceCollection services = new ServiceCollection();
    var serverChannel = new ServerTelemetryChannel();
    services.Configure<TelemetryConfiguration>(
        (config) =>
        {
            config.TelemetryChannel = serverChannel;
            config.TelemetryInitializers.Add(new MyTelemetryInitalizer());
            config.DefaultTelemetrySink.TelemetryProcessorChainBuilder.UseSampling(5);
            serverChannel.Initialize(config);
        }
    );

    // Add the logging pipelines to use. We are adding Application Insights only.
    services.AddLogging(loggingBuilder =>
    {
        loggingBuilder.AddApplicationInsights();
    });

    ........
    ........

    // Explicitly calling Flush() followed by sleep is required in Console Apps.
    // This is to ensure that even if the application terminates, telemetry is sent to the back end.
    serverChannel.Flush();
    Thread.Sleep(1000);
```

## Control logging level

The ASP.NET Core *ILogger* infra has a built-in mechanism to apply [log filtering](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-filtering). This lets you control the logs that are sent to each registered provider, including the Application Insights provider. The filtering can be done either in configuration (typically by using an *appsettings.json* file) or in code. This facility is provided by the framework itself. It's not specific to the Application Insights provider.

The following examples apply filter rules to ApplicationInsightsLoggerProvider.

### Create filter rules in configuration with appsettings.json

For ApplicationInsightsLoggerProvider, the provider alias is `ApplicationInsights`. The following section of *appsettings.json* configures logs for *Warning* and above from all categories and *Error* and above from categories that start with "Microsoft" to be sent to `ApplicationInsightsLoggerProvider`.

```json
{
  "Logging": {
    "ApplicationInsights": {
      "LogLevel": {
        "Default": "Warning",
        "Microsoft": "Error"
      }
    },
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

### Create filter rules in code

The following code snippet configures logs for *Warning* and above from all categories and for *Error* and above from categories that start with "Microsoft" to be sent to `ApplicationInsightsLoggerProvider`. This configuration is the same as in the previous section in *appsettings.json*.

```csharp
    WebHost.CreateDefaultBuilder(args)
    .UseStartup<Startup>()
    .ConfigureLogging(logging =>
      logging.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                        ("", LogLevel.Warning)
             .AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                        ("Microsoft", LogLevel.Error);
```

## Frequently asked questions

### What are the old and new versions of ApplicationInsightsLoggerProvider?

[Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) included a built-in ApplicationInsightsLoggerProvider (Microsoft.ApplicationInsights.AspNetCore.Logging.ApplicationInsightsLoggerProvider), which was enabled through **ILoggerFactory** extension methods. This provider is marked obsolete from version 2.7.0-beta2. It will be removed completely in the next major version change. The [Microsoft.ApplicationInsights.AspNetCore 2.6.1](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) package itself isn't obsolete. It's required to enable monitoring of requests, dependencies, and so on.

The suggested alternative is the new standalone package [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights), which contains an improved ApplicationInsightsLoggerProvider (Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider) and extension methods on ILoggerBuilder for enabling it.

[Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) version 2.7.0-beta3 takes a dependency on the new package and enables ILogger capture automatically.

### Why are some ILogger logs shown twice in Application Insights?

Duplication can occur if you have the older (now obsolete) version of ApplicationInsightsLoggerProvider enabled by calling `AddApplicationInsights` on `ILoggerFactory`. Check if your **Configure** method has
  the following, and remove it:

```csharp
 public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
 {
     loggerFactory.AddApplicationInsights(app.ApplicationServices, LogLevel.Warning);
     // ..other code.
 }
```

If you experience double logging when you debug from Visual Studio, set `EnableDebugLogger` to  *false* in the code that enables Application Insights, as follows. This duplication and fix is only relevant when you're debugging the application.

```csharp
 public void ConfigureServices(IServiceCollection services)
 {
     ApplicationInsightsServiceOptions options = new ApplicationInsightsServiceOptions();
     options.EnableDebugLogger = false;
     services.AddApplicationInsightsTelemetry(options);
     // ..other code.
 }
```

### I updated to [Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) version 2.7.0-beta3, and logs from ILogger are captured automatically. How do I turn off this feature completely?

See the [Control logging level](../../azure-monitor/app/ilogger.md#control-logging-level) section to see how to filter logs in general. To turn-off ApplicationInsightsLoggerProvider, use `LogLevel.None`:

**In code:**

```csharp
    builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                      ("", LogLevel.None);
```

**In config:**

```json
{
  "Logging": {
    "ApplicationInsights": {
      "LogLevel": {
        "Default": "None"
      }
}
```

### Why do some ILogger logs not have the same properties as others?

Application Insights captures and sends ILogger logs by using the same TelemetryConfiguration that's used for every other telemetry. But there's an exception. By default, TelemetryConfiguration is not fully set up when you log from **Program.cs** or **Startup.cs**. Logs from these places won't have the default configuration, so they won't be running all TelemetryInitializers and TelemetryProcessors.

### I'm using the standalone package Microsoft.Extensions.Logging.ApplicationInsights, and I want to log some additional custom telemetry manually. How should I do that?

When you use the standalone package, `TelemetryClient` is not injected to the DI container, so you need to create a new instance of `TelemetryClient` and use the same configuration as the logger provider uses, as the following code shows. This ensures that the same configuration is used for all custom telemetry as well as telemetry from ILogger.

```csharp
public class MyController : ApiController
{
   // This telemtryclient can be used to track additional telemetry using TrackXXX() api.
   private readonly TelemetryClient _telemetryClient;
   private readonly ILogger _logger;

   public MyController(IOptions<TelemetryConfiguration> options, ILogger<MyController> logger)
   {
        _telemetryClient = new TelemetryClient(options.Value);
        _logger = logger;
   }  
}
```

> [!NOTE]
> If you use the Microsoft.ApplicationInsights.AspNetCore package to enable Application Insights, modify this code to get `TelemetryClient` directly in the constructor. For an example, see [this FAQ](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core#frequently-asked-questions).


### What Application Insights telemetry type is produced from ILogger logs? Or where can I see ILogger logs in Application Insights?

ApplicationInsightsLoggerProvider captures ILogger logs and creates TraceTelemetry from them. If an Exception object is passed to the **Log()** method on ILogger, *ExceptionTelemetry* is created instead of TraceTelemetry. These telemetry items can be found in same places as any other TraceTelemetry or ExceptionTelemetry for Application Insights, including portal, analytics, or Visual Studio local debugger.

If you prefer to always send TraceTelemetry, use this snippet: ```builder.AddApplicationInsights((opt) => opt.TrackExceptionsAsExceptionTelemetry = false);```

### I don't have the SDK installed, and I use the Azure Web Apps extension to enable Application Insights for my ASP.NET Core applications. How do I use the new provider? 

The Application Insights extension in Azure Web Apps uses the old provider. You can modify the filtering rules in the *appsettings.json* file for your application. To take advantage of the new provider, use build-time instrumentation by taking a NuGet dependency on the SDK. This article will be updated when the extension switches to use the new provider.

### I'm using the standalone package Microsoft.Extensions.Logging.ApplicationInsights and enabling Application Insights provider by calling **builder.AddApplicationInsights("ikey")**. Is there an option to get an instrumentation key from configuration?


Modify Program.cs and appsettings.json as follows:

   ```csharp
   public class Program
   {
       public static void Main(string[] args)
       {
           CreateWebHostBuilder(args).Build().Run();
       }

       public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
           WebHost.CreateDefaultBuilder(args)
               .UseStartup<Startup>()
               .ConfigureLogging((hostingContext, logging) =>
               {
                   // hostingContext.HostingEnvironment can be used to determine environments as well.
                   var appInsightKey = hostingContext.Configuration["myikeyfromconfig"];
                   logging.AddApplicationInsights(appInsightKey);
               });
   }
   ```

   Relevant section from `appsettings.json`:

   ```json
   {
     "myikeyfromconfig": "putrealikeyhere"
   }
   ```

This code is required only when you use a standalone logging provider. For regular Application Insights monitoring, the instrumentation key is loaded automatically from the configuration path *ApplicationInsights: Instrumentationkey*. Appsettings.json should look like this:

   ```json
   {
     "ApplicationInsights":
       {
           "Instrumentationkey":"putrealikeyhere"
       }
   }
   ```

## Next steps

Learn more about:

* [Logging in ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging)
* [.NET trace logs in Application Insights](../../azure-monitor/app/asp-net-trace-logs.md)
