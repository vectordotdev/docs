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

