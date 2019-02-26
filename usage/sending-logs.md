# Sending Logs

Using Timber starts with sending your logs to the Timber service. This guide walks through the various steps to complete that as quickly as possible.

## 1. Create A Source

## 2. Integrate

If you haven't done so already, head over to the [integrations section](../setup/sources/) and follow the installation instruction for your platform.

{% page-ref page="../setup/sources/" %}

## 3. Format Your Logs

{% hint style="info" %}
If you're using one of our [language libraries](../under-the-hood/language-libraries.md) you can skip this step since our library structure logs automatically.
{% endhint %}

Timber recognizes [many log formats](../under-the-hood/log-processing.md#log-formats), most users will not have to change the format of their logs, but if you find Timber isn't parsing your logs correctly please read the [log processing document](../under-the-hood/log-processing.md) which outlines formats and special fields.

{% page-ref page="../under-the-hood/log-processing.md" %}

## Troubleshooting

If you're having trouble getting logs to show up in your Timber account, please see the following guid:

{% page-ref page="../setup/guides/troubleshooting-log-delivery.md" %}

