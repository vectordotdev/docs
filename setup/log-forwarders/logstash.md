---
description: Send logs to Timber from Logstash
---

# Logstash

Logstash is a general purpose log forwarders that can forward logs to your Timber account.

{% hint style="info" %}
If given the choice, we recommend [Fluent Bit](fluent-bit.md) for it's superior performance and flexibility.
{% endhint %}

## Installation

1. Install Logstash for your platform.
2. Configure the appropriate input plugins.
3. Configure the `http` output plugin to send data to Timber, _**replacing `YOUR_API_KEY` and `YOUR_SOURCE_ID` appropriately**_:  


   ```javascript
   output {
     http {
       url => "https://logs.timber.io/sources/YOUR_SOURCE_ID/frames"
       http_method => "post"
       headers => {
         "Authorization" => "Bearer YOUR_API_KEY"
       }
       format => "json"
       workers => 10
     }
   }
   ```

