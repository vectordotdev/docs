---
description: Send logs to Timber via the Timber HTTP API
---

# HTTP

Timber has a rich [HTTP API](http://docs.api.timber.io/) that you can use to send logs to the Timber platform.

{% hint style="info" %}
Timber offers [language level libraries](../../languages/) and support for [log forwarders](../../log-forwarders/) that handle the efficient and reliable delivery of your log data. We _highly_ recommend using those when possible.
{% endhint %}

## Installation

Forwarding logs to Timber is highly specific to your HTTP client. Below are a few examples, as well as best practices. If possible, we recommend following our [language](../../languages/), [log forwarder](../../log-forwarders/), [operating system](../../operating-systems/), or [platform](../../platforms/) integrations, as log delivery is handled for you in all of these.

### Examples

{% tabs %}
{% tab title="http \(bearer auth\)" %}
```bash
POST https://logs.timber.io/sources/YOUR_SOURCE_ID/frames
Authorization: Bearer YOUR_API_KEY
Content-Type: application/ndjson
{"level": "debug", "message": "Testing the pipes"}
{"level": "debug", "message": "Testing the pipes again"}
```
{% endtab %}

{% tab title="http \(basic auth\)" %}
```bash
POST https://user:YOUR_API_KEY@logs.timber.io/sources/YOUR_SOURCE_ID/frames
Content-Type: application/ndjson
{"level": "debug", "message": "Testing the pipes"}
{"level": "debug", "message": "Testing the pipes again"}
```

{% hint style="info" %}
Timber will discard the `user` value. Basic auth required this. Use any value you'd like.
{% endhint %}
{% endtab %}

{% tab title="curl \(bearer auth\)" %}
```bash
curl \
--request POST \
--header 'Authorization: Bearer YOUR_API_KEY' \
--header "Content-Type: application/ndjson" \
--data '{"level": "debug", "message": "Testing the pipes"}
{"level": "debug", "message": "Testing the pipes again"}' \
https://logs.timber.io/sources/YOUR_SOURCE_ID/frames
```
{% endtab %}

{% tab title="curl \(basic auth\)" %}
```bash
curl \
--request POST \
--user "user:YOUR_API_KEY" \
--header "Content-Type: application/ndjson" \
--data '{"level": "debug", "message": "Testing the pipes"}
{"level": "debug", "message": "Testing the pipes again"}' \
https://logs.timber.io/sources/YOUR_SOURCE_ID/frames
```

{% hint style="info" %}
Timber will discard the `user` value. Basic auth required this. Use any value you'd like.
{% endhint %}
{% endtab %}
{% endtabs %}

### Getting Your API Key And Source ID

If you haven't already, please create a Timber source by following the [Sending Logs guide](../../../guides/sending-logs-to-timber.md). Timber will display your API key and source ID immediately after you create a source.

### Authorization

Timber requires the `Authorization` header as part of making requests to the Timber API. We support 2 strategies, the [`Bearer`](https://tools.ietf.org/html/rfc6750) and [`Basic`](https://tools.ietf.org/html/rfc7617) strategies.

#### Bearer Strategy

When choosing the `Bearer` strategy you can simply paste in your API key in its original form. You do not need to base 64 encode the key!

```text
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5c...
```

#### Basic Strategy

When choose the `Basic` strategy you _must_ do 2 things:

1. Prefix your API key with a `:`
2. Base 64 encode it.

For example, given this API key: `eyJhbGciOiJIUzI1NiIsInR5c`, you would supply the following header:

```text
Authorization: Basic ZXlKaGJHY2lPaUpJVXpJMU5pSXNJblI1Yw==
```

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

If you don't want to do this yourself you can use one of [Timber's language libraries](../../languages/).

## Limitations

See the [Limitations document](../../../under-the-hood/limitations.md) for a comprehensive list.

## Troubleshooting

To begin, please see our [log delivery troubleshooting guide](../../../guides/troubleshooting-log-delivery.md). This covers the most common issues we see with log delivery:

{% page-ref page="../../../guides/troubleshooting-log-delivery.md" %}

If the above guide does not resolve your issue please contact support.

