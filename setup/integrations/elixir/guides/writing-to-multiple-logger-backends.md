---
description: 'Write logs to a file, STDOUT, or other backends in addition to Timber'
---

# Multiple Logger Backends

The [Elixir `Logger`](https://hexdocs.pm/logger/Logger.html) includes [support for logging to multiple backends](https://hexdocs.pm/logger/Logger.html#module-backends). Logging to a file or `:stdout` in addition to Timber is simple.

## STDOUT

Logging to `:stdout` uses the provided `:console` backend. You can read more about configuring the `:console`backend [here](https://hexdocs.pm/logger/Logger.html#module-console-backend).

{% code-tabs %}
{% code-tabs-item title="config/config.exs" %}
```elixir
config :logger, backends: [Timber.LoggerBackends.HTTP, :console]
```
{% endcode-tabs-item %}
{% endcode-tabs %}

## File

{% hint style="warning" %}
We highly recommended following the [12 Factor methodology](https://12factor.net/) and redirecting `STDOUT` to a file instead of writing directly to a file from your app.
{% endhint %}

1. Add [`logger_file_backend`](https://hex.pm/packages/logger_file_backend) as a dependency in your `mix.exs` file:  


   {% code-tabs %}
   {% code-tabs-item title="mix.exs" %}
   ```elixir
   defp deps do
       [{:logger_file_backend, "~> 0.10"}]
   end
   ```
   {% endcode-tabs-item %}
   {% endcode-tabs %}

2. Configure your the `:logger` to write to both destinations:  


   {% code-tabs %}
   {% code-tabs-item title="config/config.exs" %}
   ```elixir
   config :logger,
       backends: [
           Timber.LoggerBackends.HTTP,
           {LoggerFileBackend, :file_log}
       ]

   config :logger, :file_log,  path: "/var/log/my_app/app.log"
   ```
   {% endcode-tabs-item %}
   {% endcode-tabs %}



