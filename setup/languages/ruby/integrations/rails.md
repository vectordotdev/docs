---
description: Integrate Timber with Rails
---

# Rails

{% hint style="warning" %}
The Timber Rails integration requires [installation of the base `timber` Ruby gem](../#installation) first.
{% endhint %}

Timber integrates with [Rails](https://rubyonrails.org/) through the `timber_rails` Ruby gem which automatically captures useful context and metadata, turning your Rails logs into rich structured events.

## Installation

1. In your `Gemfile`, add the `timber-rails` gem:  


   {% code-tabs %}
   {% code-tabs-item title="Gemfile" %}
   ```ruby
   gem 'timber-rails', '~> 1.0'
   ```
   {% endcode-tabs-item %}
   {% endcode-tabs %}

2. In your `shell` run `bundle install`

That's it! Timber automatically installs itself through a [Rails initializer](https://api.rubyonrails.org/v2.3.8/classes/Rails/Initializer.html). There is nothing else you need to do.

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

### sql\_query\_executed

The `sql_query_executed` event is emitted when `ActiveRecord` executes a query:

```javascript
{
    "sql_query_executed": {
        "query": "SELECT * FROM users",
        "duration_ms": 50.4
    }
}
```

### template\_rendered

The `template_rendered` event is emitted when `ActionView` renders a template:

```javascript
{
    "template_rendered": {
        "name": "/path/to/template.html.erb",
        "duration_ms": 50.4
    }
}
```



