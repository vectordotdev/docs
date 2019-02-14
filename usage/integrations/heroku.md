---
description: Send Heroku logs to Timber
---

# Heroku

Timber integrates with [Heroku](https://heroku.com) through [Timber's Heroku add-on](https://elements.heroku.com/addons/timber-logging).

## Installation

1. Add the [Timber Heroku add-on](https://elements.heroku.com/addons/timber-logging):  


   ```bash
   heroku addons:create timber-logging:free
   ```

2. _Optionally_ [attach](https://devcenter.heroku.com/articles/managing-add-ons#using-the-command-line-interface-attaching-an-add-on-to-another-app) the Timber add-on to multiple apps:  


   ```bash
   heroku addons:attach timber-1234 -a my-app
   ```

3. _Optionally_ improve your logs by [configuring Heroku features](heroku.md#configuration).

### Why should I attach Timber's add-in instead of adding it to each app?

Timber's Heroku add-on includes first-class support for add-on attachments. When you log into Timber you'll see all of your attached apps with the ability to conveniently switch between them without having to go back to the Heroku dashboard. We recommend this approach because it makes plan management easier. You have one add-on to manage instead of multiple.

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

