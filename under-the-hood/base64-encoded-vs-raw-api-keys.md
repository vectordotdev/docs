---
description: How Timber's API keys and source IDs relate
---

# API Keys & Source IDs

Throughout Timber's integration installation instructions you'll notice we require that you specify a source ID along with your API key. This document will explain each of the concepts and why we require both.

## What are Timber API keys?

[API keys](../usage/account-management/api-keys.md) provide access to [Timber's API](http://docs.api.timber.io/) for your account. All API keys are scoped to the organization they belong to. You use them in the `Authorization` header within your requests to the Timber API \(more details in the [HTTP instruction](../setup/http-api/#examples)\).

### Obtaining an API Key

By default, an API is created with your organization. Head over to the [API keys section](../usage/account-management/api-keys.md) within your organization settings to manage them.

## What are Timber source IDs?

Timber's "sources" are a container for log data. It allows you to segment your logs around your natural boundaries. Each source has a [schema](schema-maintenance.md), [alerts](../usage/alerting.md), [views](../usage/saved-views.md), etc.

### Creating a source

Head over to the [sending logs guide]() for information on creating and setting up a source.

## Why do I need both?

Because an organization can have many sources, and an API key works across your entire organization, Timber requires an explicit source ID to properly route the data. Otherwise Timber has no way to know which source you want to write your log data to. In additional, this makes API key management simple since you do not need to store and manage source specific API keys.

