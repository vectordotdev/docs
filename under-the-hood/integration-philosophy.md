---
description: Shared structured data across your logs
---

# Context

Timber treats context as a _key_ component of the Timber service. This guide will outline, in more detail, what context is and how Timber helps you take advantage of it.

## What Is Context?

Context is shared structured data that relates your logs, making them much more useful. For example, your server's hostname or an HTTP request's ID are all forms of context:

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

## In-App Context

In every integration Timber aims to capture the necessary context to make your logs useful.





