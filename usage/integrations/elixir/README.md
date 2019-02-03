---
description: Send Elixir logs to Timber
---

# Elixir

Timber integrates with [Elixir](https://elixir-lang.org/) through the [`:timber` Hex package](https://hex.pm/packages/timber). It works directly with the core [Elixir `Logger`](https://hexdocs.pm/logger/Logger.html) making it easy to integrate without code level changes.

In its most basic form, `:timber` simply ships logs from your Elixir app to Timber. In its most advanced form, `:timber` serves as a structured logging framework, turning your text-based logs into rich structured events.

## Installation

{% tabs %}
{% tab title="HTTP \(recommended\)" %}
Ship logs directly to Timber from _within_ your Elixir app. Recommended for its simplicity.

1. In your `mix.exs` file add the `:timber` dependency:  


   {% code-tabs %}
   {% code-tabs-item title="mix.exs" %}
   ```elixir
   defp deps do
     [{:timber, "~> 3.0"}]
   end
   ```
   {% endcode-tabs-item %}
   {% endcode-tabs %}

2. In your `config.exs` file install the Timber logger backend:  


   {% code-tabs %}
   {% code-tabs-item title="config/config.ex" %}
   ```elixir
   config :logger,
     backends: [Timber.LoggerBackends.HTTP],

   config :timber,
     api_key: "{{your-api-key}}"
   ```
   {% endcode-tabs-item %}
   {% endcode-tabs %}

3. In your `shell`, verify installation by running:  


   ```bash
   mix timber.test_the_pipes
   ```
{% endtab %}

{% tab title="STDOUT" %}
{% hint style="warning" %}
This method is more advanced and requires a separate step to ship logs to Timber. Basic knowledge of `STDOUT` and log management is required.
{% endhint %}

For advanced users we recommend writing logs to `STDOUT` and then shipping logs separately to Timber. This is a more complicated process but offers [reliability and durability advantages](../../guides/ship-logs-from-within-my-app.md), it also follows the widely practiced [Twelve-Factor methodology](https://12factor.net/). In this type of setup, `:timber` is not responsible for log delivery, it's only purpose is to capture context and structure your logs.

1. In your `mix.exs` file add the `:timber` dependency:  


   {% code-tabs %}
   {% code-tabs-item title="mix.exs" %}
   ```elixir
   defp deps do
     [{:timber, "~> 3.0"}]
   end
   ```
   {% endcode-tabs-item %}
   {% endcode-tabs %}

2. In your `config.exs` file install the Timber console JSON formatter:  


   {% code-tabs %}
   {% code-tabs-item title="config/config.exs" %}
   ```elixir
   config :logger,
     backends: [:console],
     utc_log: true
  
   config :logger, :console,
     format: {Timber.Formatter, :format},
     metadata: :all

   ```
   {% endcode-tabs-item %}
   {% endcode-tabs %}

3. At this point your application is writing logs to `STDOUT` in JSON format. Please choose the [appropriate platform integration instructions](../) to ship your log data.
{% endtab %}
{% endtabs %}

### Which installation method should I choose?

We recommend the "HTTP" method if you are unsure. To understand why you would choose one over the other, please see the ["Ship Logs From Within My App?" guide](../../guides/ship-logs-from-within-my-app.md).

## Configuration

All configuration options for Timber can be found in the [`Timber.Config` documentation](https://hexdocs.pm/timber/Timber.Config.html#content).

## Usage

### Basic Logging

Timber works with your existing [`Logger`](https://hexdocs.pm/logger/Logger.html) calls, no changes necessary:

## Context

`:timber` automatically captures context to enrich your logs. If you're shipping logs from within your app you'll want to keep context enabled. If you're shipping logs external from your app you'll want to disable context that is redundant to your log shipper.

### System 

System context captures system level context such as hostname and pid:

```javascript
{
    "context": {
        "system": {
            "hostname": "ec2-44-125-241-8",
            "pid": 20643
        }
    }
}
```

| Field | Description |
| :--- | :--- |
| `context.system.hostname` | System level hostname, value of [`:inet.gethostname()`](http://erlang.org/doc/man/inet.html#gethostname-0)\`\` |
| `context.system.pid` | System level process ID, value of [`System.get_pid()`](https://hexdocs.pm/elixir/System.html#get_pid/0)\`\` |

### Runtime

The Elixir `Logger` by default includes [runtime metadata](https://hexdocs.pm/logger/Logger.html#module-metadata) that Timber takes advantage of.

```javascript
{
    "context": {
        "runtime": {
            "vm_pid": "<0.9960.261>",
            "module_name": "MyModule",
            "line": 371,
            "function": "my_func/2",
            "file": "lib/my_app/my_module.ex",
            "application": "my_app"
        }
    }
}
```

| Field | Description |
| :--- | :--- |
| `context.runtime.vm_pid` | The Elixir VM pid \(not the operating system level pid\) |
| `context.runtime.module_name` | The name of the module where the log statement originated. |
| `context.runtime.line` | The line number where the log statement originated. |
| `context.runtime.file` | The file where the log statement originated. |
| `context.runtime.function` | The function where the log statement originated. |
| `content.runtime.application` | The Elixir component app where the log statement originated. |

```elixir
Logger.info("Hello world")
```

### Structured Logging

Your `Logger` calls now take an `:event` metadata key where you can pass structured data. We recommend logging data with a root level namespace as shown below to avoid type conflicts with other events. You can read more on this in our [Event Naming guide.](../../guides/event-naming.md)

```elixir
Logger.info(fn ->
    event = %{order_placed: %{order_id: "aarg23dw", total: 100.23}}
    message = "Order aarg23dw placed, total: $100.23"
    {message, event: event}
end)
```

### Setting Context

Before capturing context, please make sure Timber does not already capture the context you want \([see the Context section](./#context)\).

#### Local Context

Because Elixir is a highly concurrent language, Timber context, by default, is local to the Elixir process. This makes context management easy since it dies with the process. This fits well with Erlang / Elixir concurrency patterns since processes are typically ephemeral. Adding context in this fashion is simple:

```elixir
Timber.add_context(user: %{id: "d23f6h7ffx", email: "paul@bunyan.com"})
```

If you are spawning processes and wish to copy context to that process see the ["Copying Context To Other Processes" section](./#copying-context-to-other-processes).

#### Global Context

In rare cases you'll want to set global context. Global context applies to _all_ processes and should be used with care. Setting global context follow the same pattern as local context except it must be explicitly specified as the second argument:

```elixir
Timber.add_context(build: %{commit_sha: "0529d471fa3e2eeb8556bb400ba8b2b0f497a7e1"}, :global)
```

### Deleting Context

As stated above, you should not have to remove context, as it will die with the process. In cases where you need to remove context you can do so as follows:

```elixir
# Add context with a :user root key
Timber.addon_context(user: %{id: "abcd1234"})

# Delete the entire :user key
Timber.delete_context(:user)
```

Deleting global context is similar but with a second explicit argument:

```elixir
Timber.delete_context(:user, :global)
```

## Integrations

| Name | Description |
| :--- | :--- |
| \`\`[`:timber_ecto`](https://github.com/timberio/timber-elixir-ecto) | Upgrade your `Ecto` logs with context and metadata. |
| \`\`[`:timber_exceptions`](https://github.com/timberio/timber-elixir-exceptions) | Upgrade your exception logs to include useful metadata. |
| \`\`[`:timber_phoenix`](https://github.com/timberio/timber-elixir-phoenix) | Update your `Phoenix` logs with context and metadata. |
| \`\`[`:timber_plug`](https://github.com/timberio/timber-elixir-plug) | Upgrade your `Plug` logs with context and metadata. |

## Guides

### Metrics & Timings

For operational events, it's common to include execution timings. This allows you to use log events to gain insight into your applications' performance. This is especially useful for questions that operate on high cardinality attributes, such as a user's ID. Timber makes capturing and logging timings easy, let's run through an example of timing a HTTP request to an external service:

```elixir
timer = Timber.start_timer()

# ... issue the HTTP request ...

duration_ms = Timber.duration_ms(timer)

Logger.info(
  event = %{http_response_received: %{method: "GET", host: "api.payments.com", path: "/payment", duration_ms: duration_ms}}
  message = "Received POST https://api.payments.com/payment in 56.7ms",
  {message, event: event}
)
```

It's important that you use `Timber.start_timer/0` and `Timber.duration_ms/1` to capture timings since it uses monotonic times. This is important for accurate timings. You can read more about this in the Elixir [`System` documentation](https://hexdocs.pm/elixir/System.html#module-time).

### Copying Context To Other Processes

As described in the ["Setting Context" section](./#setting-context), Timber's context is local to each process. This ensures that each process can maintain context specific to it's state without conflicting with other processes, but there are times where you'll want to carry context over to child processes, such as when using the [`Task` module](https://hexdocs.pm/elixir/Task.html):

```elixir
current_context = Timber.LocalContext.load()

Task.async fn ->
  Timber.LocalContext.save(current_context)
  Logger.info("Logs from a separate process")
end
```

The new process spawned with `Task.async/1` will now contain the same Timber context as its parent. Please note, that the [runtime context's](./#runtime) `vm_pid` will be overridden and remain true to the local process.

## Performance

Extreme care was taken into the design of `:timber` to be fast and reliable:

1. `:timber` works directly with the [Elixir `Logger`](https://hexdocs.pm/logger/master/Logger.html), automatically assuming all of the [stability and performance benefits](https://hexdocs.pm/logger/master/Logger.html#module-runtime-configuration) this provides, such as back pressure, load shedding, and defensability around `Logger` failures.
2. Log data is buffered and flushed on an interval to optimize performance and delivery.
3. The `:timber` HTTP backend uses a controlled [multi-buffer](https://en.wikipedia.org/wiki/Multiple_buffering) design to efficiently ship data to the Timber service.
4. Connections are re-used and rotated to ensure efficient delivery of log data.
5. Delivery failures are retried with an exponential backoff, maximizing successful delivery.
6. [Msgpack](https://msgpack.org/index.html) is used for payload encoding for it's superior performance and memory management.
7. The Timber service ingest endpoint is a HA servce designed to handle extreme fluctuations of volume, it responds in under 50ms to reduce back pressure.

