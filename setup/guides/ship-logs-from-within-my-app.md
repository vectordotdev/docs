# Ship Logs From Within My App?

You'll notice Timber offers [language level integrations](../../under-the-hood/language-libraries.md) that ship logs directly from within your application, we also offer a [platform level integrations](../integrations/) that ship logs outside of your application. Customers often ask which method is best. This document will help clarify the differences between the two methods and help you choose the best for your needs.

## Option 1: Sending Logs From Within Your App

This method requires that you install one of Timber's [language level integrations](../../under-the-hood/language-libraries.md). Logs generated from within your app will be sent over HTTP to the Timber service.

### Pros

1. Simple to setup.
2. Does not require system level changes or installations.

### Cons

1. Potential for data loss. The Timber libraries build a small in-memory buffer of data in order to send log data in batches. Our libraries are configured to flush at 950kb or after 2 seconds. If your app crashes unexpectedly, we'll attempt a final delivery, but it is not guaranteed depending on the crash reason. Therefore, it is possible to lose up to 2 seconds of data immediately before the crash.
2. Less reliable. Delivery failures are retried 3 times and then discarded to handle back-pressure. This ensures Timber does not disrupt your application's performance, but it also means that this log data is lost.
3. Less stable. Because you're coupling log delivery with your application there is the possibility that log delivery will disrupt your application's stability. While Timber has taken every measure possible to prevent this, many times it it outside of our control.

## Option 2: Sending Logs Outside Of Your App

This method is more advanced, and requires system level understanding of concepts like `STDOUT` , log file management, disk buffering, and installing binaries / utilities.

### Pros

1. Durable. If you configure your application to write to disk your log data is durable. It will survive application crashes and restarts.
2. Reliable. Log delivery is more reliable since you're writing log data to disk and using that as a buffer. Failures can be indefinitely retried until they succeed, allowing you to continue processing through the remaining buffer on disk.
3. Stable. Log delivery is decoupled from your application removing the risk that it could affect application stability.
4. Follows the [12 factor methodology](https://12factor.net/) best practices.

### Cons

1. Difficult. This method involves more steps and requires system-level operational experience to connect all of the pieces.
2. Slower. This method is slower since there are more steps and configuration.

## What Timber Recommends

For serious production systems we _highly_ recommend shipping logs external from your application, following the [12 factor methodology](https://12factor.net/) and using one of our [integrations](../integrations/). It is worth the time and effort to invest in this method to ensure you have log data when you need it.

For everyone else, our [language libraries](../../under-the-hood/language-libraries.md) should are sufficient, especially for applications just starting out. This allows you to get up and running with very little time.



