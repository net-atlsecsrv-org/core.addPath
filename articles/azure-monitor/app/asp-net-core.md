---
title: Azure Application Insights for ASP.NET Core applications | Microsoft Docs
description: Monitor ASP.NET Core web applications for availability, performance, and usage.
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 04/30/2020

---

# Application Insights for ASP.NET Core applications

This article describes how to enable Application Insights for an [ASP.NET Core](/aspnet/core) application. When you complete the instructions in this article, Application Insights will collect requests, dependencies, exceptions, performance counters, heartbeats, and logs from your ASP.NET Core application.

The example we'll use here is an [MVC application](/aspnet/core/tutorials/first-mvc-app) that targets `netcoreapp3.0`. You can apply these instructions to all ASP.NET Core applications. If you are using the [Worker Service](/aspnet/core/fundamentals/host/hosted-services#worker-service-template), use the instructions from [here](./worker-service.md).

## Supported scenarios

The [Application Insights SDK for ASP.NET Core](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) can monitor your applications no matter where or how they run. If your application is running and has network connectivity to Azure, telemetry can be collected. Application Insights monitoring is supported everywhere .NET Core is supported. Support covers:
* **Operating system**: Windows, Linux, or Mac.
* **Hosting method**: In process or out of process.
* **Deployment method**: Framework dependent or self-contained.
* **Web server**: IIS (Internet Information Server) or Kestrel.
* **Hosting platform**: The Web Apps feature of Azure App Service, Azure VM, Docker, Azure Kubernetes Service (AKS), and so on.
* **.NET Core version**: All officially [supported](https://dotnet.microsoft.com/download/dotnet-core) .NET Core versions.
* **IDE**: Visual Studio, VS Code, or command line.

> [!NOTE]
> ASP.NET Core 3.X requires [Application Insights 2.8.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/2.8.0) or later.

## Prerequisites

- A functioning ASP.NET Core application. If you need to create an ASP.NET Core application, follow this [ASP.NET Core tutorial](/aspnet/core/getting-started/).
- A valid Application Insights instrumentation key. This key is required to send any telemetry to Application Insights. If you need to create a new Application Insights resource to get an instrumentation key, see [Create an Application Insights resource](./create-new-resource.md).

> [!IMPORTANT]
> New Azure regions **require** the use of connection strings instead of instrumentation keys. [Connection string](./sdk-connection-string.md?tabs=net) identifies the resource that you want to associate your telemetry data with. It also allows you to modify the endpoints your resource will use as a destination for your telemetry. You will need to copy the connection string and add it to your application's code or to an environment variable.


## Enable Application Insights server-side telemetry (Visual Studio)

For Visual Studio for Mac use the [manual guidance](#enable-application-insights-server-side-telemetry-no-visual-studio). Only the Windows version of Visual Studio supports this procedure.

1. Open your project in Visual Studio.

    > [!TIP]
    > If you want to, you can set up source control for your project so you can track all the changes that Application Insights makes. To enable source control, select **File** > **Add to Source Control**.

2. Select **Project** > **Add Application Insights Telemetry**.

3. Select **Get Started**. This selection's text might vary, depending on your version of Visual Studio. Some earlier versions use a **Start Free** button instead.

4. Select your subscription. Then select **Resource** > **Register**.

5. After adding Application Insights to your project, check to confirm that you're using the latest stable release of the SDK. Go to **Project** > **Manage NuGet Packages** > **Microsoft.ApplicationInsights.AspNetCore**. If you need to, choose **Update**.

     ![Screenshot showing where to select the Application Insights package for update](./media/asp-net-core/update-nuget-package.png)

6. If you followed the optional tip and added your project to source control, go to **View** > **Team Explorer** > **Changes**. Then select each file to see a diff view of the changes made by Application Insights telemetry.

## Enable Application Insights server-side telemetry (no Visual Studio)

1. Install the [Application Insights SDK NuGet package for ASP.NET Core](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore). We recommend that you always use the latest stable version. Find full release notes for the SDK on the [open-source GitHub repo](https://github.com/Microsoft/ApplicationInsights-dotnet/releases).

    The following code sample shows the changes to be added to your project's `.csproj` file.

    ```xml
        <ItemGroup>
          <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.13.1" />
        </ItemGroup>
    ```

2. Add `services.AddApplicationInsightsTelemetry();` to the `ConfigureServices()` method in your `Startup` class, as in this example:

    ```csharp
        // This method gets called by the runtime. Use this method to add services to the container.
        public void ConfigureServices(IServiceCollection services)
        {
            // The following line enables Application Insights telemetry collection.
            services.AddApplicationInsightsTelemetry();
    
            // This code adds other services for your application.
            services.AddMvc();
        }
    ```

3. Set up the instrumentation key.

    Although you can provide the instrumentation key as an argument to `AddApplicationInsightsTelemetry`, we recommend that you specify the instrumentation key in configuration. The following code sample shows how to specify an instrumentation key in `appsettings.json`. Make sure `appsettings.json` is copied to the application root folder during publishing.

    ```json
        {
          "ApplicationInsights": {
            "InstrumentationKey": "putinstrumentationkeyhere"
          },
          "Logging": {
            "LogLevel": {
              "Default": "Warning"
            }
          }
        }
    ```

    Alternatively, specify the instrumentation key in either of the following environment variables:

    * `APPINSIGHTS_INSTRUMENTATIONKEY`

    * `ApplicationInsights:InstrumentationKey`

    For example:

    * `SET ApplicationInsights:InstrumentationKey=putinstrumentationkeyhere`

    * `SET APPINSIGHTS_INSTRUMENTATIONKEY=putinstrumentationkeyhere`

    * `APPINSIGHTS_INSTRUMENTATIONKEY` is typically used in [Azure Web Apps](./azure-web-apps.md?tabs=net), but can also be used in all places where this SDK is supported. (If you are doing codeless web app monitoring, this format is required if you aren't using connection strings.)

    In lieu of setting instrumentation keys you can now also use [Connection Strings](./sdk-connection-string.md?tabs=net).

    > [!NOTE]
    > An instrumentation key specified in code wins over the environment variable `APPINSIGHTS_INSTRUMENTATIONKEY`, which wins over other options.

### User secrets and other configuration providers

If you want to store the instrumentation key in ASP.NET Core user secrets or retrieve it from another configuration provider, you can use the overload with a `Microsoft.Extensions.Configuration.IConfiguration` parameter. For example, `services.AddApplicationInsightsTelemetry(Configuration);`.
Starting from Microsoft.ApplicationInsights.AspNetCore version [2.15.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), calling `services.AddApplicationInsightsTelemetry()` will automatically read the instrumentation key from `Microsoft.Extensions.Configuration.IConfiguration` of the application. There is no need to explicitly provide the `IConfiguration`.

## Run your application

Run your application and make requests to it. Telemetry should now flow to Application Insights. The Application Insights SDK automatically collects incoming web requests to your application, along with the following telemetry as well.

### Live Metrics

[Live Metrics](./live-stream.md) can be used to quickly verify if Application Insights monitoring is configured correctly. While it might take a few minutes before telemetry starts appearing in the portal and analytics, Live Metrics would show CPU usage of the running process in near real-time. It can also show other telemetry like Requests, Dependencies, Traces, etc.

### ILogger logs

The default configuration collects `ILogger` logs of severity `Warning` and above. This configuration can be [customized](#how-do-i-customize-ilogger-logs-collection).

### Dependencies

Dependency collection is enabled by default. [This](asp-net-dependencies.md#automatically-tracked-dependencies) article explains the dependencies that are automatically collected, and also contain steps to do manual tracking.

### Performance counters

Support for [performance counters](./performance-counters.md) in ASP.NET Core is limited:

* SDK versions 2.4.1 and later collect performance counters if the application is running in Azure Web Apps (Windows).
* SDK versions 2.7.1 and later collect performance counters if the application is running in Windows and targets `NETSTANDARD2.0` or later.
* For applications targeting the .NET Framework, all versions of the SDK support performance counters.
* SDK Versions 2.8.0 and later support cpu/memory counter in Linux. No other counter is supported in Linux. The recommended way to get system counters in Linux (and other non-Windows environments) is by using [EventCounters](#eventcounter)

### EventCounter

`EventCounterCollectionModule` is enabled by default. The [EventCounter](eventcounters.md) tutorial has instructions on configuring the list of counters to be collected.

## Enable client-side telemetry for web applications

The preceding steps are enough to help you start collecting server-side telemetry. If your application has client-side components, follow the next steps to start collecting [usage telemetry](./usage-overview.md).

1. In `_ViewImports.cshtml`, add injection:

```cshtml
    @inject Microsoft.ApplicationInsights.AspNetCore.JavaScriptSnippet JavaScriptSnippet
```

2. In `_Layout.cshtml`, insert `HtmlHelper` at the end of the `<head>` section but before any other script. If you want to report any custom JavaScript telemetry from the page, inject it after this snippet:

```cshtml
    @Html.Raw(JavaScriptSnippet.FullScript)
    </head>
```

Alternatively to using the `FullScript` the `ScriptBody` is available starting in SDK v2.14. Use this if you need to control the `<script>` tag to set a Content Security Policy:

```cshtml
 <script> // apply custom changes to this script tag.
     @Html.Raw(JavaScriptSnippet.ScriptBody)
 </script>
```

The `.cshtml` file names referenced earlier are from a default MVC application template. Ultimately, if you want to properly enable client-side monitoring for your application, the JavaScript snippet must appear in the `<head>` section of each page of your application that you want to monitor. You can accomplish this goal for this application template by adding the JavaScript snippet to `_Layout.cshtml`. 

If your project doesn't include `_Layout.cshtml`, you can still add [client-side monitoring](./website-monitoring.md). You can do this by adding the JavaScript snippet to an equivalent file that controls the `<head>` of all pages within your app. Or you can add the snippet to multiple pages, but this solution is difficult to maintain and we generally don't recommend it.

## Configure the Application Insights SDK

You can customize the Application Insights SDK for ASP.NET Core to change the default configuration. Users of the Application Insights ASP.NET SDK might be familiar with changing configuration by using `ApplicationInsights.config` or by modifying `TelemetryConfiguration.Active`. For ASP.NET Core, almost all configuration changes are done in the `ConfigureServices()` method of your `Startup.cs` class, unless you're directed otherwise. The following sections offer more information.

> [!NOTE]
> In ASP.NET Core applications, changing configuration by modifying `TelemetryConfiguration.Active` isn't supported.

### Using ApplicationInsightsServiceOptions

You can modify a few common settings by passing `ApplicationInsightsServiceOptions` to `AddApplicationInsightsTelemetry`, as in this example:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions aiOptions
                = new Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions();
    // Disables adaptive sampling.
    aiOptions.EnableAdaptiveSampling = false;

    // Disables QuickPulse (Live Metrics stream).
    aiOptions.EnableQuickPulseMetricStream = false;
    services.AddApplicationInsightsTelemetry(aiOptions);
}
```

Full List of settings in `ApplicationInsightsServiceOptions`

|Setting | Description | Default
|---------------|-------|-------
|EnablePerformanceCounterCollectionModule  | Enable/Disable `PerformanceCounterCollectionModule` | true
|EnableRequestTrackingTelemetryModule   | Enable/Disable `RequestTrackingTelemetryModule` | true
|EnableEventCounterCollectionModule   | Enable/Disable `EventCounterCollectionModule` | true
|EnableDependencyTrackingTelemetryModule   | Enable/Disable `DependencyTrackingTelemetryModule` | true
|EnableAppServicesHeartbeatTelemetryModule  |  Enable/Disable `AppServicesHeartbeatTelemetryModule` | true
|EnableAzureInstanceMetadataTelemetryModule   |  Enable/Disable `AzureInstanceMetadataTelemetryModule` | true
|EnableQuickPulseMetricStream | Enable/Disable LiveMetrics feature | true
|EnableAdaptiveSampling | Enable/Disable Adaptive Sampling | true
|EnableHeartbeat | Enable/Disable Heartbeats feature, which periodically (15-min default) sends a custom metric named 'HeartbeatState' with information about the runtime like .NET Version, Azure Environment information, if applicable, etc. | true
|AddAutoCollectedMetricExtractor | Enable/Disable AutoCollectedMetrics extractor, which is a TelemetryProcessor that sends pre-aggregated metrics about Requests/Dependencies before sampling takes place. | true
|RequestCollectionOptions.TrackExceptions | Enable/Disable reporting of unhandled Exception tracking by the Request collection module. | false in NETSTANDARD2.0 (because Exceptions are tracked with ApplicationInsightsLoggerProvider), true otherwise.
|EnableDiagnosticsTelemetryModule | Enable/Disable `DiagnosticsTelemetryModule`. Disabling this will cause the following settings to be ignored; `EnableHeartbeat`, `EnableAzureInstanceMetadataTelemetryModule`, `EnableAppServicesHeartbeatTelemetryModule` | true

See the [configurable settings in `ApplicationInsightsServiceOptions`](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/NETCORE/src/Shared/Extensions/ApplicationInsightsServiceOptions.cs) for the most up-to-date list.

### Configuration Recommendation for Microsoft.ApplicationInsights.AspNetCore SDK 2.15.0 & above

Starting from Microsoft.ApplicationInsights.AspNetCore SDK version [2.15.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/2.15.0) the recommendation is to configure every setting available in `ApplicationInsightsServiceOptions`, including instrumentationkey using applications `IConfiguration` instance. The settings must be under the section "ApplicationInsights", as shown in the below example. The following section from appsettings.json configures instrumentation key, and also disable adaptive sampling and performance counter collection.

```json
{
    "ApplicationInsights": {
    "InstrumentationKey": "putinstrumentationkeyhere",
    "EnableAdaptiveSampling": false,
    "EnablePerformanceCounterCollectionModule": false
    }
}
```

If `services.AddApplicationInsightsTelemetry(aiOptions)` is used, it overrides the settings from `Microsoft.Extensions.Configuration.IConfiguration`.

### Sampling

The Application Insights SDK for ASP.NET Core supports both fixed-rate and adaptive sampling. Adaptive sampling is enabled by default.

For more information, see [Configure adaptive sampling for ASP.NET Core applications](./sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications).

### Adding TelemetryInitializers

Use [telemetry initializers](./api-filtering-sampling.md#addmodify-properties-itelemetryinitializer) when you want to enrich telemetry with additional information.

Add any new `TelemetryInitializer` to the `DependencyInjection` container as shown in the following code. The SDK automatically picks up any `TelemetryInitializer` that's added to the `DependencyInjection` container.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();
}
```

> [!NOTE]
> `services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();` works for simple initializers. For others, the following is required: `services.AddSingleton(new MyCustomTelemetryInitializer() { fieldName = "myfieldName" });`
    
### Removing TelemetryInitializers

Telemetry initializers are present by default. To remove all or specific telemetry initializers, use the following sample code *after* you call `AddApplicationInsightsTelemetry()`.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddApplicationInsightsTelemetry();

    // Remove a specific built-in telemetry initializer
    var tiToRemove = services.FirstOrDefault<ServiceDescriptor>
                        (t => t.ImplementationType == typeof(AspNetCoreEnvironmentTelemetryInitializer));
    if (tiToRemove != null)
    {
        services.Remove(tiToRemove);
    }

    // Remove all initializers
    // This requires importing namespace by using Microsoft.Extensions.DependencyInjection.Extensions;
    services.RemoveAll(typeof(ITelemetryInitializer));
}
```

### Adding telemetry processors

You can add custom telemetry processors to `TelemetryConfiguration` by using the extension method `AddApplicationInsightsTelemetryProcessor` on `IServiceCollection`. You use telemetry processors in [advanced filtering scenarios](./api-filtering-sampling.md#itelemetryprocessor-and-itelemetryinitializer). Use the following example.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ...
    services.AddApplicationInsightsTelemetry();
    services.AddApplicationInsightsTelemetryProcessor<MyFirstCustomTelemetryProcessor>();

    // If you have more processors:
    services.AddApplicationInsightsTelemetryProcessor<MySecondCustomTelemetryProcessor>();
}
```

### Configuring or removing default TelemetryModules

Application Insights uses telemetry modules to automatically collect useful telemetry about specific workloads without requiring manual tracking by user.

The following automatic-collection modules are enabled by default. These modules are responsible for automatically collecting telemetry. You can disable or configure them to alter their default behavior.

* `RequestTrackingTelemetryModule` - Collects RequestTelemetry from incoming web requests.
* `DependencyTrackingTelemetryModule` - Collects [DependencyTelemetry](./asp-net-dependencies.md) from outgoing http calls and sql calls.
* `PerformanceCollectorModule` - Collects Windows PerformanceCounters.
* `QuickPulseTelemetryModule` - Collects telemetry for showing in Live Metrics portal.
* `AppServicesHeartbeatTelemetryModule` - Collects heart beats (which are sent as custom metrics), about Azure App Service environment where application is hosted.
* `AzureInstanceMetadataTelemetryModule` -  Collects heart beats (which are sent as custom metrics), about Azure VM environment where application is hosted.
* `EventCounterCollectionModule` -  Collects [EventCounters.](eventcounters.md) This module is a new feature and is available in SDK Version 2.8.0 and higher.

To configure any default `TelemetryModule`, use the extension method `ConfigureTelemetryModule<T>` on `IServiceCollection`, as shown in the following example.

```csharp
using Microsoft.ApplicationInsights.DependencyCollector;
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector;

public void ConfigureServices(IServiceCollection services)
{
    services.AddApplicationInsightsTelemetry();

    // The following configures DependencyTrackingTelemetryModule.
    // Similarly, any other default modules can be configured.
    services.ConfigureTelemetryModule<DependencyTrackingTelemetryModule>((module, o) =>
            {
                module.EnableW3CHeadersInjection = true;
            });

    // The following removes all default counters from EventCounterCollectionModule, and adds a single one.
    services.ConfigureTelemetryModule<EventCounterCollectionModule>(
            (module, o) =>
            {
                module.Counters.Add(new EventCounterCollectionRequest("System.Runtime", "gen-0-size"));
            }
        );

    // The following removes PerformanceCollectorModule to disable perf-counter collection.
    // Similarly, any other default modules can be removed.
    var performanceCounterService = services.FirstOrDefault<ServiceDescriptor>(t => t.ImplementationType == typeof(PerformanceCollectorModule));
    if (performanceCounterService != null)
    {
        services.Remove(performanceCounterService);
    }
}
```

Starting with 2.12.2 version, [`ApplicationInsightsServiceOptions`](#using-applicationinsightsserviceoptions) contains easy
option to disable any of the default modules.

### Configuring a telemetry channel

The default [telemetry channel](./telemetry-channels.md) is `ServerTelemetryChannel`. You can override it as the following example shows.

```csharp
using Microsoft.ApplicationInsights.Channel;

    public void ConfigureServices(IServiceCollection services)
    {
        // Use the following to replace the default channel with InMemoryChannel.
        // This can also be applied to ServerTelemetryChannel.
        services.AddSingleton(typeof(ITelemetryChannel), new InMemoryChannel() {MaxTelemetryBufferCapacity = 19898 });

        services.AddApplicationInsightsTelemetry();
    }
```

### Disable telemetry dynamically

If you want to disable telemetry conditionally and dynamically, you may resolve `TelemetryConfiguration` instance with ASP.NET Core dependency injection container anywhere in your code and set `DisableTelemetry` flag on it.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env, TelemetryConfiguration configuration)
    {
        configuration.DisableTelemetry = true;
        ...
    }
```

The above does not prevent any auto collection modules from collecting telemetry. Only the sending of telemetry to Application Insights gets disabled with the above approach. If a particular auto collection module is not desired, it is best to [remove the telemetry module](#configuring-or-removing-default-telemetrymodules)

## Frequently asked questions

### Does Application Insights support ASP.NET Core 3.X?

Yes. Update to [Application Insights SDK for ASP.NET Core](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) version 2.8.0 or higher. Older versions of the SDK do not support ASP.NET Core 3.X.

Also, if you are using Visual Studio based instructions from [here](#enable-application-insights-server-side-telemetry-visual-studio), update to the latest version of Visual Studio 2019 (16.3.0) to onboard. Previous versions of Visual Studio do not support automatic onboarding for ASP.NET Core 3.X apps.

### How can I track telemetry that's not automatically collected?

Get an instance of `TelemetryClient` by using constructor injection, and call the required `TrackXXX()` method on it. We don't recommend creating new `TelemetryClient` or `TelemetryConfiguration` instances in an ASP.NET Core application. A singleton instance of `TelemetryClient` is already registered in the `DependencyInjection` container, which shares `TelemetryConfiguration` with rest of the telemetry. Creating a new `TelemetryClient` instance is recommended only if it needs a configuration that's separate from the rest of the telemetry.

The following example shows how to track additional telemetry from a controller.

```csharp
using Microsoft.ApplicationInsights;

public class HomeController : Controller
{
    private TelemetryClient telemetry;

    // Use constructor injection to get a TelemetryClient instance.
    public HomeController(TelemetryClient telemetry)
    {
        this.telemetry = telemetry;
    }

    public IActionResult Index()
    {
        // Call the required TrackXXX method.
        this.telemetry.TrackEvent("HomePageRequested");
        return View();
    }
```

For more information about custom data reporting in Application Insights, see [Application Insights custom metrics API reference](./api-custom-events-metrics.md). A similar approach can be used for sending custom metrics to Application Insights using the [GetMetric API](./get-metric.md).

### How do I customize ILogger logs collection?

By default, only logs of severity `Warning` and above are automatically captured. To change this behavior, explicitly override the logging configuration for the provider `ApplicationInsights` as shown below.
The following configuration allows ApplicationInsights to capture all logs of severity `Information` and above.

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    },
    "ApplicationInsights": {
      "LogLevel": {
        "Default": "Information"
      }
    }
  }
}
```

It is important to note that the following will not cause ApplicationInsights provider to capture `Information` logs. This is because SDK adds a default logging filter, instructing `ApplicationInsights` to capture only `Warning` and above. Because of this, an explicit override is required for ApplicationInsights.

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information"
    }
  }
}
```

Read more about [ILogger configuration](ilogger.md#control-logging-level).

### Some Visual Studio templates used the UseApplicationInsights() extension method on IWebHostBuilder to enable Application Insights. Is this usage still valid?

While the extension method `UseApplicationInsights()` is still supported, it is marked obsolete in Application Insights SDK version 2.8.0 onwards. It will be removed in the next major version of the SDK. The recommended way to enable Application Insights telemetry is by using `AddApplicationInsightsTelemetry()` because it provides overloads to control some configuration. Also, in ASP.NET Core 3.X apps, `services.AddApplicationInsightsTelemetry()` is the only way to enable application insights.

### I'm deploying my ASP.NET Core application to Web Apps. Should I still enable the Application Insights extension from Web Apps?

If the SDK is installed at build time as shown in this article, you don't need to enable the [Application Insights extension](./azure-web-apps.md) from the App Service portal. Even if the extension is installed, it will back off when it detects that the SDK is already added to the application. If you enable Application Insights from the extension, you don't have to install and update the SDK. But if you enable Application Insights by following instructions in this article, you have more flexibility because:

   * Application Insights telemetry will continue to work in:
       * All operating systems, including Windows, Linux, and Mac.
       * All publish modes, including self-contained or framework dependent.
       * All target frameworks, including the full .NET Framework.
       * All hosting options, including Web Apps, VMs, Linux, containers, Azure Kubernetes Service, and non-Azure hosting.
       * All .NET Core versions including preview versions.
   * You can see telemetry locally when you're debugging from Visual Studio.
   * You can track additional custom telemetry by using the `TrackXXX()` API.
   * You have full control over the configuration.

### Can I enable Application Insights monitoring by using tools like Status Monitor?

No. [Status Monitor](./monitor-performance-live-website-now.md) and [Status Monitor v2](./status-monitor-v2-overview.md) currently support ASP.NET 4.x only.

### If I run my application in Linux, are all features supported?

Yes. Feature support for the SDK is the same in all platforms, with the following exceptions:

* The SDK collects [Event Counters](./eventcounters.md) on Linux because [Performance Counters](./performance-counters.md) are only supported in Windows. Most metrics are the same.
* Even though `ServerTelemetryChannel` is enabled by default, if the application is running in Linux or macOS, the channel doesn't automatically create a local storage folder to keep telemetry temporarily if there are network issues. Because of this limitation, telemetry is lost when there are temporary network or server issues. To work around this issue, configure a local folder for the channel:

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;

    public void ConfigureServices(IServiceCollection services)
    {
        // The following will configure the channel to use the given folder to temporarily
        // store telemetry items during network or Application Insights server issues.
        // User should ensure that the given folder already exists
        // and that the application has read/write permissions.
        services.AddSingleton(typeof(ITelemetryChannel),
                                new ServerTelemetryChannel () {StorageFolder = "/tmp/myfolder"});
        services.AddApplicationInsightsTelemetry();
    }
```

This limitation is not applicable from [2.15.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/2.15.0) and newer versions.

### Is this SDK supported for the new .NET Core 3.X Worker Service template applications?

This SDK requires `HttpContext`, and hence does not work in any non-HTTP applications, including the .NET Core 3.X Worker Service applications. Refer to [this](worker-service.md) document for enabling application insights in such applications, using the newly released Microsoft.ApplicationInsights.WorkerService SDK.

## Open-source SDK

* [Read and contribute to the code](https://github.com/microsoft/ApplicationInsights-dotnet).

For the latest updates and bug fixes [consult the release notes](./release-notes.md).

## Next steps

* [Explore user flows](./usage-flows.md) to understand how users navigate through your app.
* [Configure a snapshot collection](./snapshot-debugger.md) to see the state of source code and variables at the moment an exception is thrown.
* [Use the API](./api-custom-events-metrics.md) to send your own events and metrics for a detailed view of your app's performance and usage.
* Use [availability tests](./monitor-web-app-availability.md) to check your app constantly from around the world.
* [Dependency Injection in ASP.NET Core](/aspnet/core/fundamentals/dependency-injection)
