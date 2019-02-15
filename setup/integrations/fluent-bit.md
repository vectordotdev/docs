---
description: Send logs to Timber via Fluent Bit
---

# Fluent Bit

Fluent Bit is a general purpose log forwarder that can forward logs from multiple destinations to Timber.

## Installation

1. [Install Fluent Bit](https://docs.fluentbit.io/manual/installation)
2. Configure a new output in `/etc/td-agent-bit/td-agent-bit.conf`:  


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

3. Restart your fluent-bit agent:  


   ```bash
   sudo service td-agent-bit restart
   ```

