---
description: Send logs to Timber via FluentD
---

# Fluentd

[Fluentd](https://www.fluentd.org/) is a general purpose log forwarder that can forward logs from multiple sources to Timber.

## Installation

1. [Install Fluentd.](https://docs.fluentd.org/v1.0/categories/installation)
2. Install the [Timber Fluentd plugin](https://github.com/timberio/fluent-plugin-timber):  


   ```bash
   gem install fluent-plugin-timber
   ```

3. Configure an additional output for Fluentd by adding the following to `/etc/td-agent/td-agent.conf` :  


   {% code-tabs %}
   {% code-tabs-item title="/etc/td-agent/td-agent.conf" %}
   ```markup
   <match *> # will match all inputs; replace with a scope to send subset (https://docs.fluentd.org/v1.0/articles/config-file#(2)-%E2%80%9Cmatch%E2%80%9D:-tell-fluentd-what-to-do!)
     @type timber
     api_key YOUR_API_KEY_HERE
   </match>
   ```
   {% endcode-tabs-item %}
   {% endcode-tabs %}

4. Restart your Fluentd agent:  


   ```text
   sudo service td-agent restart
   ```

