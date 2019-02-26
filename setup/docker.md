---
description: Send Docker Logs to Timber
---

# Docker

Timber integrates with [Docker](https://www.docker.com/) through [Fluent Bit](https://fluentbit.io/), a light-weight, fast, and battle tested log shipper.

## Installation

{% hint style="info" %}
Requires Docker version 1.8.0 or later
{% endhint %}

1. [Install Fluent Bit at the system level](fluent-bit.md#installation). Fluent Bit has excellent setup instructions, please follow the appropriate ones for your system.
2. In `/etc/td-agent-bit/td-agent-bit.conf` , add the following.  
  
   _**Be sure to replace `YOUR_API_KEY` and `YOUR_SOURCE_ID` appropriately!**_  
   \(these are displayed on your source's installation page\).  


   {% code-tabs %}
   {% code-tabs-item title="/etc/td-agent-bit/td-agent-bit.conf" %}
   ```yaml
   [INPUT]                                                                                                                                                                                                            
       Name Forward
       Port 24224

   [OUTPUT]
     Name    http
     # Will match all inputs, replace with your match if you want to send a subset
     Match   *
     tls     On
     Host    logs.timber.io
     Port    443
     # !!!!! Replace with your Timber source ID!
     URI     /sources/YOUR_SOURCE_ID/frames
     # !!!!! Replace with your Timber API key!
     Header  Authorization Basic YOUR_API_KEY
     Header  Content-Type application/msgpack
     Format  msgpack
     Retry_Limit 5
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

## FAQs

### Why do use Fluent Bit instead of your own agent?

Fluent Bit is a battle tested, performant, log forwarding utility written in C. It is actively maintained and always improving. It allows Timber to focus on the user experience and not the low level plumbing.

### What about Fluent Bit Docker images?

Fluent Bit maintains Docker images which you can find [here](https://docs.fluentbit.io/manual/installation/docker). Although, we do not recommend running them without a supervisor of some kind.

## Troubleshooting

Because the Docker integration relies on [Fluent Bit](fluent-bit.md) we recommend reviewing the following troubleshooting guides:

{% page-ref page="../guides/troubleshooting-log-delivery.md" %}

{% page-ref page="fluent-bit.md" %}

