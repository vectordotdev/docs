---
description: Send Docker Logs to Timber
---

# Docker

Timber integrates with [Docker](https://www.docker.com/) through [Fluent Bit](https://fluentbit.io/), a light-weight, fast, and battle tested log shipper.

## Installation

{% hint style="warning" %}
Requires Docker version 1.8.0 or later
{% endhint %}

1. [Install Fluent Bit at the system level](fluent-bit.md#installation). Fluent Bit has excellent setup instructions, please follow the appropriate ones for your system.
2. In `/etc/td-agent-bit/td-agent-bit.conf` , add the following. Be sure to replace `YOUR_API_KEY` and `YOUR_SOURCE_ID` appropriately \(these are displayed on your source's installation page\).  


   {% code-tabs %}
   {% code-tabs-item title="/etc/td-agent-bit/td-agent-bit.conf" %}
   ```text
   [INPUT]                                                                                                                                                                                                            
       Name Forward
       Port 24224

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
     Header  Authorization Basic YOUR_API_KEY
     # Replace with your source ID
     Header  Timber-Source-ID YOUR_SOURCE_ID
     Header  Content-Type application/msgpack
   ```
   {% endcode-tabs-item %}
   {% endcode-tabs %}

3. Restart your Fluent Bit agent:  


   ```bash
   sudo service td-agent-bit restart
   ```

4. In `/etc/docker/daemon.json`, configure your docker daemon to forward to Fluent Bit:  


   {% code-tabs %}
   {% code-tabs-item title="/etc/docker/daemon.json" %}
   ```javascript
   {                                                                                                                                                                                                                  
       "log-driver": "fluentd"
   }
   ```
   {% endcode-tabs-item %}
   {% endcode-tabs %}

5. Restart your docker-daemon:  


   ```bash
   sudo service docker restart
   ```

Logs from all containers will be forwarded to Timber. We highly recommend configuring filtering and parsing as needed. Please see the [Fluent Bit configuration section](fluent-bit.md#configuration) for more information.

## Configuration

Please see [t](https://docs.docker.com/config/containers/logging/fluentd/)he [Fluent Bit configuration section](fluent-bit.md#configuration) for more information.

## Automatic Context

In addition to the log message itself, the above setup will automatically augment your logs with the following context keys.

## Troubleshooting

Because the Docker integration relies on [Fluent Bit](fluent-bit.md) we recommend reviewing the following troubleshooting guides:

{% page-ref page="../guides/troubleshooting-log-delivery.md" %}

{% page-ref page="fluent-bit.md" %}

