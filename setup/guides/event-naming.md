---
description: Best practices when writing structured logs
---

# Structured Logging Best Practices

Timber has a strong focus on structured logging, because modern developers are starting to see their logs as structured events, not text. And while the benefits of structured logs are obvious, it's not always obvious how to get started. After working with thousands of companies, and using Timber heavily within Timber itself, this document collates all of the best practices we've seen around structured logging.

## What is a structured event?

This is best explained with an example. Let's look at your basic Rails HTTP response log. It typically looks something like this:

```text
2019-02-04T12:23:34Z INFO Completed 200 OK in 79ms (Views: 78.8ms | ActiveRecord: 0.0ms)
```

While this nice to see, it's incredibly unhelpful since we don't have context. To improve the utility of this log, let's add some context:

```text
2019-02-04T12:23:34Z [host=34.235.155.83 user_id=5 path=/welcome method=GET] INFO Completed 200 OK in 79ms (Views: 78.8ms | ActiveRecord: 0.0ms)
```

This is better, but you can start to see how difficult this is to use:

1. It requires custom parsing to extract the attributes required to graph and search.
2. It's noisy and hard to read.
3. The structure and format doesn't follow any standard, which means it could change in the future and is unlikely to create consistency with other logs.

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

Much better! Representing the log in JSON format provides so many benefits:

1. No parsing rules are required to use this data in meaningful ways.
2. The format follows an official encoding format \(JSON\).

But how did we come up with the structure above? Why did we add a `"context"` key? And why did we nest the log event attributes under `"http_response_sent"`. 

## Namespace Your Events

In the example above you'll notice we namespace our HTTP response event with a `"http_response_sent"` key. This creates a dedicated namespace for this events and it's attributes. It prevents type conflicts from other events with the same keys. For example, if another event used the `"status"` key but included a string value that would clash with the integer value we're using here. All of our library documentation pages include this best practice, you can [see an example in our Elixir docs](../integrations/elixir/#structured-logging).

## Use Past-tense Verbs

Creating a consistent naming scheme for your events is important since it makes understanding the resulting schema much easier. We highly recommend adopting the past-tense verb naming schema. Instead of `http_response` use `http_response_sent` . This more descriptive of the fact that this is an immutable event. Some other examples:

* `order_placed`
* `order_updated`
* `order_canceled`
* `sql_query_executed`
* `template_rendered`

## Event Classifications

As you start to add events to your application you'll start to question where you draw the line. For example, let's say you have an online store application and you want to determine how many order were placed each day. To answer this question, you could explicitly add an `order_placed` event or you could use the `http_response_sent` event with the following conditions: `method=POST path=/orders status=201`. Both events would answer the question, so what do you do? Do you log one or both? Our answer is both, here's why:

When logging events we recommend thinking about them as falling into 2 categories: operational and business:

### Operational Events

These events are for you, the developer, to understand how your system is operating. Examples include:

* `sql_query_executed`
* `http_response_sent`
* `exception_raised`

### Business Events

These events are for business intelligence and should be used by your sales, marketing, and executive departments. They are almost always tied to a user or account. Examples include:

* `order_placed`
* `subscription_canceled`
* `payment_received`

## Pro-tips

If you followed our best practices you can do some interesting things with your structured logs. Instead of thinking of them as structured logs, start to think of them as events, and your logger becomes an [event bus](https://wikitech.wikimedia.org/wiki/EventBus).

1. Send all business events to Segment, making that data available for all kinds of interesting analytics and marketing uses.
2. Derive metrics from your events and send those metrics to your favorite metrics service. We utilize this strategy at Timber, all of our metrics are derived from events allowing us to easily pivot between the 2 since the naming scheme is consistent.

