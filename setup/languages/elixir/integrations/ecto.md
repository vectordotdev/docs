---
description: Integrate Timber with the Ecto library
---

# Ecto

{% hint style="warning" %}
The Timber Ecto integration requires [installation of the base `timber` Hex package](../#installation) first.
{% endhint %}

Timber integrates with [Ecto](https://github.com/elixir-ecto/ecto) through the [`timber_ecto` Hex package](https://hex.pm/packages/timber_ecto) which automatically captures useful context and metadata, turning your Ecto logs into rich structured events.

## Installation

{% hint style="info" %}
When integrating Timber with Ecto for an umbrella application, the `:timber_ecto` library needs to be a dependency for every application that defines an Ecto Repo.
{% endhint %}

1. Ensure that you have both `:timber` \(version 3.1.0 or later\) and `:timber_ecto` listed as dependencies in `mix.exs`:  


   {% code-tabs %}
   {% code-tabs-item title="mix.exs" %}
   ```elixir
   def deps do
     [
       {:timber, "~> 3.1"},
       {:timber_ecto, "~> 2.0"}
     ]
   end
   ```
   {% endcode-tabs-item %}
   {% endcode-tabs %}

2. Run `mix deps.get` to get the dependencies.
3. Add a configuration line for every Ecto Repo. For example, if you have the application `:my_app` and the Ecto Repo `MyApp.Repo`, the configuration in `config/config.exs` would look like this:  


   {% code-tabs %}
   {% code-tabs-item title="config/config.exs" %}
   ```elixir
   use Mix.Config

   config :my_app, MyApp.Repo,
     log: false
   ```
   {% endcode-tabs-item %}
   {% endcode-tabs %}

4. Attach Timber's Telemetry event handler in your Application's `start` callback:  


   {% code-tabs %}
   {% code-tabs-item title="lib/my\_app/application.ex" %}
   ```elixir
   def start(_type, _args) do
     # ...
     :ok = :telemetry.attach(
       "timber-ecto-query-handler",
       [:my_app, :repo, :query],
       &Timber.Ecto.handle_event/4,
       []
     )
     # ...
     Supervisor.start_link(children, opts)
   end
   ```
   {% endcode-tabs-item %}
   {% endcode-tabs %}

For more information, see the documentation for the [Timber.Ecto](https://hexdocs.pm/timber_ecto/Timber.Ecto.html) module.

## Configuration

### query\_time\_ms\_threshold

Only log query that exceed a specified duration \(in milliseconds\):

{% code-tabs %}
{% code-tabs-item title="lib/my\_app/application.ex" %}
```elixir
:ok = Telemetry.attach(
  "timber-ecto-query-handler",
  [:my_app, :repo, :query],
  Timber.Ecto,
  :handle_event,
  [query_time_ms_threshold: 2_000]
  )
```
{% endcode-tabs-item %}
{% endcode-tabs %}

## Events

### sql\_query\_executed

When Ecto logs a query a `sql_query_execute` event will be logged:

```javascript
{
    // ...
    "sql_query_executed": {
        "query": "SELECT * FROM users",
        "duration_ms": 54.2
    }
    // ...
}
```

| Name | Type | Description |
| :--- | :--- | :--- |
| `sql_query_executed.query` | `string` | SQL query |
| `sql_query_executed.duration_ms` | `float` | Execution duration of the query |

