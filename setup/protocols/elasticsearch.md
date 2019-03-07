---
description: Point existing Elasticsearch clients to Timber
---

# Elasticsearch Bulk

Timber supports the Elasticsearch [`/_bulk` endpoint](https://www.elastic.co/guide/en/elasticsearch/reference/current/docs-bulk.html) protocol, allowing you to point existing Elasticsearch clients to Timber. This makes Timber a drop in replacement for Elasticsearch ingestion.

## Installation

1. Configure your client's URL to be `https://logs.timber.io/sources/YOUR_SOURCE_ID/frames`, _**replacing `YOUR_SOURCE_ID` accordingly**_.
2. Configure your client's headers to include the `Authorization: Bearer YOUR_API_KEY`, _**replacing `YOUR_API_KEY` accordingly**_.
3. Configure your client's buffer size to flush at 950kb or after 2 seconds \(see limitations below\).

## Limitations

1. Timber does not support the `delete` and `update` Elasticsearch bulk actions. Your log data in Timber is immutable.
2. The `_index`, `_type`, and `_id` fields for the `create` and `index` actions are ignored. Timber handles all of this for you.
3. Timber does not accept request payloads larger than `1mb` in size, including headers. Timber is a real-time platform, providing real-time insight, and is designed for streaming ingestion of data.

