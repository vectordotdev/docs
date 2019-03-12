---
description: How Timber bills
---

# Billing

Timber offers a simple and flexible billing system based on [retention and volume](billing.md#plan-structure), helping you to save money and pick the most efficient plan.

{% hint style="warning" %}
You must have [`admin` permissions](role-based-access-control.md) to access and mange your organization's billing.
{% endhint %}

## Plans

### Plan Structure

For convenience, Timber offers a [configurable matrix of volume and retention based plans](https://timber.io/pricing). This provides you with maximum flexibility, helping you to choose the most cost effective plan for your needs. Each aspect is described below.

#### Volume

A plan's volume is the cumulative amount of data, in bytes, sent within a single billing period. Unlike other platforms, Timber's plans are not daily limits, they're l For example, if you're on a 30 day plan, and you send 2gb of data per day, your volume would be 60gb \(30 \* 2\) for the billing period.

#### Retention

Retention is separate from volume and refers to the length of time data is stored from the time it is received. For example, if you're on a plan with a 14 day retention, and you send data on March 1, 2019 at 2pm, your data will be deleted sometime after 2pm on March 15, 2019.

#### Billing Period

Each plan will specify a billing period. By default, all plans are on a 30 day period.

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

T

## Subscriptions

### Trials

### Billing Periods

### Exceeding Your Limit

### Changing Plans

## Payment Methods

### Changing Payment Methods

### Failed Payments

### International Currencies

## Invoices

## Security

## Canceling

