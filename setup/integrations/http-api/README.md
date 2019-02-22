---
description: Send logs to Timber via the Timber HTTP API
---

# HTTP API

Timber has a rich [HTTP API](http://docs.api.timber.io/) that you can use to send logs to the Timber platform.

{% hint style="info" %}
Timber offers [language level libraries](../../../under-the-hood/language-libraries.md) and [integrations](../) that handle the efficient and reliable delivery of your log data. We _highly_ recommend using those when possible.
{% endhint %}

## Installation

Forwarding logs to Timber is highly specific to your HTTP client. Below are a few examples, as well as best practices. If possible, we recommend using one of [Timber's libraries](../../../under-the-hood/language-libraries.md) as all of the intricate details of sending data to Timber are handled for you.

### Examples

{% tabs %}
{% tab title="http" %}
```bash
POST https://logs.timber.io/frames
Authorization: Bearer YOUR_API_KEY
Timber-Source-ID: YOUR_SOURCE_ID
Content-Type: application/ndjson
{"level": "debug", "message": "Testing the pipes"}
{"level": "debug", "message": "Testing the pipes again"}
```
{% endtab %}

{% tab title="curl" %}
```bash
curl \
--request POST \
--header 'Authorization: Bearer YOUR_API_KEY' \
--header 'Timber-Source-ID: YOUR_SOURCE_ID' \
--header "Content-Type: application/ndjson" \
--data '{"level": "debug", "message": "Testing the pipes"}
{"level": "debug", "message": "Testing the pipes again"}' \
https://logs.timber.io/frames
```
{% endtab %}
{% endtabs %}

### Getting Your API Key And Source ID

If you haven't already, please create a Timber source by following the [Sending Logs guide](../../../usage/sending-logs.md). Timber will display your API key and source ID immediately after you create a source.

### Headers

Timber requires 2 headers to properly send log data, the `Authorization` and `Timber-Source-ID` headers. Both are displayed on your source's installation page. This pay is displayed immediately after creating a source and can also be found by viewing your source's settings.

The `Timber-Source-ID` header is required so that we can properly route your log data. Your sources serve as "containers" for logs, so you must specify which source the log data belongs to.

### Buffering & Batching

Always buffer and batch your log data to Timber. Do _not_ send lines individually as this is extremely inefficient; you'll quickly tax your application. Timber recommends flushing your buffer every 2 seconds or until it's size reaches 950kb. Timber will not accept requests larger than 1mb.

### Accepted Content Types

Timber accepts the following content types \(ordered by preference\):

1. \`\`[`application/msgpack`](https://msgpack.org/index.html) 
2. \`\`[`application/ndjson`](http://ndjson.org/)\`\`
3. \`\`[`application/json`](https://www.json.org/) 
4. \`\`[`text/plain`](https://www.w3.org/Protocols/rfc1341/7_1_Text.html) 
5. \`\`[`application/logplex-1`](https://github.com/heroku/logplex/blob/master/doc/README.http_drains.md#logplex-http-drains)1

### Expected Response Codes

1. `202` - Success, logs accepted.
2. `400` - Request invalid, usually due to a bad `content-type`.
3. `401` - API key invalid.
4. `402` - Subscription blocked or unpaid. Please update your subscription.
5. `500` - Timber service error.

### Timber Libraries

If you don't want to do this yourself you can use one of [Timber's language libraries](../../../under-the-hood/language-libraries.md).

## Limitations

1. Requests cannot exceed 1mb in size. This includes all request data, including headers.
2. Only the [content types listed](./#accepted-content-types) are accepted.

## Troubleshooting

To begin, please see our [log delivery troubleshooting guide](../../guides/troubleshooting-log-delivery.md). This covers the most common issues we see with log delivery:

{% page-ref page="../../guides/troubleshooting-log-delivery.md" %}

If the above guide does not resolve your issue please contact support.

