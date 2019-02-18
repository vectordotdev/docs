---
description: Send Heroku logs to Timber
---

# Heroku

Timber offers deep integration with [Heroku](https://heroku.com) through the [Timber Heroku add-on](https://elements.heroku.com/addons/timber-logging), supporting add-on attachments, automatic log parsing, recognition of various Heroku platform events, and more.

## Installation

{% tabs %}
{% tab title="Add-on" %}
1. Add the [Timber Heroku add-on](https://elements.heroku.com/addons/timber-logging):  


   ```bash
   heroku addons:create timber-logging:free
   ```

2. _Optionally_ [attach](https://devcenter.heroku.com/articles/managing-add-ons#using-the-command-line-interface-attaching-an-add-on-to-another-app) the Timber add-on to other apps \(repeat for all apps\):  


   ```bash
   heroku addons:attach timber-1234 -a my-app
   ```

3. Done! _Optionally_ improve your logs by [configuring Heroku features](./#configuration).

### Add-on Limitations

Because of the way Heroku add-ons are structured there are a few inherent limitations:

1. Because Heroku handles billing, you're limited to the Timber add-on plans, whereas traditional Timber accounts have a much more flexible matrix of volume and retention.
2. Because of the way Heroku handles billing, organizations created through the Heroku add-on process cannot add non-Heroku sources to their account. If you require this you'll need to signup for Timber directly and use the "Manual" installation method.
3. Authentication is delegated to Heroku, meaning to log into Timber you must do so by clicking the Timber addon within your Heroku dashboard.
{% endtab %}

{% tab title="Manual" %}
1. Add the Timber [Heroku log drain](https://devcenter.heroku.com/articles/log-drains):  


   ```bash
    heroku drains:add https://{{my-timber-api-key}}@logs.timber.io/frames
   ```

2.  _Optionally_ improve your logs by [configuring Heroku features](./#configuration).
{% endtab %}
{% endtabs %}

### Which installation method should I use?

For sheer simplicity we recommend the "Add-on" method. It's a one-click install and billing is handled automatically through your Heroku account. If any of the limitations listed are a problem, please use the "Manual" method. Beyond the limitations, there is no functional difference between the two methods.

## Configuration

### Log Runtime Metrics

Heroku offers a [log runtime metrics feature](https://devcenter.heroku.com/articles/log-runtime-metrics) that periodically drops system level stats in your logs. Timber will parse these events and help provide views on top of that data. You can enable it via:

```bash
heroku labs:enable log-runtime-metrics
heroku restart
```

### Dyno Metadata 

{% hint style="info" %}
This feature requires the use of one of our language level libraries. If you choose not to use Timber's libraries we recommend manually adding this to your log context.
{% endhint %}

Heroku offers a [dyno metadata feature ](https://devcenter.heroku.com/articles/dyno-metadata)that will automatically add environment variables describing the environment of your dyno. Timber's libraries will take advantage of this data and automatically include it in the context of your logs. You can enable it via:

```text
heroku labs:enable runtime-dyno-metadata
```

## Context

By default, Timber adds the following context to your Heroku logs:

```text
{
    "source": "app",
    "dyno_id": 4,
    "proc_type": "web"
}
```

| Field | Description |
| :--- | :--- |
| `source` | The source of the log, will be `heroku` or `app` |
| `dyno_id` | The ID of the dyno that emitted the log. |
| `proc_type` | The process type as defined in your `Procfile` |

## Events

Heroku will log useful platform events within your log stream. Timber recognizes these events and automatically parses them.

### CPU Usage Samples

If you enabled the [log runtime metrics feature](./#log-runtime-metrics) you'll begin to see CPU usage samples in your logs. Timber parses these and uses this data to help you understand your application's CPU usage.

{% tabs %}
{% tab title="Before" %}
```text
source=web.1 dyno=heroku.2808254.d97d0ea7-cf3d-411b-b453-d2943a50b456 sample#load_avg_1m=2.46 sample#load_avg_5m=1.06 sample#load_avg_15m=0.99
```
{% endtab %}

{% tab title="After" %}
```javascript
{
    "source": "web.1",
    "dyno": "heroku.2808254.d97d0ea7-cf3d-411b-b453-d2943a50b456"
    "sample#load_avg_1m": 2.46,
    "sample#load_avg_5m": 1.06,
    "sample#load_avg_15m": 0.99
}
```

You can read more about these fields in the [Heroku docs](https://devcenter.heroku.com/articles/log-runtime-metrics#cpu-load-averages).
{% endtab %}
{% endtabs %}

### Deploys

Each time your app deploys on Heroku an event is added to your logs. Timber parses this event so that you can easily search for and audit when deploys were made.

### Memory Usage Samples

If you enabled the [log runtime metrics feature](./#log-runtime-metrics) you'll begin to see meory usage samples in your logs. Timber parses these and uses this data to help you understand your application's CPU usage.

{% tabs %}
{% tab title="Before" %}
```text
source=web.1 dyno=heroku.2808254.d97d0ea7-cf3d-411b-b453-d2943a50b456 sample#memory_total=21.00MB sample#memory_rss=21.22MB sample#memory_cache=0.00MB sample#memory_swap=0.00MB sample#memory_pgpgin=348836pages sample#memory_pgpgout=343403pages
```
{% endtab %}

{% tab title="After" %}
```javascript
{
    "source": "web.1",
    "dyno": "heroku.2808254.d97d0ea7-cf3d-411b-b453-d2943a50b456"
    "sample#memory_total_mb": 21.0,
    "sample#memory_rss_mb": 21.22,
    "sample#memory_cache_mb": 0.0,
    "sample#memory_swap_mb": 0.0,
    "sample#memory_pgpgin_pages": 348836,
    "sample#memory_pgpgout_pages": 343403
}
```

You can read more about these fields in the [Heroku docs](https://devcenter.heroku.com/articles/log-runtime-metrics#memory-swap).
{% endtab %}
{% endtabs %}

### Platform Errors

All Heroku H, R, and L platform errors are parsed to help you keep tabs on your application's errors

```text
2010-10-06T21:51:12-07:00 heroku[router]: at=error code=H10 desc="App crashed" method=GET path="/" host=myapp.herokuapp.com fwd=17.17.17.17 dyno= connect= service= status=503 bytes=
```

### 

### Router  Events

Heroku's built-in router will log events every time it routes a request to your app. Timber parses these events.

{% tabs %}
{% tab title="Before" %}
```text
2012-02-07T09:43:06.123456+00:00 heroku[router]: at=info method=GET path="/stylesheets/dev-center/library.css" host=devcenter.heroku.com fwd="204.204.204.204" dyno=web.5 connect=1ms service=18ms status=200 bytes=13
```
{% endtab %}

{% tab title="After" %}
```javascript
{
    "level": "info",
    "method": "GET",
    "path": "/stylesheets/dev-center/library.css",
    "host": "devcenter.heroku.com",
    "dyno": "web.5",
    "connect_ms": 1.0,
    "service_ms": 18.0,
    "status": 200,
    "bytes": 13,
    "fwd": "204.204.204.204"
}    
```
{% endtab %}
{% endtabs %}

### Slow Queries



