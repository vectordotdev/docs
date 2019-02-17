---
description: High-level overview of how Timber works
---

# How Timber Works

Timber is a hosted, maintenance free, log aggregation service. It aims to be as simple and low configuration as possible. Simply [send your log data](../usage/sending-logs.md) to Timber and use our [app](../usage/searching.md), [CLI](https://github.com/timberio/timber-cli), and [API](http://docs.api.timber.io/) to access and analyze it. To best explain how Timber works let's walk through the flow of your log data:

## 1. Data Collection

Before you can begin using Timber you must [send your log data](../usage/sending-logs.md) to it. Timber invests heavily in it's [integrations](../setup/integrations/) and aims to be as easy as possible to integrate with your platform or application. To get started, head over to our [integrations](../setup/integrations/) and [language libraries](language-libraries.md) section \(depending on your integration type\)

{% page-ref page="../setup/integrations/" %}

{% page-ref page="language-libraries.md" %}

## 2. Data Processing

When Timber receives your data is goes through various [processing](log-processing.md) steps. We work to parse, enrich, and normalize your data so that it's as easy as possible to use. To learn more please see these related docs:

{% page-ref page="log-processing.md" %}

{% page-ref page="architecture.md" %}

{% page-ref page="schema-maintenance.md" %}

## 3. Data Access & Analysis

Once your data has made it into Timber successfully you have a variety of ways of access your data.

