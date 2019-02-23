---
description: Send Elixir logs to Timber
---

# Elixir

Timber integrates with [Elixir](https://elixir-lang.org/) through the [`:timber` Hex package](https://hex.pm/packages/timber). The Timber Elixir library features:

* **Simple Integration.** Works directly with the core [Elixir `Logger`](https://hexdocs.pm/logger/Logger.html) making it easy to integrate without code level changes.
* **Fast & light-weight.**  [Designed with performance in mind](./#performance).
* **Support for** [**local**](./#local-context-default) **and** [**global**](./#global-context) **context.**
* \*\*\*\*[**Automatic context**](./#context) **capturing.**
* **Strong** [**structured logging support**](./#structured-logging)**.**
* \*\*\*\*[**Integrates with popular 3rd party libraries**](./#integrations)**.**

## Installation

{% tabs %}
{% tab title="HTTP \(recommended\)" %}
1. In `mix.exs`, add the `:timber` dependency:  


   {% code-tabs %}
   {% code-tabs-item title="mix.exs" %}
   ```elixir
   defp deps do
     [{:timber, "~> 3.0"}]
   end
   ```
   {% endcode-tabs-item %}
   {% endcode-tabs %}

2. In `config.exs`, install the Timber logger backend with your _**raw**_ API key.  


   {% code-tabs %}
   {% code-tabs-item title="config/config.ex" %}
   ```elixir
   config :logger,
     backends: [Timber.LoggerBackends.HTTP],

   config :timber,
     api_key: "YOUR_API_KEY",
     source_id: "YOUR_SOURCE_ID"
   ```
   {% endcode-tabs-item %}
   {% endcode-tabs %}

3. In your `shell`, verify installation by running:  


   ```bash
   mix timber.test_the_pipes
   ```

4. _Optionally_ [install integrations](./#integrations) with `Plug`, `Phoenix`, `Ecto`, and more.
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

**Which installation method should I choose?**

We recommend the "HTTP" method if you are unsure. To understand why you would choose one over the other, please see the ["Ship Logs From Within My App?" guide](../../guides/ship-logs-from-within-my-app.md).

## Configuration

### Timber Configuration

All configuration options for `:timber` can be found in the [`Timber.Config` documentation](https://hexdocs.pm/timber/Timber.Config.html#content).

### Elixir Logger Configuration

All configuration options for the Elixir `Logger` can be found in the [`Logger` documentation](https://hexdocs.pm/logger/Logger.html#module-configuration).

#### Log to STDOUT in addition to Timber

{% hint style="warning" %}
If you have the means to log to `STDOUT` we highly recommend that you redirect STDOUT to Timber through one of our [integrations](../) instead of shipping logs from within your app. You can read more about that [here](../../guides/ship-logs-from-within-my-app.md). 
{% endhint %}

Logging to `:stdout` uses the Elixir provided `:console` backend. You can read more about configuring the `:console`backend [here](https://hexdocs.pm/logger/Logger.html#module-console-backend).

{% code-tabs %}
{% code-tabs-item title="config/config.exs" %}
```elixir
config :logger, backends: [Timber.LoggerBackends.HTTP, :console]
```
{% endcode-tabs-item %}
{% endcode-tabs %}

From here you can redirect `STDOUT` to a [file](../fluent-bit.md), [Syslog](../syslog.md), or any device of your choice.

## Usage

### Basic Logging

Timber works with your existing [`Logger`](https://hexdocs.pm/logger/Logger.html) calls, no changes necessary:

```elixir
Logger.info("Hello world")
```

### Structured Logging

{% hint style="info" %}
If you haven't already, please see our [structured logging best practices guide](../../guides/event-naming.md).
{% endhint %}

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

#### Local Context \(default\)

Because Elixir is a highly concurrent language, Timber context, by default, is local to the Elixir process. This makes context management easy since it dies with the process. This fits well with Erlang / Elixir concurrency patterns since processes are typically ephemeral. Adding context in this fashion is simple:

```elixir
Timber.add_context(user: %{id: "d23f6h7ffx", email: "paul@bunyan.com"})
```

If you are spawning processes and wish to copy context to that process see the ["Copying Context To Other Processes" section](./#copying-context-to-other-processes).

#### Global Context

In rare cases you'll want to set global context. Global context applies to _all_ processes and should be used with care. Setting global context follows the same pattern as local context except it must be explicitly specified as the second argument:

```elixir
Timber.add_context(build: %{commit_sha: "0529d471fa3e2eeb8556bb400ba8b2b0f497a7e1"}, :global)
```

#### Deleting Context

As stated above, you should regularly not have to remove context, as it will die with the process. In cases where you need to explicitly remove context you can do so as follows:

```elixir
# Add context with a :user root key
Timber.add_context(user: %{id: "abcd1234"})

# Delete the entire :user key
Timber.delete_context(:user)
```

Deleting global context is similar but with a second explicit argument:

```elixir
Timber.delete_context(:user, :global)
```

### Tying Logs To Users

A very common use-case for context is tying logs to users, you can accomplish this by setting user context immediately after you log the user in. This is typically done in an authentication [plug](https://hexdocs.pm/plug/readme.html):

{% code-tabs %}
{% code-tabs-item title="auth\_plug.ex" %}
```elixir
defmodule AuthPlug
    @behaviour Plug
    
    def call(conn, _default, opts \\ []) do
        # ...
        Timber.add_context(user: %{id: "abcd1234"})
        # ...
    end
end
```
{% endcode-tabs-item %}
{% endcode-tabs %}

Be careful not to include personally identifiable information for privacy reasons.

### Tying Logs To HTTP Requests

{% hint style="info" %}
Timber offers a [`Plug` and `Phoenix` integration](../) that does this automatically.
{% endhint %}

Another common use-case for context is tying logs to HTTP requests through the request ID. You can see an example of this in the [`:timber_plug` 's `HTTPContext` module](https://github.com/timberio/timber-elixir-plug/blob/master/lib/timber_plug/http_context.ex).

### Timing Code Execution

Timber offers utilities for timing code execution and including those timings in your logs. Timing in Erlang should not be done using wall clock time, instead, it should use monotonic time:

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

### Copying Context To Child Processes

As described in the ["Setting Context" section](./#setting-context), Timber's context is local to each process. This ensures that each process can maintain context specific to it's state without conflicting with other processes, but there are times where you'll want to carry context over to child processes, such as when using the [`Task` module](https://hexdocs.pm/elixir/Task.html):

```elixir
current_context = Timber.LocalContext.load()

Task.async fn ->
  Timber.LocalContext.save(current_context)
  Logger.info("Logs from a separate process")
end
```

The new process spawned with `Task.async/1` will now contain the same Timber context as its parent. Please note, that the [runtime context's](./#runtime) `vm_pid` will be overridden and remain true to the local process.

## Automatic Context

`:timber` automatically captures [context](../../../under-the-hood/concepts.md#context) to enrich your logs. If you're shipping logs from within your app you'll want to keep context enabled. If you're shipping logs external from your app you'll want to disable context that is redundant to your log shipper.

### System 

System context captures system level information such as hostname and pid:

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

Runtime context captures information about the originator of the log line. By default, the Elixir `Logger` includes [runtime metadata](https://hexdocs.pm/logger/Logger.html#module-metadata) that Timber simply takes advantage of.

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

## Integrations

Timber integrates with popular 3rd party libraries to enhance the logs they emit. Instead of emitting raw text logs, Timber's integrations augment their logs with structured data, turning them into rich structured events.

| Name | Description |
| :--- | :--- |
| \`\`[`:timber_ecto`](https://github.com/timberio/timber-elixir-ecto) | Upgrade your `Ecto` logs with context and metadata. |
| \`\`[`:timber_exceptions`](https://github.com/timberio/timber-elixir-exceptions) | Upgrade your exception logs to include useful metadata. |
| \`\`[`:timber_phoenix`](https://github.com/timberio/timber-elixir-phoenix) | Update your `Phoenix` logs with context and metadata. |
| \`\`[`:timber_plug`](https://github.com/timberio/timber-elixir-plug) | Upgrade your `Plug` logs with context and metadata. |

## Performance

Extreme care was taken into the design of `:timber` to be fast and reliable:

1. `:timber` works directly with the [Elixir `Logger`](https://hexdocs.pm/logger/master/Logger.html), automatically assuming all of the [stability and performance benefits](https://hexdocs.pm/logger/master/Logger.html#module-runtime-configuration) this provides, such as back pressure, load shedding, and defensability around `Logger` failures.
2. Log data is buffered and flushed on an interval to optimize performance and delivery.
3. The `:timber` HTTP backend uses a controlled [multi-buffer](https://en.wikipedia.org/wiki/Multiple_buffering) design to efficiently ship data to the Timber service.
4. Connections are re-used and rotated to ensure efficient delivery of log data.
5. Delivery failures are retried with an exponential backoff, maximizing successful delivery.
6. [Msgpack](https://msgpack.org/index.html) is used for payload encoding for it's superior performance and memory management.
7. The Timber service ingest endpoint is a HA servce designed to handle extreme fluctuations of volume, it responds in under 50ms to reduce back pressure.

## Troubleshooting

To begin, please see our [log delivery troubleshooting guide](../../guides/troubleshooting-log-delivery.md). This covers the most common issues we see with log delivery:

{% page-ref page="../../guides/troubleshooting-log-delivery.md" %}

If the above troubleshooting guide does not resolve your issue then we recommend enabling debug logging within the `:timber` library itself by adding this to your `config/config.ex` file:

{% code-tabs %}
{% code-tabs-item title="config/config.exs" %}
```elixir
config :timber,
  debug_io_device: :stdio
```
{% endcode-tabs-item %}
{% endcode-tabs %}

Because the `:timber` library operates within the `Logger` itself it cannot call `Log.*` statements. The above configuration routes all `:timber` debug statements to `STDOUT` , allowing you access generic output statements generated by the `:timber` library. Once you've done this you'll want to analyze these statements, looking specifically for statements on the `error` level. This should give you insight into the functioning of the `:timber` library and any problems it's experiencing.

