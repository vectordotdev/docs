---
description: Capturing metrics & timings with Timber
---

# Timings

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

