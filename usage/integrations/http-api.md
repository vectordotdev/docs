---
description: Send logs to Timber via the Timber HTTP API
---

# HTTP API

Timber has a rich [HTTP API](http://docs.api.timber.io/) that you can use to send logs to the Timber platform.

{% hint style="info" %}
Timber offers language level libraries that handle the efficient and reliable delivery of log data for a variety of languages. We highly recommend using those when possible.
{% endhint %}

## Installation

Forwarding logs to Timber is highly specific to your HTTP client. Below are a few examples as well as best practices.

### Examples

{% hint style="warning" %}
Your Timber API key must be Base64 encoded before including it in the `Authorization` header. Please use the Base64 version of your API key!
{% endhint %}

{% tabs %}
{% tab title="http" %}
```bash
POST https://logs.timber.io/frames
Authorization: Basic {{my-base64-encoded-timber-api-key}}
Content-Type: text/plain
debug: Testing the pipes
debug: Testing the pipes again
```
{% endtab %}

{% tab title="curl" %}
```bash
curl \
--request POST \
--user '{{my-base64-encoded-timber-api-key}}' \
--header "Content-Type: text/plain" \
--data 'debug: Testing the pipes
debug: Testing the pipes again' \
https://logs.timber.io/frames
```
{% endtab %}
{% endtabs %}

### Base64 Encoding Your Timber API Key

The `Authorization` header requires that the value be [Base64 URL encoded](https://en.wikipedia.org/wiki/Base64). Timber provides you with a base64 encoded version. To manually encode your key you can run `irb` in your terminal and perform the following:

{% code-tabs %}
{% code-tabs-item title="irb" %}
```ruby
require "base64"
Base64.urlsafe_encode64("925_d317185e7ae25f2f:46572b152d4f3bbe79ceba443e994868b1a841c47cf13e1c27761a23d128f158")
```
{% endcode-tabs-item %}
{% endcode-tabs %}

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

