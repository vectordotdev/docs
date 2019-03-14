---
description: Powerful SQL querying against your structured log data
---

# SQL Querying

Timber offers [full ANSI compliant SQL querying](https://prestodb.github.io/docs/current/sql/select.html) against your structured log data. Timber utilizes [Athena](https://aws.amazon.com/athena/) \([Presto](https://prestodb.github.io/)\) under the hood, providing you with powerful SQL querying.

## Getting Started

{% tabs %}
{% tab title="CLI" %}

{% endtab %}

{% tab title="Web App" %}
{% hint style="warning" %}
SQL Querying within the Timber web app is not currently available. Please use the CLI.
{% endhint %}
{% endtab %}
{% endtabs %}

## How It Works

{% hint style="info" %}
If you haven't already, please read our [Log Ingestion document](../under-the-hood/ingestion-pipeline.md) for a deeper dive into our log ingestion pipeline.
{% endhint %}

Timber persists your data in an efficient columnar format \([ORC](https://orc.apache.org/)\) on [S3](https://aws.amazon.com/s3/) and uses [Athena](https://aws.amazon.com/athena/) \([Presto](https://prestodb.github.io/)\) to query that data. Athena utilizes thousands of CPU cores to query your data, resulting in [incredibly fast query speeds](https://tech.marksblogg.com/billion-nyc-taxi-rides-aws-athena.html).

Because SQL queries can vary in complexity they are executed asynchronously, and the status of the query is polled. This is due to the fact that SQL queries can sometimes take a while to complete. Again, this is entirely dependent on the complexity of your query and the amount of data scanned. Performance is _largely_ correlated with the amount of data scanned. You can reduce the execution time by following our [best practices](sql-querying.md#best-practices).

## Query Syntax

Timber only supports the [Presto `SELECT` syntax](https://prestodb.github.io/docs/current/sql/select.html):

```text
[ WITH with_query [, ...] ]
SELECT [ ALL | DISTINCT ] select_expr [, ...]
[ FROM from_item [, ...] ]
[ WHERE condition ]
[ GROUP BY [ ALL | DISTINCT ] grouping_element [, ...] ]
[ HAVING condition]
[ { UNION | INTERSECT | EXCEPT } [ ALL | DISTINCT ] select ]
[ ORDER BY expression [ ASC | DESC ] [, ...] ]
[ LIMIT [ count | ALL ] ]
```

Please see the [Presto `SELECT` docs](https://prestodb.github.io/docs/current/sql/select.html) for a comprehensive syntax overview.

### Tables

Your table name is formatted as `source_{id}`. Where `{id}` is replaced by your actual source ID. For example: `SELECT * FROM source_1234 LIMIT 100` would select data from source ID `1234`.

### Columns

Any column you send as part of your log data is automatically made available for querying. If you haven't already, please read out [Dynamic Schema Maintenance document](../under-the-hood/schema-maintenance.md).

### Functions

Please see the [Presto `SELECT` docs](https://prestodb.github.io/docs/current/sql/select.html) for a comprehensive overview of all functions available.

### Examples

#### 1. Retrieve the last 50 logs logs:

```sql
SELECT dt, message
FROM source_{id}
ORDER BY `dt.desc`
LIMIT 50
```

#### 2. Count logs over the past 24 hours:

```sql
SELECT COUNT(*) AS count
FROM source_{id}
WHERE dt >= (now() - interval '24' hour)
```

#### 3. Count errors by user over the last week

{% hint style="info" %}
This query assumes you have a `user.id` field.
{% endhint %}

```sql
SELECT
    `user.id` AS user_id,
    COUNT(*) AS count
FROM source_{id}
WHERE
    level = 'error' AND
    dt >= (now() - interval '1' week)
```

#### 4. Average HTTP server response times over the last 24 hours \(1 minute intervals\):

{% hint style="info" %}
This query assumes you have a `http_response_sent.duration_ms` field.
{% endhint %}

```sql
SELECT
    floor(dt - mod(dt, 60)) AS interval,
    AVG(http_response_sent.duration_ms) AS response_avg
FROM source_{id}
WHERE dt >= (now() - interval '24' hours)
GROUP BY floor(dt - mod(dt, 60))
```

#### 5. Search logs by a phrase

```sql
SELECT dt, message
FROM source_{id}
WHERE normalized_message LIKE '%sent 500%'
ORDER BY `dt.desc`
LIMIT 50
```

`normalized_message` is a special field that Timber provides. It is a downcased and ANSI formatted stripped version of the `message` field, providing for case-insensitive searching.

## Special Fields

* `dt` - Log date in fractional [Unix timestamp format](https://en.wikipedia.org/wiki/Unix_time) \(float\). The timestamp represents seconds and the fractions represent fractions of a second. Use this to efficiently narrow your queries to a specific date range.
* `normalized_message` - Down-cased and [ANSI formatting](https://en.wikipedia.org/wiki/ANSI_escape_code) stripped. Convenient for searching.
* `application_id` - The ID of the source.
* `severity` - Numerical representation of the `level` field. The value follows the [Syslog 5424 severities](https://en.wikipedia.org/wiki/Syslog#Severity_level).

## Usage Calculation

Each SQL query scans data in order to execute and return its result. The amount of data scanned is entirely dependent on the query and the data within account and will be displayed in your client after the query has finished executing.

It is very easy to write a query that will scan all of your data and exhaust your limit. Additionally, it is just as easy to write efficient queries that only scan the data necessary. Please see our querying best practices on how to do this.

## Best Practices

1. Supply a date range on the `dt` column to limit the amount of data scanned:
2. Supply a `LIMIT` to return early and reduce the amount of data returned.
3. Specify individual columns within the `SELECT` clause to reduce the amount of data scanned and returned.
4. Avoid `JOIN`s if possible.

## Downloading Results

{% tabs %}
{% tab title="CLI" %}

{% endtab %}

{% tab title="Web App" %}

{% endtab %}
{% endtabs %}

## Limitations

1. SQL queries are read-only, only `SELECT` queries are allowed.
2. Only 400% of your [billing plan's volume](account-management/billing.md#volume) can be scanned within a given billing period. See the Usage Calculation section for more information.

Please contact support for a limit increase.

