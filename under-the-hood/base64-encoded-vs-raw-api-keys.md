---
description: How Timber's API keys and source IDs relate
---

# API Keys & Source IDs

Throughout Timber's integration installation instructions you'll notice we require a set of credentials identified by `YOUR_API_KEY` and `YOUR_SOURCE_ID` . This document will explain each of these credentials parts and why we need both.

## What are Timber API keys?

[API keys](../usage/account-management/api-keys.md) provide access to [Timber's API](http://docs.api.timber.io/) for your account. All API keys are scoped to the organization they belong to. You use them in the `Authorization` header as the `Bearer` strategy within your requests to the Timber API \(see the the [HTTP](../setup/protocols/http/#examples) and [API Keys](../usage/account-management/api-keys.md) section\).

## What are Timber source IDs?

Timber's "sources" are a containers for log data and create a workspace for those logs. For example, each source has its own [schema](schema-maintenance.md), [alerts](../usage/alerting.md), and [views](../usage/saved-views.md). For more information, head over to the [source management section](../usage/source-management.md).

## Why do I need both when sending logs?

Because an organization can have many sources, and an API key works across your entire organization, Timber requires an explicit source ID to properly route the data. Otherwise Timber has no way to know which source you want to write your log data to. In additional, this makes API key management simple since you do not need to store and manage source specific API keys.

## How do I obtain an API key and source ID?

Simply [create a source](../usage/source-management.md#creating) within your organization. Your API key and source ID will be displayed on the [installation page](../usage/source-management.md#installation-instructions). __Additionally, you can [obtain an API solely](../usage/account-management/api-keys.md), but it cannot be used for log ingestion without specifying a source ID.



