---
description: Send Ruby logs to Timber
---

# Ruby

Timber integrates with [Ruby](https://www.ruby-lang.org) through its [`timber` Ruby gem](https://rubygems.org/gems/timber), enabling you to send Ruby logs to your Timber account The Timber Ruby library features:

* \*\*\*\*[**Simple integration. Exposes the same API as the Ruby `Logger`.**](./#installation)\*\*\*\*
* [**Performant, light-weight, and thoughtful design.**](./#performance)\*\*\*\*
* \*\*\*\*[**Support for structured logging.**](./#structured-logging)\*\*\*\*
* \*\*\*\*[**Support for context.**](./#setting-context)\*\*\*\*
* \*\*\*\*[**Automatic context capturing.**](./#automatic-context)\*\*\*\*
* \*\*\*\*[**Integrates with popular 3rd party libraries, such as Rails and Rack.**](./#integrations)\*\*\*\*

## Installation

{% hint style="info" %}
If you're unsure, we recommend installing via the "HTTP" method. To understand why you would choose one over the other, please see the ["Sending Logs To Timber" guide](../../../guides/sending-logs-to-timber.md).
{% endhint %}

{% tabs %}
{% tab title="Rails \(HTTP\)" %}
1. In your `Gemfile`, add the `timber` gems:  


   {% code-tabs %}
   {% code-tabs-item title="Gemfile" %}
   ```ruby
   gem 'timber', '~> 3.0'
   gem 'timber-rack', '~> 1.0' # optional
   gem 'timber-rails', '~> 1.0' # optional
   ```
   {% endcode-tabs-item %}
   {% endcode-tabs %}

2. In your `shell` run `bundle install`
3. Add the `config/initializers/timber.rb` file with the following contents, _**replace `YOUR_API_KEY` and `YOUR_SOURCE_ID` accordingly**_:  


   {% code-tabs %}
   {% code-tabs-item title="config/initializers/timber.rb" %}
   ```ruby
   http_device = Timber::LogDevices::HTTP.new("YOUR_API_KEY", "YOUR_SOURCE_ID")
   Rails.logger = Timber::Logger.new(http_device)
   ```
   {% endcode-tabs-item %}
   {% endcode-tabs %}

#### Test The Pipes

```bash
bundle exec rails console
```

Then run the following code:

```ruby
http_device = Timber::LogDevices::HTTP.new("YOUR_API_KEY", "YOUR_SOURCE_ID")
logger = Timber::Logger.new(http_device)
Rails.logger.info("Order #1234 placed", order_placed: {id: 1234, total: 100.54})
http_device.verify_delivery!
```
{% endtab %}

{% tab title="Rails \(STDOUT\)" %}
{% hint style="warning" %}
This method is more advanced and requires a separate step to ship logs to Timber. Basic knowledge of `STDOUT` and log management is required. For more information on the advantages of this method please see [this guide](../../../guides/sending-logs-to-timber.md).
{% endhint %}

1. In your `Gemfile`, add the `timber` gems:  


   {% code-tabs %}
   {% code-tabs-item title="Gemfile" %}
   ```ruby
   gem 'timber', '~> 3.0'
   gem 'timber-rack', '~> 1.0' # optional
   gem 'timber-rails', '~> 1.0' # optional
   ```
   {% endcode-tabs-item %}
   {% endcode-tabs %}

2. In your `shell` run `bundle install`
3. Add the `config/initializers/timber.rb` file with the following content, _**replace `YOUR_API_KEY` and `YOUR_SOURCE_ID` accordingly**_:  


   {% code-tabs %}
   {% code-tabs-item title="config/initializers/timber.rb" %}
   ```ruby
   Rails.logger = Timber::Logger.new(STDOUT)
   ```
   {% endcode-tabs-item %}
   {% endcode-tabs %}

4. _Optionally_ [install integrations](./#integrations) with `Rails`, `Rack`, and more.
5. At this point your application is writing logs to `STDOUT` in JSON format. Please choose the appropriate [platform](../../platforms/), [log forwarder](../../log-forwarders/), or [operating system](../../operating-systems/).
{% endtab %}

{% tab title="Non-Rails \(HTTP\)" %}
1. In your `Gemfile`, add the `timber` gems:  


   {% code-tabs %}
   {% code-tabs-item title="Gemfile" %}
   ```ruby
   gem 'timber', '~> 3.0'
   gem 'timber-rack', '~> 1.0' # optional
   ```
   {% endcode-tabs-item %}
   {% endcode-tabs %}

2. In your `shell` run `bundle install`
3. Instantiate the Timber logger for global use, _**replace `YOUR_API_KEY` and `YOUR_SOURCE_ID` accordingly**_:  


   ```ruby
   http_device = Timber::LogDevices::HTTP.new("YOUR_API_KEY", "YOUR_SOURCE_ID")
   LOGGER = Timber::Logger.new(http_device)
   ```

4. _Optionally_ [install integrations](./#integrations) with `Rack`, and more.
5. Verify installation by testing the pipes:  


   ```bash
   bundle console
   ```

   Then run the following code:  


   ```ruby
   http_device = Timber::LogDevices::HTTP.new("YOUR_API_KEY", "YOUR_SOURCE_ID")
   logger = Timber::Logger.new(http_device)
   logger.info("Order #1234 placed", order_placed: {id: 1234, total: 100.54})
   http_device.verify_delivery!
   ```
{% endtab %}

{% tab title="Non-Rails \(STDOUT\)" %}
{% hint style="warning" %}
This method is more advanced and requires a separate step to ship logs to Timber. Basic knowledge of `STDOUT` and log management is required. For more information on the advantages of this method please see [this guide](../../../guides/sending-logs-to-timber.md).
{% endhint %}

1. In your `Gemfile`, add the `timber` gems:  


   {% code-tabs %}
   {% code-tabs-item title="Gemfile" %}
   ```ruby
   gem 'timber', '~> 3.0'
   gem 'timber-rack', '~> 1.0' # optional
   ```
   {% endcode-tabs-item %}
   {% endcode-tabs %}

2. In your `shell` run `bundle install`
3. Instantiate the Timber logger for global use, _**replace `YOUR_API_KEY` and `YOUR_SOURCE_ID` accordingly**_:  


   ```ruby
   LOGGER = Timber::Logger.new(STDOUT)
   ```

4. _Optionally_ [install integrations](./#integrations) with `Rack`, and more.
5. At this point your application is writing logs to `STDOUT` in JSON format. Please choose the appropriate [platform](../../platforms/), [log forwarder](../../log-forwarders/), or [operating system](../../operating-systems/).
{% endtab %}
{% endtabs %}

## Configuration

### Options

All configuration options are available in the [`Timber::Config` module documentation](https://www.rubydoc.info/github/timberio/timber-ruby/Timber/Config).

### Log to STDOUT or a file in addition to Timber

{% hint style="info" %}
If you are choosing to write logs to `STDOUT` or a file we highly recommend following the "STDOUT" installation instructions and integrating Timber with your [platform](../../platforms/) or [operating system](../../operating-systems/).
{% endhint %}

#### STDOUT

Simply pass `STDOUT` as the second argument to `Timber::Logger.new`:

```ruby
http_device = Timber::LogDevices::HTTP.new("YOUR_API_KEY", "YOUR_SOURCE_ID")
logger = Timber::Logger.new(http_device, STDOUT)
```

#### File

Simply pass a file logger as the second argument to `Timber::Logger.new` :

```ruby
http_device = Timber::LogDevices::HTTP.new("YOUR_API_KEY", "YOUR_SOURCE_ID")
file_logger = Logger.new("#{Rails.root}/log/#{Rails.env}.log")
logger = Timber::Logger.new(http_device, file_logger)
```

## Usage

### Basic Logging

Log standard log messages just like you would with the Ruby `::Logger`:

```ruby
logger.info("Hello world")
```

### Structured Logging

{% hint style="info" %}
If you haven't already, please see our [structured logging best practices guide](../../../guides/structured-logging-best-practices.md).
{% endhint %}

Your `Logger` calls now take a second argument where you can pass structured data. We recommend logging data with a root level namespace as shown below to avoid type conflicts with other events. You can read more on this in our [Event Naming guide.](../../../guides/structured-logging-best-practices.md)

```ruby
logger.info(
    "Order #1234 placed",
    order_placed: {id: 1234, total: 100.54}
)
```

### Setting Context

Before capturing context, please make sure Timber does not already capture the context you want \([see the Automatic Context section](../elixir/#context)\).

```ruby
Timber.with_context(organization: {id: "abcd1234"}) do
    logger.info(
        "Subscription #1234 upgraded to $100",
        subscription_upgraded: {id: 1234, total: 100.0}
    )
end
```

### Tying Logs To Users

{% tabs %}
{% tab title="Rails" %}
Simply add [`timber-rails`](https://github.com/timberio/timber-ruby-rails) to your `Gemfile`. `timber-rails` [automatically installs middleware](https://github.com/timberio/timber-ruby-rails/blob/0c69b63cb2fd709e33a0f9bfd0656c28399af980/lib/timber-rails.rb#L57) to capture user context, there is nothing additional you need to do.
{% endtab %}

{% tab title="Non-Rails" %}
A very common use-case for context is tying logs to users, you can accomplish this by setting user context immediately after you log the user in. This is typically done in a Rack middleware. It's important to do this as high up as possible so that subsequent logs contain user context:

```ruby
class UserContextMiddleware
  def initialize(app)
    @app = app
  end                

  def call(env)
    Timber.with_context(user: {id: "abcd1234"}) do
      @app.call(env)
    end
  end
end

use UserContextMiddleware
```
{% endtab %}
{% endtabs %}

### Tying Logs To HTTP Requests

{% tabs %}
{% tab title="Rails" %}
Simply add [`timber-rails`](https://github.com/timberio/timber-ruby-rails) to your `Gemfile`. `timber-rails` [automatically installs middleware](https://github.com/timberio/timber-ruby-rails/blob/0c69b63cb2fd709e33a0f9bfd0656c28399af980/lib/timber-rails.rb#L57) to capture HTTP context, there is nothing additional you need to do.
{% endtab %}

{% tab title="Non-Rails" %}
1. Add `timber-rack` to your `Gemfile` :
2. Add the `HTTPContext` Rack middleware:
{% endtab %}
{% endtabs %}

### Timing Code Execution

A common piece of data to include in logs is code execution timings. You can do this like so:

```ruby
timer = Timber::Timer.start
# ... code to time ...
logger.info(
    "Processed background job",
    background_job: {duration_ms: timer}
)
```

## Automatic Context

`timber` automatically captures [context](../../../under-the-hood/concepts.md#context) to enrich your logs.

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
| `context.system.hostname` | System level hostname, value of `Socket.gethostname` |
| `context.system.pid` | System level process ID, value of `Process.pid` |

### Runtime

Runtime context captures information about the origin of the log line:

```javascript
{
    "context": {
        "runtime": {
            "thread_id": "70179740727560"
        }
    }
}
```

| Field | Description |
| :--- | :--- |
| `context.runtime.thread_id` | The result of `Thread.current.object_id`, helping you to segment logs by thread. |

## Integrations

Timber _optionally_ integrates with popular 3rd party libraries to enhance the logs they emit. Instead of emitting raw text logs, Timber's integrations augment their logs with structured data, turning them into rich structured events.

{% hint style="info" %}
These integrations are entirely optional. While we recommend them, they are not required to use Timber. The Timber service is designed to work with all types of logs, text or structured.
{% endhint %}

## Performance

Extreme care was taken into the design of `timber` to be fast and reliable:

1. Log data is buffered and flushed on an interval to optimize performance and delivery.
2. The `timber` HTTP backend uses a controlled [multi-buffer](https://en.wikipedia.org/wiki/Multiple_buffering) design to efficiently ship data to the Timber service.
3. Connections are re-used and rotated to ensure efficient delivery of log data.
4. Delivery failures are retried with an exponential backoff, maximizing successful delivery.
5. [Msgpack](https://msgpack.org/index.html) is used for payload encoding for it's superior performance and memory management.
6. The Timber service ingest endpoint is a HA service designed to handle extreme fluctuations of volume, it responds in under 50ms to reduce back pressure.

## FAQs

### Will installing Timber affect my application's performance?

No. Timber was designed with a [keen focus on performance](../elixir/#performance). It leans heavily on a light-weight asynchronous log processing design. Timber is also battle tested on hundreds of Ruby apps since it launched multiple years ago.

### Can Timber crash my app?

No, Timber is wrapped in a `try` / `catch` block to ensure exceptions do not bubble up to your app.

## Troubleshooting

To begin, please see our [log delivery troubleshooting guide](../../../guides/troubleshooting-log-delivery.md). This covers the most common issues we see with log delivery:

{% page-ref page="../../../guides/troubleshooting-log-delivery.md" %}

If the above troubleshooting guide does not resolve your issue then we recommend enabling debug logging within the `timber` library itself by adding this to your `config/initializers/timber.rb` file:

{% code-tabs %}
{% code-tabs-item title="config/initializers/timber.rb" %}
```elixir
Timber::Config.instance.debug_to_stdout!()
```
{% endcode-tabs-item %}
{% endcode-tabs %}

This will print internal debug messages to `STDOUT`. Alternatively, you can debug to a file instead:

{% code-tabs %}
{% code-tabs-item title="config/initializers/timber.rb" %}
```ruby
Timber::Config.instance.debug_to_file!("/var/log/timber.log")
```
{% endcode-tabs-item %}
{% endcode-tabs %}

