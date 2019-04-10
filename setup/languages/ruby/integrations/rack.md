---
description: Integrate Timber with Rack
---

# Rack

{% hint style="warning" %}
The Timber Rails integration requires [installation of the base `timber` Ruby gem](../#installation) first. Additionally, if your application is a Rails app please see the [Rails integration](rails.md) which includes the Rack integration.
{% endhint %}

Timber integrates with [Rack](https://rack.github.io/) through the [`timber_rack` Ruby gem](https://rubygems.org/gems/timber-rack) which captures useful context and metadata, turning your Rack logs into rich structured events.

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
3. Install the Rack middleware:  


   ```ruby
   use Timber::Integrations::Rack::HTTPContext
   use Timber::Integrations::Rack::UserContext
   use Timber::Integrations::Rack::HTTPEvents
   use Timber::Integrations::Rack::ErrorEvent
   ```

## Configuration

### collapse\_into\_single\_event

If you're a fan of [lograge](https://github.com/roidrage/lograge), you'll appreciate this setting which reduces the HTTP log events into a single event.

{% code-tabs %}
{% code-tabs-item title="config/initializers/timber.rb" %}
```ruby
Timber.config.integrations.rack.http_events.collapse_into_single_event = true
```
{% endcode-tabs-item %}
{% endcode-tabs %}

It turns this:

```text
Started GET "/"
Completed 200 OK in 1.2ms
```

Into this:

```text
GET / completed with 200 OK in 1.2ms
```

### silence\_request

Silencing noisy requests can be helpful for silencing load balance health checks, bot scanning, or activity that generally is not meaningful to you. The following will silence all `[GET] /_health` requests:

```ruby
Timber.config.integrations.rack.http_events.silence_request = lambda do |rack_env, rack_request|
  rack_request.path == "/_health"
end
```

We require a block because it gives you complete control over how you want to silence requests. The first parameter being the traditional Rack env hash, the second being a [Rack Request](http://www.rubydoc.info/gems/rack/Rack/Request) object.

### custom\_user\_hash

By default Timber automatically captures user context for most of the popular authentication libraries \(Devise, and Clearance\). See [Timber::Integrations::Rack::UserContext](http://www.rubydoc.info/github/timberio/timber-rack/Timber/Integrations/Rack/UserContext) for a complete list.

In cases where you Timber doesn't support your strategy, or you want to customize it further, you can do so like:

```ruby
Timber.config.integrations.rack.user_context.custom_user_hash = lambda do |rack_env|
  user = rack_env['warden'].user
  if user
    {
      id: user.id, # unique identifier for the user, can be an integer or string,
      name: user.name, # identifiable name for the user,
      email: user.email, # user's email address
    }
  else
    nil
  end
end
```

## Available Context

### context.http

{% hint style="warning" %}
HTTP context is only available is you install the `Timber::Integrations::Rack::HTTPContext` middleware as shown in [Installation](rack.md#installation).
{% endhint %}

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

### context.user

{% hint style="warning" %}
User context is only available is you install the `Timber::Integrations::Rack::UserContext` middleware as shown in [Installation](rack.md#installation).
{% endhint %}

User context captures structured data about the current logged in user.:

```javascript
{
    "context": {
        "user": {
            "id": "abcd1234",
            "email": "paul@bunyan.com",
            "name": "Paul Bunyan"
        }
    }
}
```

| Field | Type | Description |
| :--- | :--- | :--- |
| `context.user.id` | `string` | Unique identifier for the user |
| `context.http.email` | `string` | Email address of the user, if available. |
| `context.http.name` | `string` | Name of the user, if available. |

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

## Guides

### Adding User Context



