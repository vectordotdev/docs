---
description: Shared structured data across your logs
---

# Context

Timber treats context as a _key_ component of the Timber service. This guide will outline, in more detail, what context is and how Timber helps you take advantage of it.

## What Is Context?

Context is shared structured data that relates your logs, making them much more useful. For example, your server's hostname or an HTTP request's ID are all forms of context. Here's an example log with `http`, `user`, and `system` context:

```javascript
{
    "context": {
        "http": {
            "request_id": "30f14c6c1fc85cba12bfd093aa8f90e3",
            "method": "GET",
            "path": "/"
        },
        "user": {
            "id": "Thf323kHHfe4"
        },
        "system": {
            "host": "my.host.com"
        }
    },
    "dt": "2019-02-03T33:32:22.332322Z"
    "level": "info",
    "message": "Processing [GET] /",
}
```

## Automatic Context Capturing

Part of what makes Timber unique is our automatic context capturing through our integrations. All of our integrations are designed to automatically capture useful context, making it easier to search and graph your logs.

### In-App Context

Timber maintains [language level libraries](../setup/languages/) that can be installed in your app. These are not just log shippers but full-fledged structured logging libraries that automatically capture context and provide APIs for rich structured data. See our [Ruby library](../setup/languages/ruby/#automatic-context) for an example.

### System Context

If you aren't using our language level libraries Timber captures useful system level context outside of your app. This augments your logs with useful structured data like the host name, pod name, or anything else that helps describe the environment the log was generated. See our [Kubernetes integration](../setup/platforms/kubernetes.md#automatic-context) for example.

## Custom Context

Timber provides facilities to add custom context to your logs. This is helpful to extend context into your specific environments. If supported, the integration will outline how to capture custom context. So our [Ruby integration](../setup/languages/ruby/#setting-context) for example.

## Using Context

What good is context if you can't use it in meaningful ways? Timber provides a few ways to take advantage of context. For example the [structured searching](../usage/live-tailing.md#query-syntax) in our [live-tail](../usage/live-tailing.md) feature, [SQL querying](../usage/sql-querying.md), and [graphing](../usage/graphing.md) all take advantage of context and structured data.







