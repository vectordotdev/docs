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

## Configuration

Please see the [Rack configuration](rack.md#configuration) since `timber_rack` is included as part of the Rails integration.

## Automatic Context

Please see the [Rack context](rack.md#automatic-context) section since [`timber_rack`](rack.md) is included as part of the Rails integration.

## Events

### Rack events

Please see the [Rack events](rack.md#events) since `timber_rack` is included as part of the Rails integration.

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
| `controller_called.params_json` |  | The parameters being sent to the controller encoded in JSON. See [this guide](../../../../guides/structured-logging-best-practices.md#keeping-your-schema-clean) on why we encode this data to JSON. |

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

| Name | Type | Description |
| :--- | :--- | :--- |
| `sql_query_executed.query` | `string` | The SQL query executed. |
| `sql_query_executed.duration_ms` | `float` | The duration, in milliseconds, of the query execution. |

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

| Name | Type | Description |
| :--- | :--- | :--- |
| `template_rendered.name` | `string` | The name of the template rendered. |
| `template_rendered.duration_ms` | `float` | The duration, in milliseconds, that it took to render the template. |

