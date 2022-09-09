---
title: Monitor Python applications with Azure Monitor (preview) | Microsoft Docs
description: Provides instructions to wire up OpenCensus Python with Azure Monitor
ms.service:  azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: reyang
ms.author: reyang
ms.date: 10/11/2019

ms.reviewer: mbullwin
---

# Set up Azure Monitor for your Python application (preview)

Azure Monitor supports distributed tracing, metric collection, and logging of Python applications through integration with [OpenCensus](https://opencensus.io). This article will walk you through the process of setting up OpenCensus for Python and sending your monitoring data to Azure Monitor.

## Prerequisites

- An Azure subscription. If you don't have an Azure subscription, create a [free account](https://azure.microsoft.com/free/) before you begin.
- Python installation. This article uses [Python 3.7.0](https://www.python.org/downloads/), though earlier versions will likely work with minor changes.

## Sign in to the Azure portal

Sign in to the [Azure portal](https://portal.azure.com/).

## Create an Application Insights resource in Azure Monitor

First you have to create an Application Insights resource in Azure Monitor, which will generate an instrumentation key (ikey). The ikey is then used to configure the OpenCensus SDK to send telemetry data to Azure Monitor.

1. Select **Create a resource** > **Developer tools** > **Application Insights**.

   ![Adding an Application Insights resource](./media/opencensus-python/0001-create-resource.png)

1. A configuration box appears. Use the following table to fill out the input fields.

   | Setting        | Value           | Description  |
   | ------------- |:-------------|:-----|
   | **Name**      | Globally unique value | Name that identifies the app you're monitoring |
   | **Resource Group**     | myResourceGroup      | Name for the new resource group to host Application Insights data |
   | **Location** | East US | A location near you, or near where your app is hosted |

1. Select **Create**.

## Instrument with OpenCensus Python SDK for Azure Monitor

Install the OpenCensus Azure Monitor exporters:

```console
python -m pip install opencensus-ext-azure
```

For a full list of packages and integrations, see [OpenCensus packages](https://docs.microsoft.com/azure/azure-monitor/app/nuget#common-packages-for-python-using-opencensus).

> [!NOTE]
> The `python -m pip install opencensus-ext-azure` command assumes that you have a `PATH` environment variable set for your Python installation. If you haven't configured this variable, you need to give the full directory path to where your Python executable is located. The result is a command like this: `C:\Users\Administrator\AppData\Local\Programs\Python\Python37-32\python.exe -m pip install opencensus-ext-azure`.

The SDK uses three Azure Monitor exporters to send different types of telemetry to Azure Monitor: trace, metrics, and logs. For more information on these telemetry types, see [the data platform overview](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform). Use the following instructions to send these telemetry types via the three exporters.

## Telemetry type mappings

Here are the exporters that OpenCensus provides mapped to the types of telemetry that you will see in Azure Monitor.

![Screenshot of mapping of telemetry types from OpenCensus to Azure Monitor](./media/opencensus-python/0012-telemetry-types.png)

### Trace

> [!NOTE]
> `Trace` in OpenCensus refers to [distributed tracing](https://docs.microsoft.com/azure/azure-monitor/app/distributed-tracing). The 
`AzureExporter` sends `requests` and `dependency` telemetry to Azure Monitor.

1. First, let's generate some trace data locally. In Python IDLE, or your editor of choice, enter the following code.

    ```python
    from opencensus.trace.samplers import ProbabilitySampler
    from opencensus.trace.tracer import Tracer

    tracer = Tracer(sampler=ProbabilitySampler(1.0))

    def valuePrompt():
        with tracer.span(name="test") as span:
            line = input("Enter a value: ")
            print(line)

    def main():
        while True:
            valuePrompt()

    if __name__ == "__main__":
        main()
    ```

2. Running the code will repeatedly prompt you to enter a value. With each entry, the value will be printed to the shell, and the OpenCensus Python Module will generate a corresponding piece of `SpanData`. The OpenCensus project defines a [trace as a tree of spans](https://opencensus.io/core-concepts/tracing/).
    
    ```
    Enter a value: 4
    4
    [SpanData(name='test', context=SpanContext(trace_id=8aa41bc469f1a705aed1bdb20c342603, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='15ac5123ac1f6847', parent_span_id=None, attributes=BoundedDict({}, maxlen=32), start_time='2019-06-27T18:21:22.805429Z', end_time='2019-06-27T18:21:44.933405Z', child_span_count=0, stack_trace=None, annotations=BoundedList([], maxlen=32), message_events=BoundedList([], maxlen=128), links=BoundedList([], maxlen=32), status=None, same_process_as_parent_span=None, span_kind=0)]
    Enter a value: 25
    25
    [SpanData(name='test', context=SpanContext(trace_id=8aa41bc469f1a705aed1bdb20c342603, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='2e512f846ba342de', parent_span_id=None, attributes=BoundedDict({}, maxlen=32), start_time='2019-06-27T18:21:44.933405Z', end_time='2019-06-27T18:21:46.156787Z', child_span_count=0, stack_trace=None, annotations=BoundedList([], maxlen=32), message_events=BoundedList([], maxlen=128), links=BoundedList([], maxlen=32), status=None, same_process_as_parent_span=None, span_kind=0)]
    Enter a value: 100
    100
    [SpanData(name='test', context=SpanContext(trace_id=8aa41bc469f1a705aed1bdb20c342603, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='f3f9f9ee6db4740a', parent_span_id=None, attributes=BoundedDict({}, maxlen=32), start_time='2019-06-27T18:21:46.157732Z', end_time='2019-06-27T18:21:47.269583Z', child_span_count=0, stack_trace=None, annotations=BoundedList([], maxlen=32), message_events=BoundedList([], maxlen=128), links=BoundedList([], maxlen=32), status=None, same_process_as_parent_span=None, span_kind=0)]
    ```

3. Although entering values is helpful for demonstration purposes, ultimately we want to emit the `SpanData` to Azure Monitor. Modify your code from the previous step based on the following code sample:

    ```python
    from opencensus.ext.azure.trace_exporter import AzureExporter
    from opencensus.trace.samplers import ProbabilitySampler
    from opencensus.trace.tracer import Tracer
    
    # TODO: replace the all-zero GUID with your instrumentation key.
    tracer = Tracer(
        exporter=AzureExporter(
            connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000'),
        sampler=ProbabilitySampler(1.0),
    )

    def valuePrompt():
        with tracer.span(name="test") as span:
            line = input("Enter a value: ")
            print(line)

    def main():
        while True:
            valuePrompt()

    if __name__ == "__main__":
        main()
    ```

4. Now when you run the Python script, you should still be prompted to enter values, but only the value is being printed in the shell. The created `SpanData` will be sent to Azure Monitor. You can find the emitted span data under `dependencies`.

5. For information on sampling in OpenCensus, take a look at [sampling in OpenCensus](https://docs.microsoft.com/azure/azure-monitor/app/sampling#configuring-fixed-rate-sampling-in-opencensus-python).

6. For details on telemetry correlation in your trace data, take a look at OpenCensus [telemetry correlation](https://docs.microsoft.com/azure/azure-monitor/app/correlation#telemetry-correlation-in-opencensus-python).

### Metrics

1. First, let's generate some local metric data. We'll create a simple metric to track the number of times the user presses Enter.

    ```python
    from datetime import datetime
    from opencensus.stats import aggregation as aggregation_module
    from opencensus.stats import measure as measure_module
    from opencensus.stats import stats as stats_module
    from opencensus.stats import view as view_module
    from opencensus.tags import tag_map as tag_map_module

    stats = stats_module.stats
    view_manager = stats.view_manager
    stats_recorder = stats.stats_recorder
    
    prompt_measure = measure_module.MeasureInt("prompts",
                                               "number of prompts",
                                               "prompts")
    prompt_view = view_module.View("prompt view",
                                   "number of prompts",
                                   [],
                                   prompt_measure,
                                   aggregation_module.CountAggregation())
    view_manager.register_view(prompt_view)
    mmap = stats_recorder.new_measurement_map()
    tmap = tag_map_module.TagMap()

    def prompt():
        input("Press enter.")
        mmap.measure_int_put(prompt_measure, 1)
        mmap.record(tmap)
        metrics = list(mmap.measure_to_view_map.get_metrics(datetime.utcnow()))
        print(metrics[0].time_series[0].points[0])

    def main():
        while True:
            prompt()

    if __name__ == "__main__":
        main()
    ```
2. Running the code will repeatedly prompt you to press Enter. A metric is created to track the number of times Enter is pressed. With each entry, the value will be incremented and the metric information will be displayed in the console. The information includes the current value and the current time stamp when the metric was updated.

    ```
    Press enter.
    Point(value=ValueLong(5), timestamp=2019-10-09 20:58:04.930426)
    Press enter.
    Point(value=ValueLong(6), timestamp=2019-10-09 20:58:06.570167)
    Press enter.
    Point(value=ValueLong(7), timestamp=2019-10-09 20:58:07.138614)
    ```

3. Although entering values is helpful for demonstration purposes, ultimately we want to emit the metric data to Azure Monitor. Modify your code from the previous step based on the following code sample:

    ```python
    from datetime import datetime
    from opencensus.ext.azure import metrics_exporter
    from opencensus.stats import aggregation as aggregation_module
    from opencensus.stats import measure as measure_module
    from opencensus.stats import stats as stats_module
    from opencensus.stats import view as view_module
    from opencensus.tags import tag_map as tag_map_module

    stats = stats_module.stats
    view_manager = stats.view_manager
    stats_recorder = stats.stats_recorder
    
    prompt_measure = measure_module.MeasureInt("prompts",
                                               "number of prompts",
                                               "prompts")
    prompt_view = view_module.View("prompt view",
                                   "number of prompts",
                                   [],
                                   prompt_measure,
                                   aggregation_module.CountAggregation())
    view_manager.register_view(prompt_view)
    mmap = stats_recorder.new_measurement_map()
    tmap = tag_map_module.TagMap()

    # TODO: replace the all-zero GUID with your instrumentation key.
    exporter = metrics_exporter.new_metrics_exporter(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')

    view_manager.register_exporter(exporter)

    def prompt():
        input("Press enter.")
        mmap.measure_int_put(prompt_measure, 1)
        mmap.record(tmap)
        metrics = list(mmap.measure_to_view_map.get_metrics(datetime.utcnow()))
        print(metrics[0].time_series[0].points[0])

    def main():
        while True:
            prompt()

    if __name__ == "__main__":
        main()
    ```

4. The exporter will send metric data to Azure Monitor at a fixed interval. The default is every 15 seconds. We're tracking a single metric, so this metric data, with whatever value and time stamp it contains, will be sent every interval. You can find the data under `customMetrics`.

### Logs

1. First, let's generate some local log data.

    ```python
    import logging

    logger = logging.getLogger(__name__)

    def valuePrompt():
        line = input("Enter a value: ")
        logger.warning(line)

    def main():
        while True:
            valuePrompt()

    if __name__ == "__main__":
        main()
    ```

2.  The code will continuously ask for a value to be entered. A log entry is emitted for every entered value.

    ```
    Enter a value: 24
    24
    Enter a value: 55
    55
    Enter a value: 123
    123
    Enter a value: 90
    90
    ```

3. Although entering values is helpful for demonstration purposes, ultimately we want to emit the log data to Azure Monitor. Modify your code from the previous step based on the following code sample:

    ```python
    import logging
    from opencensus.ext.azure.log_exporter import AzureLogHandler
    
    logger = logging.getLogger(__name__)
    
    # TODO: replace the all-zero GUID with your instrumentation key.
    logger.addHandler(AzureLogHandler(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
    )
    
    def valuePrompt():
        line = input("Enter a value: ")
        logger.warning(line)
    
    def main():
        while True:
            valuePrompt()
    
    if __name__ == "__main__":
        main()
    ```

4. The exporter will send log data to Azure Monitor. You can find the data under `traces`. 

> [!NOTE]
> `traces` in this context is not the same as `Tracing`. `traces` refers to the type of telemetry that you will see in Azure Monitor when utilizing the `AzureLogHandler`. `Tracing` refers to a concept in OpenCensus and relates to [distributed tracing](https://docs.microsoft.com/azure/azure-monitor/app/distributed-tracing).

5. To format your log messages, you can use `formatters` in the built-in Python [logging API](https://docs.python.org/3/library/logging.html#formatter-objects).

    ```python
    import logging
    from opencensus.ext.azure.log_exporter import AzureLogHandler
    
    logger = logging.getLogger(__name__)
    
    format_str = '%(asctime)s - %(levelname)-8s - %(message)s'
    date_format = '%Y-%m-%d %H:%M:%S'
    formatter = logging.Formatter(format_str, date_format)
    # TODO: replace the all-zero GUID with your instrumentation key.
    handler = AzureLogHandler(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
    handler.setFormatter(formatter)
    logger.addHandler(handler)
    
    def valuePrompt():
        line = input("Enter a value: ")
        logger.warning(line)
    
    def main():
        while True:
            valuePrompt()
    
    if __name__ == "__main__":
        main()
    ```

6. You can also add custom dimensions to your logs. These will appear as key-value pairs in `customDimensions` in Azure Monitor.
> [!NOTE]
> For this feature to work, you need to pass a dictionary as an argument to your logs, any other data structure will be ignored. To maintain string formatting, store them in a dictionary and pass them as arguments.

    ```python
    import logging
    
    from opencensus.ext.azure.log_exporter import AzureLogHandler
    
    logger = logging.getLogger(__name__)
    # TODO: replace the all-zero GUID with your instrumentation key.
    logger.addHandler(AzureLogHandler(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
    )
    logger.warning('action', {'key-1': 'value-1', 'key-2': 'value2'})
    ```

7. For details on how to enrich your logs with trace context data, see OpenCensus Python [logs integration](https://docs.microsoft.com/azure/azure-monitor/app/correlation#logs-correlation).

## View your data with queries

You can view the telemetry data that was sent from your application through the **Logs (Analytics)** tab.

![Screenshot of the overview pane with "Logs (Analytics)" selected in a red box](./media/opencensus-python/0010-logs-query.png)

In the list under **Active**:

- For telemetry sent with the Azure Monitor trace exporter, incoming requests appear under `requests`. Outgoing or in-process requests appear under `dependencies`.
- For telemetry sent with the Azure Monitor metrics exporter, sent metrics appear under `customMetrics`.
- For telemetry sent with the Azure Monitor logs exporter, logs appear under `traces`. Exceptions appear under `exceptions`.

For more detailed information about how to use queries and logs, see [Logs in Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs).

## Learn more about OpenCensus for Python

* [OpenCensus Python on GitHub](https://github.com/census-instrumentation/opencensus-python)
* [Customization](https://github.com/census-instrumentation/opencensus-python/blob/master/README.rst#customization)
* [Flask integration](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-flask)
* [Django integration](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-django)
* [MySQL integration](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-mysql)
* [PostgreSQL](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-postgresql)

## Next steps

* [Application map](./../../azure-monitor/app/app-map.md)
* [End-to-end performance monitoring](./../../azure-monitor/learn/tutorial-performance.md)

### Alerts

* [Availability tests](../../azure-monitor/app/monitor-web-app-availability.md): Create tests to make sure your site is visible on the web.
* [Smart diagnostics](../../azure-monitor/app/proactive-diagnostics.md): These tests run automatically, so you don't have to do anything to set them up. They tell you if your app has an unusual rate of failed requests.
* [Metric alerts](../../azure-monitor/app/alerts.md): Set alerts to warn you if a metric crosses a threshold. You can set them on custom metrics that you code into your app.
