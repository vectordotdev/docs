---
description: High-level overview of how Timber works
---

# How Timber Works

Timber is a hosted, maintenance free, log aggregation service. Simply send your data to Timber and use our [app](../usage/searching.md), [CLI](https://github.com/timberio/timber-cli), and [API](http://docs.api.timber.io/) to access and analyze it. To best explain how Timber works let's walk through the flow of your log data:

## Data Collection

Before you can begin using Timber you must send your log data to it. Timber invests heavily in it's [integrations](../setup/integrations/) and aims to be as easy as possible to integrate with your platform or application. To get started, head over to our [integrations](../setup/integrations/) section.

{% page-ref page="../setup/integrations/" %}

## Data Processing

When Timber receives your data is goes through various processing steps. We work to parse, enrich, and normalize your data so that it's as easy as possible to use. To learn more please see these related docs:

{% page-ref page="architecture.md" %}

{% page-ref page="schema-maintenance.md" %}

## Data Access

Once your data has made it into Timber successfully you have a variety of ways of access your data.

