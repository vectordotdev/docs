---
description: Integrate Timber with Rack
---

# Rack

{% hint style="warning" %}
The Timber Rails integration requires [installation of the base `timber` Ruby gem](../#installation) first. Additionally, if your application is a Rails app please see the [Rails integration](rails.md) which includes the Rack integration.
{% endhint %}

Timber integrates with [Rack](https://rack.github.io/) through the `timber_rack` Ruby gem which automatically captures useful context and metadata, turning your Rack logs into rich structured events.

## Installation

1. In your `Gemfile`, add the `timber-rack` gem:  


   {% code-tabs %}
   {% code-tabs-item title="Gemfile" %}
   ```ruby
   gem 'timber-rack', '~> 1.0'
   ```
   {% endcode-tabs-item %}
   {% endcode-tabs %}

2. In your `shell` run `bundle install`
3. Use the Rack middleware:  


   ```ruby
   use Timber::Integrations::Rack::Middleware
   ```

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

| Field | Type | Description |
| :--- | :--- | :--- |
| `context.http.request_id` | `string` | Unique identifier for the request |
| `context.http.method` | `string` | Method of the HTTP request |
| `context.http.host` | `string` | Host of the HTTP request |
| `context.http.path` | `string` | Path of the HTTP request |

## Events

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

| Field | Type | Description |
| :--- | :--- | :--- |
| `http_request_received.method` | `string` | Method of the HTTP request |
| `http_request.scheme` | `string` | Scheme of the HTTP request \(`http` or `https`\) |
| `http_request_received.host` | `string` | Host of the HTTP request |
| `http_request_received.path` | `string` | Path of the HTTP request |
| `http_request_received.params_json` | `string` | JSON representation of the incoming parameters. See [this guide](../../../../guides/structured-logging-best-practices.md#keeping-your-schema-clean) to understand why we encode this data. |

{% hint style="info" %}
Where's the body? Timber purposefully leaves out the HTTP request body because it can be represented in many different formats. Instead, [we log the parameters in the `controller_called` event](rails.md#controller_called) which will be in a normalized structured.
{% endhint %}

### http\_response\_sent

The `http_response_sent` event is emitted when a HTTP response is sent back to the user.

```javascript
{
    "http_response_sent": {
        "status": 200,
        "duration_ms": 50.4
    }
}
```

| Field | Type | Description |
| :--- | :--- | :--- |
| `http_response_sent.status` | `string` | HTTP status code of the response |
| `http_response_sent.duration_ms` | `float` | Duration of processing the entire request |

