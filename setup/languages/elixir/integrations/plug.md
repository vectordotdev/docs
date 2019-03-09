# Plug

{% hint style="warning" %}
The Timber Plug integration requires [installation of the base `timber` Hex package](../#installation) first.
{% endhint %}

Timber integrates with [Plug](https://github.com/elixir-plug/plug) through the [`timber_plug` Hex package](https://hex.pm/packages/timber_plug) which automatically captures useful context and metadata, turning your Plug logs into rich structured events.

## Installation

{% hint style="info" %}
When integrating Timber with Plug for an umbrella application, the `:timber_plug` library needs to be a dependency for every application that defines a Plug endpoint.
{% endhint %}

1. Ensure that you have both `:timber` \(version 3.1.0 or later\) and `:timber_plug` listed as dependencies in `mix.exs`:  


   {% code-tabs %}
   {% code-tabs-item title="mix.exs" %}
   ```elixir
   def deps do
     [
       {:timber, "~> 3.1"},
       {:timber_plug, "~> 1.0"}
     ]
   end
   ```
   {% endcode-tabs-item %}
   {% endcode-tabs %}

2. Run `mix deps.get` to get the dependencies.
3. Add the Timber plugs in your `endpoint.exs` file\(s\):  


   {% code-tabs %}
   {% code-tabs-item title="lib/my\_app/web/endpoint.ex" %}
   ```elixir
   defmodule Odin.ClientAPI.Web.Endpoint do
     use Phoenix.Endpoint, otp_app: :odin_client_api
  
     # ...
  
     # Add these 2 lines
     plug(Timber.Plug.HTTPContext)
     plug(Timber.Plug.Event)
  
     plug(Router)
  
     # ...
   end
   ```
   {% endcode-tabs-item %}
   {% endcode-tabs %}

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

| Name | Type | Description |
| :--- | :--- | :--- |
| `context.http.request_id` | `string` | The request ID of the incoming request. |
| `context.http.method` | `float` | The method of the incoming. |
| `context.http.host` | `string` | The host of the incoming request |
| `context.http.path` | `string` | The path of the incoming request. |

## Events

### http\_request\_received

The `http_request_received` event is emitted when a HTTP request is received.

```javascript
{
    "http_request_received": {
        "method": "get",
        "scheme": "https",
        "host": "sub.mydomain.com",
        "path": "/",
        "request_id": "30f14c6c1fc85cba12bfd093aa8f90e3"
    }
}
```

| Name | Type | Description |
| :--- | :--- | :--- |
| `http_request_received.method` | `string` | The method of the incoming HTTP request. |
| `http_request_received.action` | `string` | The scheme of the incoming HTTP request. |
| `http_request_received.host` | `string` | The host of the incoming HTTP request. |
| `http_request_received.path` | `string` | The path of the incoming HTTP request. |
| `http_request_received.request_id` | `string` | The request ID of the incoming HTTP request. |

{% hint style="info" %}
Where's the body? Timber purposefully leaves out the HTTP request body because it can be represented in many different formats. Instead, [we log the parameters in the `controller_called` event](phoenix/#controller_called) which will be in a normalized structured.
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

| Name | Type | Description |
| :--- | :--- | :--- |
| `http_response_sent.status` | `int` | The HTTP status code of the response. |
| `http_response_sent.duration_ms` | `float` | The duration, in milliseconds, that it took to process and response to the entire incoming HTTP request. |

