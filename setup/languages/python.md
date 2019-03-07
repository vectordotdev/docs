---
description: Send Python logs to Timber
---

# Python

Timber integrates with [Python](https://www.python.org/) through the [`timber` Python library](https://pypi.org/project/timber/), enabling you to send Python logs to your Timber account. The Timber Python library features:

* \*\*\*\*[**Simple integration**. Integrates with the Python `logging` library.](python.md#installation)
* \*\*\*\*[**Support for structured logging and events**](python.md#structured-logging)**.**
* [**Support for context**](python.md#setting-context)**.**
* [**Performant, light weight, with a thoughtful design**](python.md#performance)**.**

## Installation

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

## Configuration

Please see the [Configuration section](https://github.com/timberio/timber-python#configuration) within the Timber Python library README.

## Usage

### Basic Logging

Use the Python Logger as usual:

```python
logger.info('Info message')
```

### Structured Logging

{% hint style="info" %}
If you haven't already, please read our [structured logging best practices guide](../../guides/structured-logging-best-practices.md).
{% endhint %}

```python
logger.debug('Order #1234 placed, total: $520.23', extra={
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

To begin, please see our [log delivery troubleshooting guide](../../guides/troubleshooting-log-delivery.md). This covers the most common issues we see with log delivery:

{% page-ref page="../../guides/troubleshooting-log-delivery.md" %}

If the above troubleshooting guide does not resolve your issue then we recommend [enabling the `raise_exceptions` option](https://github.com/timberio/timber-python#raise_exceptions). This should surface any delivery errors.

