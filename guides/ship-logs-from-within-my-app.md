---
description: How to effectively send logs to Timber
---

# Sending Logs To Timber

You'll notice Timber offers a variety of methods for sending logs to Timber. While flexible, this can be confusing, and often it's hard to understand which method is best. This guides serves to help in that process, helping you to walk away with the best integration method for your scenario.

## In-App Log Delivery

In-app log delivery refers to the process of sending application log data to Timber directly within your app. For example, using [Timber's Ruby library](../setup/languages/ruby.md), you can send your Ruby app's logs directly to Timber without writing to `STDOUT` or a file. While simple, this method has a number of drawbacks:

### Pros

1. Simple to setup.
2. Does not require system level changes or installations.

### Cons

1. Potential for data loss. The Timber libraries build a small in-memory buffer of data in order to send log data in batches. Our libraries are configured to flush at 950kb or after 2 seconds. If your app crashes unexpectedly, we'll attempt a final delivery, but it is not guaranteed depending on the crash reason. Therefore, it is possible to lose up to 2 seconds of data immediately before the crash.
2. Less reliable. Delivery failures are retried 3 times and then discarded to handle back-pressure. This ensures Timber does not disrupt your application's performance, but it also means that this log data is lost.
3. Less stable. Because you're coupling log delivery with your application there is the possibility that log delivery will disrupt your application's stability. While Timber has taken every measure possible to prevent this, many times it it outside of our control.

## Platform & System Log Delivery

Platform & system log delivery refers to the process of sending log data to Timber outside of an application, such as tailing a file or forwarding Syslog logs to Timber.

### Pros

1. Durable. If you configure your application to write to disk your log data is durable. It will survive application crashes and restarts.
2. Reliable. Log delivery is more reliable since you're writing log data to disk and using that as a buffer. Failures can be indefinitely retried until they succeed, allowing you to continue processing through the remaining buffer on disk.
3. Stable. Log delivery is decoupled from your application removing the risk that it could affect application stability.
4. Follows the [12 factor methodology](https://12factor.net/) best practices.

### Cons

1. Difficult. This method involves more steps and requires system-level operational experience to connect all of the pieces.
2. Slower. This method is slower since there are more steps and configuration.

## Which One Is Best?

For serious production systems we _highly_ recommend shipping logs external from your application by integrating directly with your platform or system. It is worth the time and effort to invest in this method to ensure you have log data when you need it.

For everyone else, our [language libraries](../under-the-hood/language-libraries.md) should be sufficient, especially for applications just starting out. This allows you to get up and running with very little effort and time.

