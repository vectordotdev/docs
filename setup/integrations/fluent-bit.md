---
description: Send logs to Timber via Fluent Bit
---

# Fluent Bit

[Fluent Bit](https://fluentbit.io/) is a general purpose log forwarder that can forward logs from multiple destinations to Timber.

## Installation

1. [Install Fluent Bit](https://docs.fluentbit.io/manual/installation)
2. In `/etc/td-agent-bit/td-agent-bit.conf`, configure a new output:  


   {% code-tabs %}
   {% code-tabs-item title="/etc/td-agent-bit/td-agent-bit.conf" %}
   ```yaml
   [OUTPUT]
     Name    http
     # will match all inputs, replace with your match if you want to send a subset
     Match   *

     Host    logs.timber.io
     Port    443
     URI     /frames
     tls     On
     # recommended
     Retry_Limit 5

     Format  msgpack
     # Replace with your API key
     Header  Authorization Basic YOUR_API_KEY_HERE
     Header  Content-Type application/msgpack
   ```
   {% endcode-tabs-item %}
   {% endcode-tabs %}

3. _Optionally_ install [parser](https://docs.fluentbit.io/manual/parser) and [filter](https://docs.fluentbit.io/manual/filter) plugins to enhance your logs.
4. Restart your fluent-bit agent:  


   ```bash
   sudo service td-agent-bit restart
   ```

## Configuration

Please see the [Fluent Bit configuration documentation](https://docs.fluentbit.io/manual/configuration).

## Troubleshooting

To begin, please see our [log delivery troubleshooting guide](../guides/troubleshooting-log-delivery.md). This covers the most common issues we see with log delivery:

{% page-ref page="../guides/troubleshooting-log-delivery.md" %}

If the above troubleshooting guide does not resolve your issue then we recommend enabling logging for the FluentBit service itself. You can do so by adding the following section to your FluentBit configuration file, typically located at:

```text
[SERVICE]
    Log_File    /var/log/fluentbit.log
    Log_Level   DEBUG
```

More information on these options can be found in the [FluentBit configuration documentation](https://docs.fluentbit.io/manual/service).

