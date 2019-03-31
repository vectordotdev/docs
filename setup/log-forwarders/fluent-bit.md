---
description: Send logs to Timber via Fluent Bit
---

# Fluent Bit

[Fluent Bit](https://fluentbit.io/) is a general purpose log forwarder that can forward logs from [multiple sources](https://docs.fluentbit.io/manual/input) to Timber. You can use Fluent Bit to send logs to your Timber account.

## Installation

1. [Install Fluent Bit](https://docs.fluentbit.io/manual/installation) for your platform. Such as [Debian](https://docs.fluentbit.io/manual/installation/debian), [Ubuntu](https://docs.fluentbit.io/manual/installation/ubuntu), [CentOS](https://docs.fluentbit.io/manual/installation/redhat_centos), or [building directly](https://docs.fluentbit.io/manual/installation/build_install).
2. Install your desired [input plugins](https://docs.fluentbit.io/manual/input), such as [file tailing](https://docs.fluentbit.io/manual/input/tail), [STDIN](https://docs.fluentbit.io/manual/input/stdin), [Syslog](https://docs.fluentbit.io/manual/input/syslog), or [TCP](https://docs.fluentbit.io/manual/input/tcp).
3. In `/etc/td-agent-bit/td-agent-bit.conf`, add the following.  
  
   _**Replace `TIMBER_API_KEY`, `TIMBER_SOURCE_ID`, and `HOSTNAME`accordingly.  
   Note: Fluent Bit will resolve these values as environment variables if they are set.**_  


   {% code-tabs %}
   {% code-tabs-item title="/etc/td-agent-bit/td-agent-bit.conf" %}
   ```yaml
   [SERVICE]
     # Reduce the flush interval for better real-time access
     Flush  2
  
   [OUTPUT]
     Name    http
     # Will match all inputs, replace with your match if you want to send a subset
     Match   *
     tls     On
     Host    logs.timber.io
     Port    443
     URI     /sources/${TIMBER_SOURCE_ID}/frames
     Header  Authorization Bearer ${TIMBER_API_KEY}
     Header  Content-Type application/msgpack
     Format  msgpack
     Retry_Limit 5
  
   [FILTER]
       Name record_modifier
       # Will match all inputs, replace with your match if you want to send a subset
       Match *
       Record hostname ${HOSTNAME}
   ```
   {% endcode-tabs-item %}
   {% endcode-tabs %}

4. _Optionally_ install any additionally [parser](https://docs.fluentbit.io/manual/parser) and [filter](https://docs.fluentbit.io/manual/filter) plugins to enhance your logs.
5. Restart your fluent-bit agent:  


   ```bash
   sudo service td-agent-bit restart
   ```

## Configuration

Please see the [Fluent Bit configuration documentation](https://docs.fluentbit.io/manual/configuration). Specifically the [parsing](https://docs.fluentbit.io/manual/parser) and [filtering](https://docs.fluentbit.io/manual/filter) sections.

## Automatic Context

Unless you're operating Fluent Bit in a [Kubernetes cluster](../platforms/kubernetes.md), context is not automatically added to your logs by default. This is why we include the `[FILTER]` plugin as part of the [installation instructions](fluent-bit.md#installation). If possible, we recommend expanding this with any other information you might find helpful.

## FAQs

### Why do recommend Fluent Bit instead of your own agent?

Fluent Bit is a battle tested, performant, log forwarding utility written in C. It is actively maintained and always improving. It allows Timber to focus on the user experience and not the low level plumbing.

### Should I use Fluent Bit or FluentD?

You can read more about both utilities in the [FluentD docs](https://docs.fluentbit.io/manual/about/fluentd_and_fluentbit). In general, we recommend Fluent Bit when possible due to its improved performance. FluentD should be used in situations where Fluent Bit is not sufficient. For example, if you want to collect data from a source that Fluent Bit does not support, or you want to use a FluentD plugin that is not available in Fluent Bit.

## Troubleshooting

To begin, please see our [log delivery troubleshooting guide](../../guides/troubleshooting-log-delivery.md). This covers the most common issues we see with log delivery:

{% page-ref page="../../guides/troubleshooting-log-delivery.md" %}

If the above troubleshooting guide does not resolve your issue then we recommend enabling logging for the FluentBit service itself. You can do so by adding the following section to your FluentBit configuration file, typically located at:

```text
[SERVICE]
    Log_File    /var/log/fluentbit.log
    Log_Level   DEBUG
```

More information on these options can be found in the [FluentBit configuration documentation](https://docs.fluentbit.io/manual/service).

