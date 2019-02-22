---
description: Send Docker Logs to Timber
---

# Docker

Timber integrates with [Docker](https://www.docker.com/) through [Fluent Bit](https://fluentbit.io/), a light-weight, fast, and battle tested log shipper.

## Installation

{% hint style="warning" %}
Requires Docker version 1.8.0 or later
{% endhint %}

1. [Install Fluent Bit for your platform.](fluent-bit.md#installation)
2. In `/etc/td-agent-bit/td-agent-bit.conf` , enable the [`Forward` input](https://docs.fluentbit.io/manual/input/forward) to collect Fluent Bit formatted logs:  


   {% code-tabs %}
   {% code-tabs-item title="/etc/td-agent-bit/td-agent-bit.conf" %}
   ```text
   [INPUT]                                                                                                                                                                                                            
       Name Forward
       Port 24224
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

Logs from any containers run after this point will be forwarded to Timber.

## Configuration

Please see [https://docs.docker.com/config/containers/logging/fluentd/](https://docs.docker.com/config/containers/logging/fluentd/) for additional log driver configuration.

## Automatic Context

In addition to the log message itself, the above setup will automatically augment your logs with the following context keys.

## Troubleshooting

Because the Docker integration relies on [Fluent Bit](fluent-bit.md) we recommend reviewing the following troubleshooting guides:

{% page-ref page="../guides/troubleshooting-log-delivery.md" %}

{% page-ref page="fluent-bit.md" %}

