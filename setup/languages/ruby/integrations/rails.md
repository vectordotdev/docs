---
description: Integrate Timber with Rails
---

# Rails

Timber integrates with Rails through the `timber_rails` Ruby gem which automatically captured useful context and metadata, turning your Rails logs into rich structured events.

## Installation

1. In your `Gemfile`, add the `timber-rails` gem:
2. In your `shell` run `bundle install`

That's it! Timber automatically installs itself through a Rails initializer. There is nothing else you need to do.

## Automatic Context

### context.http

HTTP context captures structured data on incoming HTTP requests:

```javascript
{
    "context": {
        "http": {
            "request_id": "30f14c6c1fc85cba12bfd093aa8f90e3",
            "method": "get",
            "host": "myhost.com",
            "path": "/"
        }
    }
}
```

| Field | Description |
| :--- | :--- |
| `context.http.request_id` | Unique identifier for the request |
| `context.http.method` | Method of the HTTP request |
| `context.http.host` | Host of the HTTP request |
| `context.http.path` | Path of the HTTP request |

## Events

### controller\_called

The `controller_called` event is emitted when a controller received a request.

```javascript
{
    "controller_called": {
        "controller": "MyController",
        "action": "action",
        "format": "html",
        "params_json": "{\"key\":\"val\"}"
    }
}
```

| Field | Description |
| :--- | :--- |
| `controller_called.controller` | Class name of the controller being called |
| `controller_called.action` | Action name of the action being called |
| `controller_called.format` | Format being request such as `html` or `json` |
| `controller_called.params_json` | JSON representation of the incoming parameters. See [this guide](../../../../guides/structured-logging-best-practices.md#keeping-your-schema-clean) to understand why we encode this data. |

### http\_request\_received

The `http_request_received` event is emitted when a HTTP request is received.

```javascript
{
    "http_request_received": {
        "method": "get",
        "scheme": "https",
        "host": "sub.mydomain.com",
        "path": "/"
    }
}
```

| Field | Description |
| :--- | :--- |
| `http_request_received.method` | Method of the HTTP request |
| `http_request.scheme` | Scheme of the HTTP request \(`http` or `https`\) |
| `http_request_received.host` | Host of the HTTP request |
| `http_request_received.path` | Path of the HTTP request |
| `http_request_received.params_json` | JSON representation of the incoming parameters. See [this guide](../../../../guides/structured-logging-best-practices.md#keeping-your-schema-clean) to understand why we encode this data. |

{% hint style="info" %}
Where's the body? Timber purposefully leaves out the HTTP request body because it can be represented in many different formats. Instead, [we log the parameters in the `controller_called` event](rails.md#controller_called) which will be in a normalized structured.
{% endhint %}

### http\_response\_sent

### sql\_query\_executed

### template\_rendered





