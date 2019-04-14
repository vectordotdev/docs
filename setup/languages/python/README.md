---
description: Send Python logs to Timber
---

# Python

Timber integrates with [Python](https://www.python.org/) through the [`timber` Python library](https://pypi.org/project/timber/), enabling you to send Python logs to your Timber account.

## Features

* \*\*\*\*[**Simple integration**. Integrates with the Python `logging` library.](./#installation)
* \*\*\*\*[**Support for structured logging and events**](./#structured-logging)**.**
* [**Support for context**](./#setting-context)**.**
* [**Performant, light weight, with a thoughtful design**](./#performance)**.**

## Example

To quickly demonstrate the power of Timber, the following example logs a simple structured log with context:

```python
with timber.context(user={'id': 123}):
    logger.info('Order #1234 placed, total: $520.23', extra={
      'order_placed': {
        'id': 1234,
        'total': 520.23
      }
    })
```

This produces the following JSON log line:

```javascript
{
    "dt": "2019-03-04T03:44:21.221232Z",
    "level": "info",
    "message": "Order #1234 placed, total: $520.23",
    "order_placed": {
        "id": 1234,
        "total": 520.23
    },
    "context": {
        "runtime": {
            "function": "myFunc",
            "file": "file.py",
            "line": 23,
            "thread_id": "70179740727560",
            "thread_name": "async_thread",
            "logger_name": "logger"
        },
        "system": {
            "pid": 20643,
            "process_name": "python"
        },
        "user": {
            "id": 1234
        }
    }
}
```

Continue to learn more about [setting context](./#setting-context), [automatic context](./#automatic-context), [structured logging](./#structured-logging), and more.

## Installation

{% hint style="info" %}
If you're unsure, we recommend installing via the "HTTP" method. To understand why you would choose one over the other, please see the ["Sending Logs To Timber" guide](../../../guides/sending-logs-to-timber.md).
{% endhint %}

{% tabs %}
{% tab title="HTTP \(Inline\)" %}
Send logs directly to Timber over HTTP from within your app. The "inline" method, as opposed to the "config" method, instantiates the Timber logger inline within your code instead of the [Python Logger config dictionary](https://docs.python.org/3/library/logging.config.html#).

1. Install the [`timber` library](https://pypi.org/project/timber/):  


   ```bash
   pip install timber
   ```

2. Install the Timber logger, _**replace `YOUR_API_KEY` and `YOUR_SOURCE_ID` accordingly**_:



   ```python
   import logging
   import timber

   logger = logging.getLogger(__name__)
   # Set to logging.DEBUG if you want all logs
   logger.setLevel(logging.INFO)

   timber_handler = timber.TimberHandler(source_id='YOUR_SOURCE_ID', api_key='YOUR_API_KEY')
   logger.addHandler(timber_handler)
   ```
{% endtab %}

{% tab title="HTTP \(config\)" %}
Send logs directly to Timber over HTTP from within your app. The "config" method, as opposed to the "inline" method, adds the Timber logging handler to your [logging config dictionary](https://docs.python.org/3/library/logging.config.html#).

1. Install the [`timber` library](https://pypi.org/project/timber/):  


   ```bash
   pip install timber
   ```

2. Add the `timber`handler to your `LOGGING` configuration, _**replace `YOUR_API_KEY` and `YOUR_SOURCE_ID` accordingly**_:  


   {% code-tabs %}
   {% code-tabs-item title="myapp/settings.py" %}
   ```python
   # This config dictionary could be defined inline or
   # within a file. This depends on how your app is
   # configured. For Django apps, this is located in
   # your settings.py file.
   LOGGING = {
       'version': 1,
       'handlers': {
           'timber': {
               'level': 'DEBUG',
               'class': 'timber.TimberHandler',
               'api_key': 'YOUR_API_KEY',
               'source_id': 'YOUR_SOURCE_ID'
           },
       },
       'loggers': {
           'my-app': {
               'handlers': ['timber'],
               'level': 'DEBUG',
           }
       },
   }
   ```
   {% endcode-tabs-item %}
   {% endcode-tabs %}
{% endtab %}

{% tab title="STDOUT \(inline\)" %}
Write logs to `STDOUT`, encoded in JSON format, and ship them external from your app. The "inline" method, as opposed to the "config" method, instantiates the Timber logger inline within your code instead of the [Python Logger config dictionary](https://docs.python.org/3/library/logging.config.html#).

{% hint style="warning" %}
This method is more advanced and requires a separate step to ship logs to Timber. Basic knowledge of `STDOUT` and log management is required. If you are unsure, please use the "HTTP" method. For more information on the advantages of this method please see [this guide](../../../guides/sending-logs-to-timber.md).
{% endhint %}

1. Install the [`timber` library](https://pypi.org/project/timber/):  


   ```bash
   pip install timber
   ```

2. Install the Timber logger, _**replace `YOUR_API_KEY` and `YOUR_SOURCE_ID` accordingly**_:



   ```python
   import logging
   import timber

   logger = logging.getLogger(__name__)
   # Set to logging.DEBUG if you want all logs
   logger.setLevel(logging.INFO)

   stream_handler = logging.StreamHandler()
   formatter = timber.Formatter()
   stream_handler.setFormatter(formatter)
   logger.addHandler(stream_handler)
   ```
{% endtab %}

{% tab title="STDOUT \(config\)" %}
Write logs to `STDOUT`, encoded in JSON format, and ship them external from your app. The "config" method, as opposed to the "inline" method, adds the Timber logging handler to your [logging config dictionary](https://docs.python.org/3/library/logging.config.html#).

{% hint style="warning" %}
This method is more advanced and requires a separate step to ship logs to Timber. Basic knowledge of `STDOUT` and log management is required. If you are unsure, please use the "HTTP" method. For more information on the advantages of this method please see [this guide](../../../guides/sending-logs-to-timber.md).
{% endhint %}

1. Install the [`timber` library](https://pypi.org/project/timber/):  


   ```bash
   pip install timber
   ```

2. Add the `timber` formatter to your list of formatters:  


   {% code-tabs %}
   {% code-tabs-item title="myapp/settings.py" %}
   ```python
   # This config dictionary could be defined inline or
   # within a file. This depends on how your app is
   # configured. For Django apps, this is located in
   # your settings.py file.
   LOGGING = {
       'version': 1,
       'formatters': {
           'timber': {
               '()': 'timber.TimberFormatter',
           },
       },
       'handlers': {
           'stderr': {
               'level': 'DEBUG',
               'class': 'logging.StreamHandler',
               'stream': 'ext://sys.stdout',
               'formatter': 'timber',
           },
       },
       'loggers': {
           'my-app': {
               'handlers': ['stdout'],
               'level': 'DEBUG',
           },
       },
   }
   ```
   {% endcode-tabs-item %}
   {% endcode-tabs %}
{% endtab %}
{% endtabs %}

## Configuration

The `TimberHandler` takes a variety of parameters that allow for fine-grained control over its behavior.

### `level`

Like any other `logger.Handler`, the `TimberHandler` can be configured to only respond to log events of a specific level:

```python
# Only respond to events as least as important as `warning`
timber_handler = timber.TimberHandler(level=logging.WARNING)
```

You can also set the level as a whole on the logger itself:

```python
logger.setLevel(logging.INFO)
```

### `buffer_capacity` and `flush_interval`

Timber buffers log events and sends them in the background for maximum performance. All outstanding log events are sent when the buffer is full or a certain amount of time has passed since any events were sent. To control the size of the buffer, pass the `buffer_capacity` argument:

```python
# Never allow more than 50 outstanding log events
timber_handler = timber.TimberHandler(buffer_capacity=50)
```

To control the maximum amount of time between buffer flushes, pass the `flush_interval` argument:

```python
# Send any outstanding log events at most every 60 seconds
timber_handler = timber.TimberHandler(flush_interval=60)
```

### `raise_exceptions`

Logging should never break your application, which is why the `TimberHandler` suppresses all internal exceptions by default. To change this behavior:

```python
# Allow exceptions from internal log handling to propagate to the application,
# instead of suppressing them.
timber_handler = timber.TimberHandler(raise_exceptions=True)
```

### `drop_extra_events`

As soon as the internal log event buffer is full, Timber flushes all of the events to the server, but while that occurs any incoming log events are dropped by default. To make your application block in this case to ensure that all log statements are sent to Timber:

```python
# Make log statements block until the internal log event buffer is no longer full.
timber_handler = timber.TimberHandler(drop_extra_events=False)
```

### `context`

By default all `TimberHandler` instances use the same context object \(`timber.context`\), but if you'd like to use multiple loggers and multiple handlers, each with a different context, it is possible to explicitly create and pass your own:

```python
import logging
import timber

logger = logging.getLogger(__name__)

context = timber.TimberContext()
timber_handler = timber.TimberHandler(context=context)
logger.addHandler(timber_handler)

with context(job={'id': 123}):
  logger.critical('Background job execution started')
  # ... code here
  logger.critical('Background job execution completed')
```

## Usage

### Basic Logging

Use the Python Logger as usual:

```python
logger.info('Info message')
```

### Structured Logging

{% hint style="info" %}
If you haven't already, please read our [structured logging best practices guide](../../../guides/structured-logging-best-practices.md).
{% endhint %}

```python
logger.info('Order #1234 placed, total: $520.23', extra={
  'order_placed': {
    'id': 1234,
    'total': 520.23
  }
})
```

### Setting Context

Add shared structured data across multiple log statements:

```python
with timber.context(job={'id': 123}):
  logger.info('Background job execution started')
  # ... code here
  logger.info('Background job execution completed')
```

Contexts nest and merge naturally:

```python
with timber.context(job={'id': 123, 'count': 1}):
  # Sends a context {'job': {'id': 123, 'count': 1}}
  logger.info('Background job execution started')
  # ... code here
  with timber.context(job={'count': 2}):
    # Sends a context {'job': {'id': 123, 'count': 2}}
    logger.info('Background job in progress')
  # ... code here
  # Sends a context {'job': {'id': 123, 'count': 1}}
  logger.info('Background job execution completed')
```

## Automatic Context

`timber` automatically captures [context](../../../under-the-hood/concepts.md#context) to enrich your logs.

### system 

The `system` context captures system level information such as hostname and pid:

```javascript
{
    "context": {
        "system": {
            "pid": 20643,
            "process_name": "python"
        }
    }
}
```

| Field | Description |
| :--- | :--- |
| `context.system.process_name` | System level process name |
| `context.system.pid` | System level process ID |

### runtime

The `runtime` context captures information about the origin of the log line:

```javascript
{
    "context": {
        "runtime": {
            "function": "myFunc",
            "file": "file.py",
            "line": 23,
            "thread_id": "70179740727560",
            "thread_name": "async_thread",
            "logger_name": "logger"
        }
    }
}
```

| Field | Description |
| :--- | :--- |
| `context.runtime.function` | The name of the function where the logging statement was called. |
| `context.runtime.file` | The name of the file where the logging statement was called. |
| `context.runtime.line` | The line number where the logging statement was called. |
| `context.runtime.thread_id` | The current unique thread ID. |
| `context.runtime.thread_name` | The current thread name. |
| `context.runtime.logger_name` | The name of the logger being used. |

## Integrations

Timber integrates with 3rd party libraries to enhance and upgrade the logs they emit, turning them into rich structured events.

{% page-ref page="integrations/" %}

## Performance

Extreme care was taken into the design of `timber` to be fast and reliable:

1. `timber` works directly with the [Python `logging` library](https://docs.python.org/3/library/logging.html), automatically assuming all of the [stability and performance benefits](https://hexdocs.pm/logger/master/Logger.html#module-runtime-configuration) this provides.
2. Log data is buffered and flushed on an interval to optimize performance and delivery.
3. The `timber` HTTP backend uses a controlled [multi-buffer](https://en.wikipedia.org/wiki/Multiple_buffering) design to efficiently ship data to the Timber service.
4. Connections are re-used and rotated to ensure efficient delivery of log data.
5. Delivery failures are retried with an exponential backoff, maximizing successful delivery.
6. [Msgpack](https://msgpack.org/index.html) is used for payload encoding for its superior performance and memory management.
7. The Timber service ingest endpoint is a HA service designed to handle extreme fluctuations of volume, it responds in under 50ms to reduce back pressure.

## Troubleshooting

To begin, please see our [log delivery troubleshooting guide](../../../guides/troubleshooting-log-delivery.md). This covers the most common issues we see with log delivery:

{% page-ref page="../../../guides/troubleshooting-log-delivery.md" %}

If the above troubleshooting guide does not resolve your issue then we recommend [enabling the `raise_exceptions` option](https://github.com/timberio/timber-python#raise_exceptions). This should surface any delivery errors.

