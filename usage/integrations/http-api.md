---
description: Send logs to Timber via the Timber HTTP API
---

# HTTP API

Timber has a rich [HTTP API](http://docs.api.timber.io/) that you can use to send logs to the Timber platform.

## Installation

Forwarding logs to Timber is highly specific to your HTTP client. Below are a few examples as well as best practices.

### Examples

Please replace `{{timber-api-key}}` with your Timber API key:

{% tabs %}
{% tab title="curl" %}
```bash
curl \
--request POST \
--user '{{my-timber-api-key}}' \
--header "Content-Type: text/plain" \
--data 'debug: Testing the pipes
debug: Testing the pipes again' \
https://logs.timber.io/frames
```
{% endtab %}

{% tab title="http" %}
```bash
POST https://logs.timber.io/frames
Authorization: Basic {{my-timber-api-key}}
Content-Type: text/plain
debug: Testing the pipes
debug: Testing the pipes again
```
{% endtab %}
{% endtabs %}

### Buffering & Batching

Always buffer and batch your log data to Timber. Do _not_ send lines individually as this is extremely inefficient; you'll quickly tax your application. Timber recommends flushing your buffer every 2 seconds or until it's size reaches 950kb. Timber will not accept requests larger than 1mb.

### Content Types

Timber accepts the following content types \(ordered by preference\):

1. \`\`[`application/msgpack`](https://msgpack.org/index.html) 
2. \`\`[`application/ndjson`](http://ndjson.org/)\`\`
3. \`\`[`application/json`](https://www.json.org/) 
4. \`\`[`text/plain`](https://www.w3.org/Protocols/rfc1341/7_1_Text.html) 
5. \`\`[`application/logplex-1`](https://github.com/heroku/logplex/blob/master/doc/README.http_drains.md#logplex-http-drains)1

### Response Codes

1. `202` - Success, logs accepted.
2. `400` - Request invalid, usually due to a bad `content-type`.
3. `401` - API key invalid.
4. `402` - Subscription blocked or unpaid. Please update your subscription.
5. `500` - Timber service error.

