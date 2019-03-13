---
description: Simple getting start guide
---

# Getting Started

Timber is designed to be intuitive. When you first log in, Timber will guide you through setting up your account; a guide is not necessary. For those that prefer explicit instructions please follow the steps below.

## 1. Create A Personal Account

Navigate to the [Timber app](https://app.timber.io) and create your personal account. You can signup through a social integration, such as Github or Google, or create a Timber specific account.

{% hint style="info" %}
This should be your _personal_ account, you do not need to create organization specific accounts unless you prefer. Timber offers the ability to create multiple organizations and [switch between them](usage/account-management/switching-between-orgs.md) all within the same account. 
{% endhint %}

## 2. Create An Organization

Before you can use Timber you must create an organization. An organization is the highest organizational unit within Timber. It is a workspace that contains [members](usage/account-management/team-management.md), [log sources](usage/source-management.md), and more. Your chosen organization name will also serve as your URL slug \(https://app.timber.io/slug\).

## 3. Create A Source

After creating an organization you'll want to [create your first log source](usage/source-management.md#creating). A source is simply a container for log data, such as a Kubernetes cluster, a Ruby app, or a collection of log files. You can create as many as you want and they should align with how you collect your log data.

## 4. Integrate Your Source

After creating your source Timber will display a [set of credentials](under-the-hood/base64-encoded-vs-raw-api-keys.md) \(your API key and source ID\). Use these credentials to integrate your source with Timber. 

## 4. Verify Log Delivery

Once you've integrated your source with Timber you'll want to verify log delivery using the [source debugger](usage/source-management.md#debugging). This will simply display the received log data. If you aren't seeing log data please read the [troubleshoot log delivery guide](guides/troubleshooting-log-delivery.md).

## 5. Use Your Log Data

All done! Time to start using your log data:

{% page-ref page="usage/live-tailing.md" %}

{% page-ref page="usage/sql-querying.md" %}

{% page-ref page="usage/graphing.md" %}

{% page-ref page="usage/alerting.md" %}

