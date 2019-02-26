---
description: Send logs to Timber via FluentD
---

# Fluentd

{% hint style="info" %}
If possible, we recommend using [Fluent Bit](../fluent-bit.md) for its superior performance.
{% endhint %}

[Fluentd](https://www.fluentd.org/) is a general purpose log forwarder that can forward logs from multiple sources to Timber.

## Installation

1. [Install Fluentd.](https://docs.fluentd.org/v1.0/categories/installation)
2. Install the [Timber Fluentd plugin](https://github.com/timberio/fluent-plugin-timber):  


   ```bash
   gem install fluent-plugin-timber
   ```

3. In `/etc/td-agent/td-agent.conf` , configure the Timber output:  


   _**Be sure to replace `YOUR_API_KEY` and `YOUR_SOURCE_ID` appropriately!**_  
   \(these are displayed on your source's installation page\).



   {% code-tabs %}
   {% code-tabs-item title="/etc/td-agent/td-agent.conf" %}
   ```markup
   <match *> # will match all inputs; replace with a scope to send subset (https://docs.fluentd.org/v1.0/articles/config-file#(2)-%E2%80%9Cmatch%E2%80%9D:-tell-fluentd-what-to-do!)
     @type timber
     api_key YOUR_API_KEY
     source_id YOUR_SOURCE_ID
   </match>
   ```
   {% endcode-tabs-item %}
   {% endcode-tabs %}

4. _Optionally_ install [plugins](https://www.fluentd.org/plugins) to capture context for your platform. For example, the [EC2](https://github.com/takus/fluent-plugin-ec2-metadata) and and [Kubernetes](https://github.com/fabric8io/fluent-plugin-kubernetes_metadata_filter) plugins.
5. Restart the Fluentd agent:  


   ```text
   sudo service td-agent restart
   ```

## Configuration

Please refer to the [FluentD configuration documentation](https://docs.fluentd.org/v0.12/articles/config-file).

## FAQs

### What is the difference between Fluentd and Fluent Bit

[Fluent Bit](../fluent-bit.md) is the new, faster, re-written version of Fluentd. Because it is relatively early in its development it lacking in features & plugins compared to Fluentd.

## Troubleshooting

To begin, please see our [log delivery troubleshooting guide](../../guides/troubleshooting-log-delivery.md). This covers the most common issues we see with log delivery:

{% page-ref page="../../guides/troubleshooting-log-delivery.md" %}

If the above troubleshooting guide does not resolve your issue we recommend enabling FluentD logging and analyzing log activity to understand how FluentD is functioning. Please refer to [FluentD's logging documentation](https://docs.fluentd.org/v1.0/articles/logging), you'll want to set the log level to debug to ensure you're getting all events.

