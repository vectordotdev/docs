---
description: Tips on reducing log volume without losing value
---

# Reducing Log Volume

Reducing log volume not only has the benefit of reducing your cost, but it also reduces log noise. Below are tips we provide to customers wondering how to reduce their log volume. They're ordered by their effort to return ratio.

## 1. Filter Logs You Don't Need

When it comes to log filtering, you have two choices: filter application side or filter Timber side. We highly recommend starting with the application side.

### Filtering Application Side

Filtering your logs on the application is much more efficient because it reduces the log burden on your app, avoiding the overhead of sending the log data to Timber entirely. There are a few ways you can do this:

1. Simply remove the log statement. If you don't find it helpful then it makes sense to remove it entirely.
2. Move the log statement to a lower level, such as `debug`, and then configure your app to log at a higher level, such as `info`. This is a great strategy because you don't lose the ability to generate that log if and when you need it. You can simply drop your log level down to `debug`.

### Filtering Timber Side

If you find that you cannot filter your log on the application side then we recommend filtering it on the Timber side. Timber offers a [log filtering feature](../usage/filtering-logs.md) that allows you to drop logs containing specific key words. More information on that here:

{% page-ref page="../usage/filtering-logs.md" %}

## 2. Sample Logs

As an alternative to filtering logs entirely, you can sample them at a rate. This simply reduces the volume, and at high numbers a small subset of logs usually provide enough visibility as to not reduce utility. Depending on your language, your sampling implementation will will differ. To keep things simple, we recommend starting with random sampling. For example, in Ruby you could do:

```ruby
if Random.rand(3) == 0
    logger.info("Log message")
end
```

This effectively reduces the log volume to 30% of the original. More advanced sampling strategies will rate limit logs based on time, or bucket logs based on a context factor.

## 3. Reduce Log Size

If 1 and 2 above do not solve your problem, then you'll need to look at ways to trim your log size. Because Timber is focused on structured logging, there is possibility that your log metadata could be larger than expected. In this case, we recommend trimming metadata to only the attributes necessary.

