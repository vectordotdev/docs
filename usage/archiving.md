---
description: Archive logs in your own storage
---

# Archiving

Timber offers the ability to store your log data in compressed archives on S3. This is a great way to retain your logs long term in a cost efficient manner. Because archives are optimized 

## Getting Started

{% tabs %}
{% tab title="S3" %}
1. [Open the Timber web app](https://app.timber.io).
2. Navigate to your organization settings using the [main navigation](../clients/web-app/#2-main-navigation).
3. Select the "Archives" tab.
4. Click on the "Configure" button.
5. 6. A new [tab](../clients/web-app/#tabs) will open and live tailing will begin automatically.
7. Optionally enter a [query](live-tailing.md#query-syntax) to filter your results.
{% endtab %}
{% endtabs %}

## Downloading Archives

{% tabs %}
{% tab title="Web App" %}

{% endtab %}

{% tab title="Second Tab" %}

{% endtab %}
{% endtabs %}

## Searching Archives

{% hint style="warning" %}
Searching archives can be a very slow and tedious process. This is because archives are not optimized for searching, they are optimized for cost efficient storage. If you find yourself frequently searching your archives consider [extending your Timber plan's retention](account-management/billing.md#changing-plans).
{% endhint %}

{% tabs %}
{% tab title="S3" %}

{% endtab %}
{% endtabs %}

## How It Works

### Interval

Timber's archives are daily. Once a day Timber will roll up all of your log data into an archive and place it in your configured destination. This usually happens around 3am EST but could vary depending on other factors.

### Archive Format

Archives are _full_ dumps of your log data in in [`ndjson` format](http://ndjson.org/). Each log line includes every single field in JSON format. The resulting archive file is in `.tar.gz` \([tar](https://en.wikipedia.org/wiki/Tar_%28computing%29) [gzipped](https://en.wikipedia.org/wiki/Gzip) file\).

### Compression

## FAQs

