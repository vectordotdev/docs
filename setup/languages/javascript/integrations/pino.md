---
description: Integrate the Pino Node logger with Timber
---

# Pino

## Installation

Pino does not offer in-process transports, which you can read more about [here](https://github.com/pinojs/pino/blob/master/docs/transports.md#in-process-transports). This is technically better but requires a separate out-of-process step to ship logs to Timber. You can read about the tradeoffs of this approach in our [Sending Logs To Timber guide](../../../../guides/sending-logs-to-timber.md). Because of this, we recommend piping `STDOUT` from your Node app to one of our [platform](../../../platforms/), [log forwarder](../../../log-forwarders/), or [operating system](../../../operating-systems/) integrations:

{% page-ref page="../../../platforms/" %}

{% page-ref page="../../../log-forwarders/" %}

{% page-ref page="../../../operating-systems/" %}

### Example: Fluent Bit

Following [Timber's Fluent Bit instructions](../../../log-forwarders/fluent-bit.md) you can capture `STDOUT` via [Fluent Bit's `stdin` input](https://docs.fluentbit.io/manual/input/stdin):

```bash
node app.js | fluent-bit -i stdin
```

## Usage

Please refer to [Pino's docs](https://github.com/pinojs/pino) on usage.

