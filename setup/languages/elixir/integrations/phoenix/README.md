---
description: Integration Timber with the Phoenix framework
---

# Phoenix

{% hint style="warning" %}
The Timber Phoenix integration requires [installation of the base `timber` Hex package](../../#installation) first.
{% endhint %}

Timber integrates with [Phoenix](https://phoenixframework.org/) through the [`timber_phoenix` Hex package](https://hex.pm/packages/timber_phoenix) which automatically captures useful context and metadata, turning your Phoenix logs into rich structured events.

## Installation

{% hint style="info" %}
When integrating Timber with Phoenix for an umbrella application, the `:timber_phoenix` library needs to be a dependency for every application that defines a Phoenix endpoint.
{% endhint %}

1. Ensure that you have both `:timber` \(version 3.1.0 or later\) and `:timber_phoenix` listed as dependencies in `mix.exs`:  


   {% code-tabs %}
   {% code-tabs-item title="mix.exs" %}
   ```elixir
   def deps do
     [
       {:timber, "~> 3.1"},
       {:timber_phoenix, "~> 1.0"}
     ]
   end
   ```
   {% endcode-tabs-item %}
   {% endcode-tabs %}

2. Run `mix deps.get` to get the dependencies.
3. Add a configuration line for every Phoenix endpoint. For example, if you have the application `:my_app`and the Phoenix endpoint `MyApp.Endpoint`, the configuration in `config/config.exs` would look like this:  


   {% code-tabs %}
   {% code-tabs-item title="config/config.exs" %}
   ```elixir
   use Mix.Config

   config :my_app, MyApp.Endpoint,
     # ...,
     instrumenters: [Timber.Phoenix],
   ```
   {% endcode-tabs-item %}
   {% endcode-tabs %}

For more information, see the documentation for the [Timber.Ecto](https://hexdocs.pm/timber_ecto/Timber.Ecto.html) module.

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

| Name | Type | Description |
| :--- | :--- | :--- |
| `controller_called.controller` | `string` | The name of the controller receiving the call. |
| `controller_called.action` | `float` | The name of the controller action receiving the call. |
| `controller_called.format` | `string` | The format of the request such as `html` or `json` |
| `controller_called.params_json` |  | The parameters being sent to the controller encoded in JSON. See [this guide](../../../../../guides/structured-logging-best-practices.md#keeping-your-schema-clean) on why we encode this data to JSON. |

### template\_rendered

The `template_rendered` event is emitted when `Phoenix.View` renders a template:

```javascript
{
    "template_rendered": {
        "name": "/path/to/template.html.eex",
        "duration_ms": 50.4
    }
}
```

| Name | Type | Description |
| :--- | :--- | :--- |
| `template_rendered.name` | `string` | The name of the template rendered. |
| `template_rendered.duration_ms` | `float` | The duration, in milliseconds, that it took to render the template. |

