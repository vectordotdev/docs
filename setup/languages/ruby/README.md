---
description: Send Ruby logs to Timber
---

# Ruby

Timber integrates with Ruby through its [`timber` Ruby gem](https://rubygems.org/gems/timber), enabling you to send R logs to your Timber account The Timber Ruby library features:

* Simple integration. Exposes the same API as the Ruby `Logger`.
* Performant, light-weight, and thoughtful design.
* Support for structured logging.
* Support for context.
* Automatic context capturing.
* Integrates with popular 3rd party libraries, such as Rails and Rack.

## Installation

{% hint style="info" %}
If you're unsure, we recommend installing via the "HTTP" method. To understand why you would choose one over the other, please see the ["Sending Logs To Timber" guide](../../../guides/sending-logs-to-timber.md).
{% endhint %}

{% tabs %}
{% tab title="Rails \(HTTP\)" %}
1. In your `Gemfile`, add the `timber` gem:  


   {% code-tabs %}
   {% code-tabs-item title="Gemfile" %}
   ```ruby
   gem 'timber', '~> 3.0'
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

4. _Optionally_ [install integrations](./#integrations) with `Rails`, `Rack`, and more.
5. Verify installation by testing the pipes:  


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

1. In your `Gemfile`, add the `timber` gem:  


   {% code-tabs %}
   {% code-tabs-item title="Gemfile" %}
   ```ruby
   gem 'timber', '~> 3.0'
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
1. In your `Gemfile`, add the `timber` gem:  


   {% code-tabs %}
   {% code-tabs-item title="Gemfile" %}
   ```ruby
   gem 'timber', '~> 3.0'
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
1. In your `Gemfile`, add the `timber` gem:  


   {% code-tabs %}
   {% code-tabs-item title="Gemfile" %}
   ```ruby
   gem 'timber', '~> 3.0'
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

## Integrations

Timber _optionally_ integrates with popular 3rd party libraries to enhance the logs they emit. Instead of emitting raw text logs, Timber's integrations augment their logs with structured data, turning them into rich structured events.

{% hint style="info" %}
These integrations are entirely optional. While we recommend them, they are not required to use Timber. The Timber service is designed to work with all types of logs, text or structured.
{% endhint %}

