---
description: Send logs to Timber via Filebeat
---

# Filebeat

[Filebeat](https://www.elastic.co/guide/en/beats/filebeat/current/index.html) is a general purpose log forwarder that can forward log files to your Timber account.

{% hint style="info" %}
If given the choice, we recommend [Fluent Bit](fluent-bit.md) for it's superior performance and flexibility.
{% endhint %}

## Installation

1. [Install Filebeat for your platform.](https://www.elastic.co/guide/en/beats/filebeat/current/filebeat-installation.html)
2. [Configure the appropriate file inputs.](https://www.elastic.co/guide/en/beats/filebeat/current/configuration-filebeat-options.html)
3. Configure the `elasticsearch` output to point to Timber, _**replacing `YOUR_SOURCE_ID` and `YOUR_API_KEY` accordingly**_:  


   ```yaml
   output.elasticsearch:
     hosts: ["https://logs.timber.io:443"]
     path: "/sources/YOUR_SOURCE_ID/frames"
     headers:
       Authorization: Bearer YOUR_API_KEY
   ```

## Limitations

Because Filebeat does not support generic HTTP endpoints we use the Elasticsearch output since [Timber supports the Elasticsearch /\_bulk protocol](../protocols/elasticsearch.md). As such, [all limitations outlined in the Elasticsearch protocol](../protocols/elasticsearch.md#limitations) apply here.

