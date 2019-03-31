---
description: Send Docker Logs to Timber
---

# Docker

Timber integrates with [Docker](https://www.docker.com/) through [Fluent Bit](https://fluentbit.io/), a light-weight, fast, and battle tested log shipper.

## Installation

{% hint style="info" %}
Requires Docker version 1.8.0 or later
{% endhint %}

1. [Install Fluent Bit at the system level](../log-forwarders/fluent-bit.md#installation). Fluent Bit has excellent setup instructions, please follow the appropriate ones for your system.
2. In `/etc/td-agent-bit/td-agent-bit.conf` , add the following.  


   _**Replace `TIMBER_API_KEY`, `TIMBER_SOURCE_ID`, and `HOSTNAME`accordingly.  
   Note: Fluent Bit will resolve these values as environment variables if they are set.**_  


   {% code-tabs %}
   {% code-tabs-item title="/etc/td-agent-bit/td-agent-bit.conf" %}
   ```yaml
   [SERVICE]
     # Reduce the flush interval for better real-time access
     Flush  2
  
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

Logs from all containers will be forwarded to Timber. We highly recommend configuring filtering and parsing as needed. Please see the [Fluent Bit configuration section](../log-forwarders/fluent-bit.md#configuration) for more information.

## Configuration

Please see [t](https://docs.docker.com/config/containers/logging/fluentd/)he [Fluent Bit configuration section](../log-forwarders/fluent-bit.md#configuration) for more information.

## Automatic Context

Please see the [Automatic Context section](../log-forwarders/fluent-bit.md#automatic-context) in the Fluent Bit docs.

## FAQs

### Why do recommend Fluent Bit instead of your own agent?

Fluent Bit is a battle tested, performant, log forwarding utility written in C. It is actively maintained and always improving. It allows Timber to focus on the user experience and not the low level plumbing.

### What about Fluent Bit Docker images?

Fluent Bit maintains Docker images which you can find [here](https://docs.fluentbit.io/manual/installation/docker). Although, we do not recommend running them without a supervisor of some kind.

## Troubleshooting

Because the Docker integration relies on [Fluent Bit](../log-forwarders/fluent-bit.md) we recommend reviewing the following troubleshooting guides:

{% page-ref page="../../guides/troubleshooting-log-delivery.md" %}

{% page-ref page="../log-forwarders/fluent-bit.md" %}

