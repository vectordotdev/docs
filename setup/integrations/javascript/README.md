---
description: Send Javascript logs to Timber from your Node or Browser environment
---

# Javascript

Timber integrates with [Javascript](https://en.wikipedia.org/wiki/JavaScript) through its universal [Javascript library](https://github.com/timberio/timber-js). The Timber Javascript library features:

* **Universal Node/browser support**. Log user/system errors, visits, clicks, events - _anything_ - in Chrome, Safari, Edge, or on a Node.js server, and search logs in real-time via the [Timber.io console](https://timber.io/).
* **NPM or CDN**. Use natively in your app, Webpack/Rollup into your Node/browser bundle, or just drop in a `<script>` tag to your final HTML. Integrates easily into any app.
* **Written in Typescript; runs anywhere**. Enjoy a fully typed API, whether you use Typescript or plain JS. Plays nicely with any view engine \(React, Angular, Vue, jQuery, etc\), any web server \(Express, Koa, HAPI, a static SPA, etc\), and any back-end stack you can think of.
* **Blazing fast**. Queue 100 logs in ~1.5ms, with automatic background syncing, batching and throttling with Timber.io, optimizing network I/O and reducing CPU load.
* **Guaranteed consistency**. `timber.log()` returns a Promise that resolves when the log has been ACK'd by Timber, so you know your log has been stored safely.
* **Easy logging middleware**. Pass an async func to `timber.use()` to chain your own transforms or log middleware; `.log()` only resolves when all middleware complete!
* **Light as a feather.** The gzipped browser bundle weighs in at just **4.3kb**!
* **Plays nicely with other loggers**. Plays well with [Winston](https://github.com/timberio/timber-js/blob/master/packages/winston) and [Bunyan](https://github.com/timberio/timber-js/blob/master/packages/bunyan).

## Installation

{% tabs %}
{% tab title="Node" %}
1. Install the Timber Node library:  


   ```bash
   npm i @timberio/node
   ```

2. Import the Node logger:  


   ```javascript
   import { TimberNode } from "@timberio/node";
   ```

3. Create a new logger with your _**raw**_ API key:  


   ```javascript
   const logger = new TimberNode("{{your-raw-api-key}}");
   ```
{% endtab %}

{% tab title="Browser" %}
1. Install the Timber browser library:  


   ```bash
   npm i @timberio/browser
   ```

2. Import the Browser logger:  


   ```javascript
   import { TimberBrowser } from "@timberio/browser";
   ```

3. Create a new logger with your API key:  


   ```javascript
   const logger = new TimberBrowser("{{my-timber-api-key}}");
   ```
{% endtab %}
{% endtabs %}

## Configuration

Please refer to the following helper libraries and their associated documentation for configuration options:

| Package | What's it for? |
| :--- | :--- |
| [`@timberio/core`](https://github.com/timberio/timber-js/blob/master/packages/core) | Core library to extend for custom loggers |
| [`@timberio/tools`](https://github.com/timberio/timber-js/blob/master/packages/tools) | Tools/utils used by loggers for throttling, batching, queuing logs |
| [`@timberio/types`](https://github.com/timberio/timber-js/blob/master/packages/types) | Shared Typescript types |

## Usage

{% hint style="info" %}
Timber integrates with popular Node logging libraries, allowing you to use their interfaces for logging. Please see the [integrations section](./#integrations) for more information.
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
If you haven't already, please read our [structured logging best practices guide](../../guides/event-naming.md).
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

### Which package should I install to start logging?

For most Javascript projects, just `npm i @timberio/js`.

This will install both the [Node.js](https://github.com/timberio/timber-js/blob/master/packages/node) and [browser](https://github.com/timberio/timber-js/blob/master/packages/browser) versions in a single package.

Alternatively, if you only need to log in a single environment, import [`@timberio/browser`](https://github.com/timberio/timber-js/blob/master/packages/browser) or [`@timberio/node`](https://github.com/timberio/timber-js/blob/master/packages/node) directly.

### Why are there separate loggers for Node/the browser?

Both loggers extend [`@timberio/core`](https://github.com/timberio/timber-js/blob/master/packages/core), and share the same `.log()` API.

But each offer unique features optimized for the target environment.

For example, the browser version uses built-in features of a client's web browser and client-side `fetch()` to synchronize logs with Timber.io. The result is a fast, and nimble `<script>` tag or bundle that drops easily into any client-side app.

The Node version includes features such as _msgpack_ encoding, logging from streams and deep exception handling \(coming soon!\), which are specific to Node.js and would have no purpose in a client-side logger.

Importing a distinct version of the logger makes it simple for bundlers like [Parcel](https://parceljs.org/) or [Webpack](https://webpack.js.org/) to leave out the parts that aren't required in the browser, resulting in a much smaller download — only 4.3kb!

### Are Typescript types available?

Yes! Every part of the Timber JS library is written in Typescript, so you get full types \(and documentation\) right out-the-box.

### The root `package.json` is named `@timberio/logger` - do I need to import this?

Nope. This library and its associated components are laid out as a mono-repo using [Lerna](https://github.com/lerna/lerna), to make it easier to maintain all code from one home on Github.

`@timberio/logger` is the private name that governs all code, tests and other stuff that only concerns the maintainers of this library, and is not importable.

Instead, simply use [`@timberio/js`](https://github.com/timberio/timber-js/blob/master/packages/js)

## Troubleshooting

To begin, please see our [log delivery troubleshooting guide](../../guides/troubleshooting-log-delivery.md). This covers the most common issues we see with log delivery:

{% page-ref page="../../guides/troubleshooting-log-delivery.md" %}

If the above troubleshooting guide does not resolve your issue then we recommend enabling debug logging with

## Additional Resources



