---
description: How Timber's API keys and source IDs relate
---

# API Keys & Source IDs

Throughout Timber's integration installation instructions you'll notice we require that you specify a source ID along with your API key. This document will explain each of the concepts and why we require both.

## What are Timber API keys?

[API keys](../../usage/account-management/api-keys.md) provide access to [Timber's API](http://docs.api.timber.io/) for your account. All API keys are scoped to the organization they belong. You use them in the `Authorization` header within your requests to the Timber API.

## What are Timber source IDs?

Timber has a concept of "sources". A source is simply a container for log data. It allows you to segment your logs aligning the boundaries with your services. This helps to reduce noise and maintain a clean schema for each source. Making it easier to search and use your log data.

## Why do I need both?

Because an organization can have many sources Timber requires an explicit source ID to properly route the data. Otherwise Timber has no way to know which source you want to write your log data to. In additional, this makes API key management simple since you do not need to store and manage source specific API keys.

