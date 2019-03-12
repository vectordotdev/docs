---
description: How Timber bills & collects payments
---

# Billing

Timber offers a flexible [subscription based](billing.md#subscriptions) billing system based on a [retention and volume matrix](billing.md#plan-structure). Timber aims to be as user friendly as possible with billing, helping you to choose the optimal plan for your usecase.

{% hint style="warning" %}
You must have [`admin` permissions](role-based-access-control.md) to access and mange your organization's billing.
{% endhint %}

## Plans

### Structure

For convenience, Timber offers a [configurable matrix of periods, volume, and retention](https://timber.io/pricing) based plans. This provides you with maximum flexibility, helping you to choose the most cost effective plan for your needs. Each facet is described below.

#### Billing Period

Each plan will specify a billing period. By default, all plans are on a 30 day period. Billing periods start when you create your account, this is your billing period anchor. If you need to adjust billing period anchor please can contact support.

#### Volume

A plan's volume is the cumulative amount of log data, in bytes, sent to your Timber account within a _single billing period_. Unlike other logging platforms, Timber's volume limits are across your entire billing period, not daily. This works in your favor since logging volume is generally smoothed out over 30 days accounting for daily fluctuations. For example, weekdays are likely to produce more logging volume than weekends.

Let's look at a real world example to explain further: If you're on a plan with a 30 day billing period, and you send 2gb of data per day, your volume for the billing period would be be 60gb \(30 \* 2\).

#### Retention

Retention is separate from volume and refers to the length of time data is stored from the time it is received. For example, if you're on a plan with a 14 day retention, and you send data on March 1, 2019 at 2pm, your data will be deleted sometime after 2pm on March 15, 2019. Timber offers liberal retention options due to the [architecture of our ingestion pipeline](../../under-the-hood/ingestion-pipeline.md). We can also offer custom retention based on your needs, just contact support.

### How Volume Is Calculated

Volume is calculated for each individual log line that you send to your Timber account. This is a byte size calculation. Because log lines can come in many forms \(JSON, msgpack, text, etc\), Timber normalizes all log lines into [`msgpack`](https://msgpack.org/) before calculating the byte size. This favors you, the customer, by removing any encoding "cruft" and ultimately producing a byte size calculation that is smaller. For example, given the following payload of log lines sent to Timber in `application/ndjson` format:

```javascript
{"level":"info","message":"This is the first log line"}
{"level":"error","message":"This is the second log line","key":"val"}
```

The calculated byte size would be `104` bytes. Here's how we calculated that. In your `sheel` run:

```bash
sudo gem install msgpack-ruby
irb
```

Then execute the following Ruby code:

```ruby
require("bundler")
require("msgpack")
line1 = MessagePack.pack({"level":"info","message":"This is the first log line"})
line2 = MessagePack.pack({"level":"error","message":"This is the second log line","key":"val"})
line1.bytesize + line2.bytesize
=> 104
```

If we were to use the raw JSON byte size we would end up with `124` bytes instead!

### Choosing The Right Plan

Timber allows you to change your plan as frequently as you like, your plan will be pro-rated across your billing period. Therefore, we recommend starting with an aggressive limit and then upgrading as you get a better feel for your usage. Timber will send you multiple notifications as you approach and exceed your limit.

## Subscriptions

### Trials

Every Timber subscription comes with a 14 day trial up to 100gb.

### Billing Periods

Your subscription operates on intervals called billing periods. By default, a single billing period is 30 days. All usage is totaled over a single billing period and resets when a new billing period starts.

### Exceeding Your Limit

Timber defines 3 water marks as you approach and exceed your limit. Each watermark will deliver a notification to your [designated system notification destination](notification-preferences.md). Each watermark is described below:

#### 80% Watermark

When you've used 80% of your [allotted volume](billing.md#volume) we'll send a notification to your designated system notification destination and display a warning within the Timber app itself.

#### 100% Watermark

When you've used 100% of your allotted volume we'll send a notification to your [designated system notification destination](notification-preferences.md) and display a stringent warning within the Timber app itself. As a courtesy, we will _not_ deny log data, providing you with more time to [adjust your plan](billing.md#changing-plans).

#### 120% Watermark

When you've used 100% of your allotted volume we'll send a notification to your [designated system notification destination](notification-preferences.md) and display a red noticed within the Timber app itself stating that we have stopped accepting logs. For cost reasons we cannot continue to accept logs until you [change your plan](billing.md#changing-plans) to one with a higher volume limit.

### Changing Plans

You can change your plan by:

1. Navigating to your organization's settings.
2. Click on the "Plan & Payment" tab.
3. Click on the "Change Plan" button.
4. Select your desired plan details.
5. Click "Update Plan".

## Payment Methods

### Changing Payment Methods

### Failed Payments

Timber will retry failed payments up to 3 times, at which point your account will be marked as delinquent and any new log data will be denied until payment is made.

### International Currencies

Timber accepts Visa, Mastercard, American Express, Discover, and Diners Club payments worldwide. There are no currency restrictions if you use a valid credit card.

## Invoices

You can view a list of all invoices by:

1. Navigating to your organization's settings section.
2. Click on the "Invoices" tab.

## Security

Timber is PCI compliant and does not retain payment information in any way. You can read more about our security in our [Security](../../under-the-hood/security.md) and [Compliance](../../under-the-hood/compliance.md) guides.

## Canceling

You may cancel your Timber account at any time. You can accomplish by either:

1. [Downgrading your plan to the free plan.](billing.md#changing-plans)
2. [Deleting your Timber organization.](deleting-your-account.md)

