---
description: Send Javascript logs to Timber from your Node or Browser environment
---

# Javascript

Timber integrates with [Javascript](https://en.wikipedia.org/wiki/JavaScript) through its _universal_ [Javascript library](https://github.com/timberio/timber-js), enabling you to send Javascript logs from your Node Or Browser environment to your Timber account.

## Features

* \*\*\*\*[**Universal Node/browser support**.](./#installation)
* \*\*\*\*[**NPM or CDN.**](./#installation)
* \*\*\*\*[**Written in Typescript; runs anywhere**.](./#are-typescript-types-available)
* \*\*\*\*[**Blazing fast**](./#performance).
* \*\*\*\*[**Guaranteed consistency**](./#basic-logging).
* \*\*\*\*[**Easy logging middleware**.](./#adding-middleware)
* **Light as a feather.** The gzipped browser bundle weighs in at just **4.3kb**!
* \*\*\*\*[**Plays nicely with other loggers**](integrations/).

## Example

To quickly demonstrate the power of Timber, the following example logs a simple structured log with context:

```javascript
import { ITimberLog } from "@timberio/types";

// Add middleware for context
async function addCurrentUser(log: ITimberLog): Promise<ITimberLog> {
  return {
    ...log,
    user: {id: 1000, name: "Lee"},
  };
}

timber.use(addCurrentUser);

timber.info(
  "Order #1234 placed, total: $500.23",
  {order_placed: {id: 1234, total: 500.23}}
).then(log => {
  // At this point, your log is synced with Timber.io!
});
```

This produces the following JSON log line:

```javascript
{
    "dt": "2019-03-04T03:44:21.221232Z",
    "level": "info",
    "message": "Order #1234 placed, total: $500.23",
    "order_placed": {
        "id": 1234,
        "total": 500.23
    },
    "context": {
        "system": {
            "hostname": "ec2-44-125-241-8"
        },
        "user": {
            "id": "1000",
            "name": "Lee"
        }
    }
}
```

Continue to learn more about [setting context](./#adding-context), [automatic context](../elixir/#automatic-context), [structured logging](./#structured-logging), and more.

## Installation

{% hint style="info" %}
We recommend the "HTTP" method for your environment if you are unsure. To understand why you would choose one over the other, please see the ["Ship Logs From Within My App?" guide](../../../guides/sending-logs-to-timber.md).
{% endhint %}

{% tabs %}
{% tab title="Node \(HTTP\)" %}
Send logs directly from within your app over HTTP:

1. Install the Timber Node library:  


   ```bash
   npm i @timberio/node
   ```

2. Import the Node logger:  


   In ES6/Typescript, import the `Timber` class:  


   ```javascript
   import { Timber } from "@timberio/node";
   ```

   Or, for CommonJS, require the package:  


   ```javascript
   const { Timber } = require("@timberio/node");
   ```

3. Create a new logger, _**replace `YOUR_API_KEY` and `YOUR_SOURCE_ID` accordingly**_:



   ```javascript
   const logger = new Timber("YOUR_API_KEY", "YOUR_SOURCE_ID");
   ```
{% endtab %}

{% tab title="Node \(STDOUT\)" %}
Write logs to `STDOUT` and ship them external from your app:

{% hint style="warning" %}
This method is more advanced and requires a separate step to ship logs to Timber. Basic knowledge of `STDOUT` and log management is required. For more information on the advantages of this method please see [this guide](../../../guides/sending-logs-to-timber.md).
{% endhint %}

1. Install the Timber Node library:  


   ```bash
   npm i @timberio/node
   ```

2. Import the Node logger:  


   In ES6/Typescript, import the `Timber` class:  


   ```javascript
   import { Timber } from "@timberio/node";
   ```

   Or, for CommonJS, require the package:  


   ```javascript
   const { Timber } = require("@timberio/node");
   ```

3. Create a new logger, _**replace `YOUR_API_KEY` and `YOUR_SOURCE_ID` accordingly**_:



   ```javascript
   const logger = new Timber("YOUR_API_KEY", "YOUR_SOURCE_ID");
   timber.setSync(async logs => {
       logs.forEach(log => console.log(log));
       return logs;
   })
   ```

4. At this point your application is writing logs to `STDOUT` in JSON format. Please choose the appropriate [platform](../../platforms/), [log forwarder](../../log-forwarders/), or [operating system](../../operating-systems/).
{% endtab %}

{% tab title="Browser \(NPM\)" %}
For browser based environments. If you're using a module bundler like Webpack or Rollup, you can install the package directly from [NPM](https://www.npmjs.com/package/@timberio/browser):

1. Install the Timber browser library:  


   ```bash
   npm i @timberio/browser
   ```

2. Import the `Timber` class:  
  
   In ES6/Typescript, import the `Timber` class:  


   ```javascript
   import { Timber } from "@timberio/browser";
   ```

   Or, for CommonJS, require the package:  


   ```javascript
   const { Timber } = require("@timberio/browser");
   ```

3. Create a new logger with your source ID and API key:



   ```javascript
   const logger = new Timber("YOUR_API_KEY", "YOUR_SOURCE_ID");
   ```
{% endtab %}

{% tab title="Browser \(CDN\)" %}
For Browser based environments. If you're not using a Node.js module bundler, you can log in any client-side app by dropping in a `<script>` tag:

1. Install the Timber browser library:  


   ```markup
   <script src="https://unpkg.com/@timberio/browser@0.X/dist/umd/timber.js"></script>
   ```

   This will place the `Timber` class on `window.Timber`  

2. Create a new logger with your source ID and API key:



   ```javascript
   const logger = new window.Timber("YOUR_API_KEY", "YOUR_SOURCE_ID");
   ```
{% endtab %}

{% tab title="Other Loggers" %}
Timber integrates with other popular loggers, such as [Bunyan](integrations/bunyan.md), [Pino](integrations/pino.md), and [Winston](integrations/winston.md). See the [Integrations section](integrations/) for a complete list.
{% endtab %}
{% endtabs %}

## Configuration

The `Timber` constructor takes a list of options as defined by [`ITimberOptions` type](https://github.com/timberio/timber-js/tree/master/packages/types#itimberoptions) in the [`@timberio/types` package](https://github.com/timberio/timber-js/tree/master/packages/types). Please see the [`@timber/type` docs](https://github.com/timberio/timber-js/tree/master/packages/types) for a full list of options.

#### Example: Passing configuration options

Here we pass the `ignoreExceptions` option which is a boolean to specify whether thrown errors/failed logs should be ignored:

```javascript
const logger = new Timber("YOUR_API_KEY", "YOUR_SOURCE_ID", {ignoreExceptions: true});
```

## Usage

{% hint style="info" %}
Timber integrates with popular Node logging libraries, allowing you to use their interfaces for logging. Please see the [integrations section](integrations/) for more information.
{% endhint %}

### Basic Logging

Basic leveled logging statements can be made using the appropriate method \(`debug`, `info`, `warn`, or `error`\):

```javascript
// Use any level debug, info, warn, or error
logger.info("Hello world")
```

All log levels return a Promise that will resolve once the log has been synced with Timber:

```javascript
// Will resolve when synced with Timber.io (or reject if there's an error)
timber.log("some log message").then(log => {
  // `log` is the transformed log, after going through middleware
});
```

### Structured Logging

{% hint style="info" %}
If you haven't already, please read our [structured logging best practices guide](../../../guides/structured-logging-best-practices.md).
{% endhint %}

Augment your logs with structured data:

```javascript
timber.info("Order #1234 placed, total: $500.23", {
  order_placed: {
    id: 1234,
    total: 500.23
  },
}).then(log => {
  // At this point, your log is synced with Timber.io!
});
```

### Adding Context

For completeness with our other libraries, we added this section. Adding context is achieved by [adding middleware](./#adding-middleware). Please see:

* [Adding Middleware](./#adding-middleware)
* [Tying Logs To Users](./#tying-logs-to-users)
* [Tying Logs To HTTP Requests](./#tying-logs-to-http-requests)

### Adding Middleware

You can add your own middleware functions, which act as transforms on the [`ITimberLog` log object](https://github.com/timberio/timber-js/tree/master/packages/types#itimberlog).

This is useful for augmenting the log prior to syncing with Timber, or even pushing the log to another service.

Here's what a middleware function looks like:

```javascript
import { ITimberLog } from "@timberio/types";

// In this example function, we'll add custom 'context' meta to the log
// representing the currently logged in user.
//
// Note: a middleware function is any function that takes an `ITimberLog`
// and returns a `Promise<ITimberLog>`
async function addCurrentUser(log: ITimberLog): Promise<ITimberLog> {
  return {
    ...log, // <-- copy the existing log
    user: {
      // ... and add our own `context` data
      id: 1000,
      name: "Lee",
    },
  };
}
```

Then just attach to the Timber instance with `.use`:

```javascript
timber.use(addCurrentUser);
```

You can add any number of pipeline functions to your `logger` instance, and they'll run in order.

Middleware functions run _before_ the final sync to Timber.io. Pipeline functions should return a `Promise<ITimberLog>`, making it possible to augment logs with asynchronous data from external sources.

{% hint style="danger" %}
If an exception is thrown anywhere in the pipeline chain the log won't be synced! Wrap an async `try/catch` block around your call to `.log|info|debug|warn|error()` or tack on a `catch()` to ensure your errors are handled.
{% endhint %}

### Removing Middleware

If you wish to remove middleware, pass in the original middleware function to `.remove()`:

```javascript
// `addCurrentUser` will no longer be used to transform logs
timber.remove(addCurrentUser);
```

This will remove the middleware function from _all_ future calls to `.log|info|debug|warn|error()`.

To re-add middleware, pass it to `.use()`

## Guides

### Tying Logs To Users

You can associate your logs to users by adding user context. This is achieved by [adding middleware](./#adding-middleware) immediately after logging the user in:

```javascript
import { ITimberLog } from "@timberio/types";

// In this example function, we'll add custom 'context' meta to the log
// representing the currently logged in user.
//
// Note: a middleware function is any function that takes an `ITimberLog`
// and returns a `Promise<ITimberLog>`
async function addCurrentUser(log: ITimberLog): Promise<ITimberLog> {
  return {
    ...log, // <-- copy the existing log
    user: {
      // ... and add our own `context` data
      id: 1000,
      name: "Lee",
    },
  };
}

timber.use(addCurrentUser);
```

### Tying Logs To HTTP Requests

You can associate your logs to HTTP requests by adding HTTP context. This is achieved by adding a Timber middleware within your HTTP processing pipeline:

Another common practice for Timber users is to tie their logs to HTTP requests through the HTTP request ID. That's makes it easy to logs relating to an entire HTTP transaction. We recommend [adding middleware](./#adding-middleware) at the top of your callback chain, and then [removing the middleware](./#removing-middleware) immediately after.

### Pipe Logs To An Additional Stream

Timber has the ability to pipe logs to any [writable `Stream`](https://nodejs.org/api/stream.html#stream_writable_streams). This includes [`STDOUT`](https://nodejs.org/api/process.html#process_process_stdout), [`STDERR`](https://nodejs.org/api/process.html#process_process_stderr), or even a [network Socket](https://nodejs.org/api/net.html#net_new_net_socket_options).

#### Example: Piping logs to STDOUT

{% hint style="info" %}
If you have the means to log to `STDOUT,` we highly recommend that you redirect STDOUT to Timber through one of our [platform](../../platforms/), [log forwarder](../../log-forwarders/), or [operating system](../../operating-systems/) integrations instead of shipping logs from within your app. You can read more about that [here](../../../guides/sending-logs-to-timber.md). 
{% endhint %}

Logging to `STDOUT` is as simple as piping output to the `process.stdout` stream:

```javascript
const timber = new Timber("YOUR_API_KEY", "YOUR_SOURCE_ID");
timber.pipe(process.stdout); // <-- this will send a copy to `STDOUT`

timber.log("This will also show up in the console...");
```

## Packages

Timber is a universal logging library and ships with the following pages. 

### Environment Libraries

Logging works in Node.js or the browser. Choose the appropriate lib for installation instructions. Please see the ["Which package should I install?" FAQ](./#which-package-should-i-install-to-start-logging) for more information.

| Package | What's it for? |
| :--- | :--- |
| [`@timberio/node`](https://github.com/timberio/timber-js/blob/master/packages/node) | Node.js logger |
| [`@timberio/browser`](https://github.com/timberio/timber-js/blob/master/packages/browser) | Browser logger |
| [`@timberio/js`](https://github.com/timberio/timber-js/blob/master/packages/js) | Node.js/browser logging, in a single package |

### Helper Libraries

There are a few helper libraries available that you typically won't need to use directly:

| Package | What's it for? |
| :--- | :--- |
| [`@timberio/core`](https://github.com/timberio/timber-js/blob/master/packages/core) | Core library to extend for custom loggers |
| [`@timberio/tools`](https://github.com/timberio/timber-js/blob/master/packages/tools) | Tools/utils used by loggers for throttling, batching, queuing logs |
| [`@timberio/types`](https://github.com/timberio/timber-js/blob/master/packages/types) | Shared Typescript types |

## Integrations

Timber integrates with popular 3rd party libraries such as `Winston`, `Bunyan`, `Express`, and `Koa`. Please see the Javascript integrations section:

{% page-ref page="integrations/" %}

## Performance

The Timber library provides out-the-box defaults for [batching](https://github.com/timberio/timber-js/tree/master/packages/tools#makebatchsize-number-flushtimeout-number) calls to `.log()` and [throttling](https://github.com/timberio/timber-js/tree/master/packages/tools#makethrottletmax-number) synchronization with Timber.io, aiming to provide a balance between strong performance and sensible resource usage.

We believe a logging library should be a good citizen of your stack - avoiding unnecessary slow-downs in your app due to excessive network I/O or large memory usage.

By default, the library will batch up to **1,000** logs at a time \(syncing after **1,000ms**, whichever is sooner\), and open up to **5**concurrent network requests to [Timber.io](https://timber.io/) for syncing.

These defaults can be tweaked by passing [custom options](https://github.com/timberio/timber-js/tree/master/packages/types#itimberoptions) when creating your `Timber` instance:

```javascript
const timber = new Timber("timber-organization-key", "timber-source-key", {
  // Maximum number of logs to sync in a single request to Timber.io
  batchSize: 1000,

  // Max interval (in milliseconds) before a batch of logs proceeds to syncing
  batchInterval: 1000,

  // Maximum number of sync requests to make concurrently (useful to limit
  // network I/O)
  syncMax: 100, // <-- we've increased concurrent network connections up to 100

  // Boolean to specify whether thrown errors/failed logs should be ignored
  ignoreExceptions: false,
});
```

### Metrics

This table shows the time to synchronize **10,000 logs** raised by calling `.log()` 10,000x on a 2.2Ghz Intel Core i7 Macbook Pro with 16GB of RAM based in the UK, calling the Timber.io service hosted in US-East, based on various `syncMax`connections:

| Connections | Time to sync 10k logs \(in ms\) | Improvement vs. last |
| :--- | :--- | :--- |
| 1 | 72506.09 | - |
| 2 | 34852.11 | 108.04% |
| 5 | 14488.63 | 140.55% |
| 10 | 7501.31 | 93.15% |
| 20 | 3876.49 | 93.51% |
| 50 | 2150.37 | 80.27% |
| 100 | 1736.81 | 23.81% |
| 200 | 1706.66 | 1.77% |

In general, a higher `syncMax` number \(i.e. number of concurrent throttled connections made to [Timber.io](https://timber.io/)\) will provide linear improvements in total time to sync logs vs. lower numbers.

Bear in mind:

1. This simple benchmark was performed on 10,000 immediate calls to `.log()`, which is unlikely to represent typical usage. A more common pattern in a typical app would be periodic log events, followed by periods of inactivity.
2. Although higher numbers naturally synchronize more quickly \(due to concurrent network calls to timber.io\), most apps will run sufficiently with just 1-2 open network requests since it's likely that the default `batchSize: 1000` will be enough to capture 99.9% of logging workloads within the default `batchInterval: 1000`ms time period before proceeding to sync with Timber servers.
3. Performance drops sharply after 50 concurrent connections \(and infers almost no extra benefit after 100\), likely due to the latency between the test machine and the Timber.io server.
4. A typical app won't need to consider performance; the defaults will be be sufficient.

### Recommendations

Based on your logging use-case, the following base-line recommendations can be considered when instantiating a new `Timber` instance:

| If you have... | Recommendations |
| :--- | :--- |
| A large number of logs, fired frequently, and want them to sync very quickly | Increase `syncMax` \(`50`-`100` is a good default\); lower `batchInterval` to `200`ms to emit faster |
| Logs events that occur less frequently | Decrease `batchInterval` to `100`ms, so synchronization with Timber will start within 1/10th of a second |
| An app that needs to preserve network I/O or limit outgoing requests | Drop `syncMax` to `5`; increase `batchInterval` to `2000`ms to fire less often |
| Intermittent periods of large logging activity \(and you want fast syncing\), followed by inactivity | Increase `syncMax` to `20` to 'burst' connections as needed; lower `batchSize` to match your typical log activity to emit faster |
| Low log activity, that you want to sync with Timber ASAP | Lower `batchInterval` to `10`ms, so synchronization with Timber occurs very quickly |

Or, you can simply leave the default settings in place, which will be adequate for the vast majority of applications.

### Log limits

When you log via `.log()`, you're creating a Promise that resolves when the log has been synchronized with [Timber.io](https://timber.io/)

This provides a mechanism for guaranteed consistency in your application.

While there are no hard limits on firing `.log()` events, the effective limit of concurrent logs will be determined by memory available to your Node.js V8 process \(or in the user's browser, for browser logging.\) to create a new stack for each Promise executed.

As a general rule, you might run into stack size limits beyond 100,000 - 500,000 concurrent logs -- therefore, we recommend adjusting your `syncMax`, `batchSize` and `batchInterval` settings to ensure that logs aren't sitting around in memory for long periods of time, awaiting synchronization with Timber.io.

This is a sensible strategy regardless of memory limits, to ensure logs are being written consistently to Timber and are not at risk of loss due to an app crash or server downtime.

Note: This only applies if you are working with an application that emits a large number of logs \(100,000+\) at short intervals \(within a 5-10 second window.\) 99%+ of apps will see adequate performance with the default settings.

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

To begin, please see our [log delivery troubleshooting guide](../../../guides/troubleshooting-log-delivery.md). This covers the most common issues we see with log delivery:

{% page-ref page="../../../guides/troubleshooting-log-delivery.md" %}

If the above troubleshooting guide does not resolve your issue please contact support.

