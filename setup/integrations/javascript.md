---
description: Send Javascript & browser logs to Timber
---

# Javascript

Timber integrates with Javascript through its Javascript library. The Timber javascript library features:

* **Universal Node/browser support**. Log user/system errors, visits, clicks, events - _anything_ - in Chrome, Safari, Edge, or on a Node.js server, and search logs in real-time via the [Timber.io console](https://timber.io/).
* **NPM or CDN**. Use natively in your app, Webpack/Rollup into your Node/browser bundle, or just drop in a `<script>` tag to your final HTML. Integrates easily into any app.
* **Written in Typescript; runs anywhere**. Enjoy a fully typed API, whether you use Typescript or plain JS. Plays nicely with any view engine \(React, Angular, Vue, jQuery, etc\), any web server \(Express, Koa, HAPI, a static SPA, etc\), and any back-end stack you can think of.
* **Blazing fast**. Queue 100 logs in ~1.5ms, with automatic background syncing, batching and throttling with Timber.io, optimising network I/O and reducing CPU load.
* **Guaranteed consistency**. `timber.log()` returns a Promise that resolves when the log has been ACK'd by Timber, so you know your log has been stored safely.
* **Easy logging middleware**. Pass an async func to `timber.use()` to chain your own transforms or log middleware; `.log()` only resolves when all middleware complete!
* **Light as a feather.** The gzipped browser bundle weighs in at just **4.3kb**!
* **Plays nicely with other loggers**. Plays well with [Winston](https://github.com/timberio/timber-js/blob/master/packages/winston) and [Bunyan](https://github.com/timberio/timber-js/blob/master/packages/bunyan).

## Installation

{% tabs %}
{% tab title="Node" %}

{% endtab %}

{% tab title="Browser" %}
1. Install the universal Timber JS library  


   ```text
   npm i @timberio/js
   ```

2. Import the Node logger:  


   ```text
   import { Browser, Node } from "@timberio/js";
   ```

3. f
{% endtab %}
{% endtabs %}

### Which installation method should I use?

For most Javascript projects, just `npm i @timberio/js`. This will install both the [Node.js](https://github.com/timberio/timber-js/blob/master/packages/node) and [browser](https://github.com/timberio/timber-js/blob/master/packages/browser) versions in a single package. Alternatively, if you only need to log in a single environment, import [`@timberio/browser`](https://github.com/timberio/timber-js/blob/master/packages/browser) or [`@timberio/node`](https://github.com/timberio/timber-js/blob/master/packages/node) directly.

## Usage

{% hint style="info" %}
Timber integrates with popular Node logging libraries, allowing you to use their interfaces for logging. Please see the [integrations section](javascript.md#integrations) for more information.
{% endhint %}

### Basic Logging

Basic leveled logging statements can be made using the appropriate method:

```javascript
// You can log by .debug, .info, .warn or .error -- returns a Promise
logger.info("Hello world").then(log => {
  // At this point, your log is synced with Timber.io!
});
```

### Structured Logging

{% hint style="info" %}
If you haven't already, please read our [structured logging best practices guide](../guides/event-naming.md).
{% endhint %}

Augment your logs with structured data:

```javascript
timber.info("Order #1234 placed, total: $500.23", {
  order_placed: {
    id: 1234,
    total: 500.23
  },
});
```

## Integrations

Timber integrates with popular 3rd party libraries allowing you to customize how you use Timber:

| Name |  |
| :--- | :--- |
| \`\`[`@timberio/winston`](https://github.com/timberio/timber-js/tree/master/packages/winston)\`\` | Winston 3.x transport |
| \`\`[`@timberio/bunyan`](https://github.com/timberio/timber-js/tree/master/packages/bunyan)\`\` | Bunyan writable stream |

## Performance

The Timber javascript library was designed with a _keen_ focus on performance since we know how important this is in Node and browser environments. Please [see the docs on performance](https://github.com/timberio/timber-js#performance) for more information.

## FAQs

## Troubleshooting

