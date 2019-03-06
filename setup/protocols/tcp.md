---
description: Send logs to Timber over TCP
---

# TCP

Timber accepts log data over the TCP protocol on port `6514`. All messages must conform to the Syslog 5424 message format. For this reason, we recommend using a Syslog system for forward your log data over TCP. If you prefer low-level integration please see the installation section below.

## Installation

{% hint style="info" %}
Timber integrates with [Syslog](../log-forwarders/syslog.md), which handles efficient delivery of log data over TCP. We recommend using [Syslog](../log-forwarders/syslog.md) if possible.
{% endhint %}

1. Format your log messages to conform to the [Syslog 5424 format](https://tools.ietf.org/html/rfc5424), _**replace `YOUR_API_KEY` and `YOUR_SOURCE_ID` accordingly**_:  


   ```text
   <34>1 2019-02-06T19:20:50.52-05:00 my.host.com su - ID47 [authentication@51576 source_id="YOUR_SOURCE_ID" api_key="YOUR_API_KEY"] Hello world
   ```

2. Be sure to prefix your message with their byte size, Timber will honor message trailers \(`\n`, `\r`, `\0`, and `\n\r`\) otherwise.
3. Send your messages to `logs.timber.io` on port `6514`

## Troubleshooting

Start by testing the pipes with a simple TCP request, replace `YOUR_API_KEY` and `YOUR_SOURCE_ID` accordingly:

```text
echo '<34>1 2019-02-06T19:20:50.52-05:00 my.host.com su - ID47 [authentication@51576 source_id="YOUR_SOURCE_ID" api_key="YOUR_API_KEY"] Hello world' | nc "logs.timber.io" 6514
```

If your log message appears you'll rule out Timber as the source of the problem. If it does not appear please follow the troubleshooting log deliver guide:

{% page-ref page="../../guides/troubleshooting-log-delivery.md" %}



