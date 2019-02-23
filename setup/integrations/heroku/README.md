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

3. _Optionally_ improve your logs by [configuring Heroku features](./#configuration).
{% endtab %}

{% tab title="Manual" %}
1. Add the Timber [Heroku log drain](https://devcenter.heroku.com/articles/log-drains):  


   _**Be sure to replace `YOUR_API_KEY` and `YOUR_SOURCE_ID` appropriately!**_  
   \(these are displayed on your source's installation page\).  


   ```bash
    heroku drains:add https://YOUR_API_KEY@logs.timber.io/sources/YOUR_SOURCE_ID
   ```

2.  _Optionally_ improve your logs by [configuring Heroku features](./#configuration).
{% endtab %}
{% endtabs %}

**Which installation method should I use?**

For sheer simplicity we recommend the "Add-on" method. It's a one-click install and billing is handled automatically through your Heroku account. Please note that Heroku add-ons are limited to Heroku apps only, if you plan to add non-Heroku apps you'll need to register with Timber directly and use the "Manual" method.

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

### Filtering Logs

Because Heroku is a hosted platform you can't filter logs directly on Heroku. For this reason, Timber offers filtering capabilities within the Timber service itself. Head over to the "Log Filtering" section for more information.

## Automatic Context

By default, Timber adds the following context to your Heroku logs:

```javascript
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

## Automatic Events

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

{% tabs %}
{% tab title="Before" %}
```text
2010-10-06T21:51:12-07:00 heroku[router]: at=error code=H10 desc="App crashed" method=GET path="/" host=myapp.herokuapp.com fwd=17.17.17.17 dyno= connect= service= status=503 bytes=
```
{% endtab %}

{% tab title="Second Tab" %}
```javascript
{
    "source": "heroku",
    "dyno": "router",
    "level": "error",
    "code": "H10",
    "desc": "App crashed",
    "method": "GET",
    "path": "/",
    "host": "myapp.herokuapp.com",
    "fwd": "17.17.17.17",
    "status": 503
}
```
{% endtab %}
{% endtabs %}

### Routings

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

Heroku's postgres service queries that are slow or expensive, Timber parses these queries making it easier to observe and fix them.

{% tabs %}
{% tab title="Before" %}
```text
Jun 26 08:49:40 issuetriage app/postgres.29339:  [DATABASE] [41-1] LOG:  duration: 2406.615 ms  execute <unnamed>: SELECT  "issues".* FROM "issues" WHERE "issues"."repo_id" = $1 AND "issues"."state" = $2 ORDER BY created_at DESC LIMIT $3 OFFSET $4
```
{% endtab %}

{% tab title="After" %}
```javascript
{
    "dt": "2019-06-26T08:49:40Z",
    "message": "issuetriage app/postgres.29339:  [DATABASE] [41-1] LOG:  duration: 2406.615 ms  execute <unnamed>: SELECT  "issues".* FROM "issues" WHERE "issues"."repo_id" = $1 AND "issues"."state" = $2 ORDER BY created_at DESC LIMIT $3 OFFSET $4",
    "sql_query": {
        "duration_ms": 2406.615
    }
}
```
{% endtab %}
{% endtabs %}

## FAQs

### Can I invite non-Heroku users to my Timber organization?

Yes, you can invite members to your organization just like any other Timber account. [See the "Team Management" document](../../../usage/account-management/team-management.md).

### Should I add the Timber addon for each app or attach it?

We highly recommend [attaching a single Timber add-on to all of your apps](https://devcenter.heroku.com/articles/add-ons#sharing-an-add-on-between-apps). This makes capacity and billing management much easier. Plus, Timber has first-class support for Heroku's add-on attachments, making it easy to switch between apps from within the Timber console.

## Troubleshooting

To begin, please see our [log delivery troubleshooting guide](../../guides/troubleshooting-log-delivery.md). This covers the most common issues we see with log delivery:

{% page-ref page="../../guides/troubleshooting-log-delivery.md" %}

If the above troubleshooting guide does not resolve the issue then we recommend tailing your Heroku logs within your console, looking specifically for [`L` errors](https://devcenter.heroku.com/articles/error-codes#l10-drain-buffer-overflow). This signals that there are errors with log delivery:

```bash
heroku logs --tail | grep "L[0-9][0-9]"
```



