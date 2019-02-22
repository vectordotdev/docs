---
description: Best practices when writing structured logs
---

# Structured Logging Best Practices

{% hint style="info" %}
Timber provides [language level libraries ](../../under-the-hood/language-libraries.md)that make structured logging a breeze. If you haven't already, please [check them out](../../under-the-hood/language-libraries.md).
{% endhint %}

Timber has a strong focus on structured logging; modern developers are starting to see their logs as structured events not text. And while the benefits of structured logs are obvious, it's not always obvious how to get started. After working with thousands of companies, and using Timber heavily within Timber itself, this document collates the our structured logging best practices.

## What Is Structured Logging?

This is best explained with an example. Let's look at your basic [Rails](https://rubyonrails.org/) HTTP response log. It typically looks something like this:

```text
2019-02-04T12:23:34Z INFO Completed 200 OK in 79ms (Views: 78.8ms | ActiveRecord: 0.0ms)
```

While this is nice to see, it's incredibly unhelpful. To improve the utility of this log, let's add some context:

```text
2019-02-04T12:23:34Z [host=34.235.155.83 user_id=5 path=/welcome method=GET] INFO Completed 200 OK in 79ms (Views: 78.8ms | ActiveRecord: 0.0ms)
```

Better, but you can start to see the problems with this direction:

1. This format is arbitrary. Custom parsing is required to extract the attributes.
2. It's not human friendly, the log is noisy and hard to read.
3. There's no guarantee this format won't change, breaking downstream consumption.

This is why developers turn to structured logging. Wouldn't it be nice if this log event was represented as a simple JSON document?

```javascript
{
    "dt": "2019-02-04T12:23:34Z",
    "level": "info",
    "message": "Completed 200 OK in 79ms (Views: 78.8ms | ActiveRecord: 0.0ms)",
    "context": {
        "host": "34.235.155.83",
        "user_id": 5,
        "path": "/welcome",
        "method": "GET"
    },
    "http_response_sent": {
        "status": 200,
        "duration_ms": 79.0,
        "view_ms": 78.8,
        "active_record_ms": 0.0
    }
}
```

Representing the log in JSON format provides so many benefits:

1. No special parsing rules are required to use this data in meaningful ways. Any consumer can begin using this data.
2. The format follows an official encoding format \(JSON\).
3. For interfaces designed for humans, they can simply display the `"message` key and hide the other metadata, making the log easy to ready without sacrificing the metadata.

So how did we come up with the structure above? Why did we add a `"context"` key? And why did we nest the log event attributes under `"http_response_sent"`? We'll explain these in the following sections...

## Namespace Your Events

In the example above, you'll notice we namespace our HTTP response event with a `"http_response_sent"` root key. This creates a dedicated namespace for this event, preventing type conflicts from other events with similar key names. For example, if another event used the `"status"` key, but included a string value this would create a type conflict since our event uses an integer value. Moral of the story: namespace your event structured data with a root key.

## Naming Your Events

Creating a consistent naming scheme for your events is important since it makes understanding the resulting schema much easier. We highly recommend adopting the past-tense verb naming schema. Instead of `http_response` use `http_response_sent` . This more descriptive of the fact that this is an immutable event that happened in the past. Some other examples:

* `order_placed`
* `order_updated`
* `order_canceled`
* `sql_query_executed`
* `template_rendered`

## Classifying Your Events

As you start to add events to your application you'll start to question where you draw the line. For example, let's say you have an online store application and you want to determine how many orders were placed each day. To answer this question, you could explicitly add an `order_placed` event or you could use the `http_response_sent` event with the following conditions: `method=POST path=/orders status=201`. Which one is correct? Do you log one or both? Our answer is both, here's why:

When logging events we recommend thinking about them as falling into 2 categories: operational and business:

### Operational Events

These events are for you, the developer, to understand how your system is operating. A great way to classify these events is to ask "Would my marketing team care about this event?". Examples include:

* `sql_query_executed`
* `http_response_sent`
* `exception_raised`

### Business Events

These events are for business intelligence and should be used by your sales, marketing, and executive departments. They are almost always tied to a user or account. Examples include:

* `order_placed`
* `subscription_canceled`
* `payment_received`

### Why?

Logging both business and operational events allows you to work backwards when debugging. Very often customer issues begin with a business event. For example, if a customer is complaining that their order never shipped, you could search for the `order_shipped` business event and then work backwards to your operational events to see what went wrong.

## Use Context

Context is shared structured data that is automatically merged into your logs. It represents your application's current state and allows you to relate logs. Think of it like join data for your logs. Context, is by far, the most useful thing you can do with your logs because it allows you to segment and reduce noise. Let's look at a psuedo example:

{% code-tabs %}
{% code-tabs-item title="ruby.rb" %}
```ruby
Timber.with_context(user: %{id: "abcd1234"}) do
    logger.info("First log")
    logger.info("Second log")
end
```
{% endcode-tabs-item %}
{% endcode-tabs %}

Produces the following log events:

```javascript
{"message": "First log", "context": {"user": {"id": "abcd1234"}}}
{"message": "Second log", "context": {"user": {"id": "abcd1234"}}}
```

You can see how this can be incredibly useful to capture context like [user](event-naming.md#tie-your-logs-to-users) or [request](event-naming.md#tie-your-logs-to-requests-via-the-request-id) context.

### Keep it lightweight

Context should not contain large or complex values. Limit context to small unchanging referential data, such as IDs. Use your actual log events to contain detailed complex data.

## Pro-tips

### Tie your logs to users

This is the most popular use-case for Timber's custom context. Chances are you have users, adding user context helps to relate logs to users. If you're sensitive about security and privacy, please only assign the user ID, do not include the name or email.

### Tie your logs to requests

Including request information, such as the request ID, as context is incredibly helpful since it allows you view logs for a given request.



