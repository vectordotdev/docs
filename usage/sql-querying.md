---
description: Powerful SQL querying against your structured log data
---

# SQL Querying

Timber offers [full ANSI compliant SQL querying](https://prestodb.github.io/docs/current/sql/select.html) against your structured log data, providing you with powerful unrestricted access to your log data. Query your log data just as you would any SQL compliant database.

{% hint style="info" %}
This feature is designed for complex long-term querying. Data is made available on 15 minute intervals. For real-time access please see the [Live Tailing & Searching feature](live-tailing.md).
{% endhint %}

## Getting Started

{% hint style="warning" %}
SQL Querying within the [Timber web app](../clients/web-app/) is not currently available. The instructions below use the [Timber CLI](../clients/cli/) which does support SQL querying.
{% endhint %}

1. [Install the `timber` CLI.](../clients/cli/#installation)
2. List your available sources:  


   ```bash
   timber sources
   ```

3. Execute the `sql-query` command using your chosen source ID as the [table name](sql-querying.md#tables):  


   ```bash
   timber sql-query execute "
   SELECT dt, message
   FROM source_{source_id}
   LIMIT 10
   "
   ```

## Query Syntax

### Definition

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

### Table Names

Your table name is formatted as `source_{id}`. Where `{id}` is replaced by your actual source ID. For example: `SELECT * FROM source_1234 LIMIT 100` would select data from source ID `1234`.

### Column Names

Any column you send as part of your log data is automatically made available for querying. If you haven't already, please read out [Dynamic Schema Maintenance document](../under-the-hood/schema-maintenance.md).

Nested columns are delimited by a `.`. For example `context.user.id` . When specifying columns with a `.` be sure to quote the name with \` characters. For example:

```sql
SELECT `context.user.id`
FROM source_{id}
LIMIT 10
```

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
    AVG(`http_response_sent.duration_ms`) AS response_avg
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

## Special Columns

* `dt` - Log date in fractional [Unix timestamp format](https://en.wikipedia.org/wiki/Unix_time) \(float\). The timestamp represents seconds and the fractions represent fractions of a second. Use this to efficiently narrow your queries to a specific date range.
* `normalized_message` - Down-cased and [ANSI formatting](https://en.wikipedia.org/wiki/ANSI_escape_code) stripped. Convenient for searching.
* `application_id` - The ID of the source.
* `severity` - Numerical representation of the `level` field. The value follows the [Syslog 5424 severities](https://en.wikipedia.org/wiki/Syslog#Severity_level).

## Usage Calculation

Each SQL query scans data in order to execute and return its result. The amount of data scanned is entirely dependent on the query and the data within your account. The data scanned will be displayed in your client after the query has finished executing.

It is very easy to write a query that will scan all of your data and exhaust your limit. Additionally, it is just as easy to write efficient queries that only scan the smallest amount of data necessary. Please see our [querying best practices](sql-querying.md#best-practices) on how to do this.

## Best Practices

1. Supply a date range on the `dt` column to limit the amount of data scanned, otherwise all data within your account will be scanned.
2. Supply a `LIMIT` to return early and reduce the amount of data returned.
3. Specify individual columns within the `SELECT` clause to reduce the amount of data scanned and returned. Avoid `*`.
4. Avoid `JOIN`s if possible.

## Downloading Results

{% tabs %}
{% tab title="CLI" %}

{% endtab %}

{% tab title="Web App" %}

{% endtab %}
{% endtabs %}

## How It Works

{% hint style="info" %}
If you haven't already, please read our [Log Ingestion document](../under-the-hood/ingestion-pipeline.md) for a deeper dive into our log ingestion pipeline.
{% endhint %}

### Architecture

Timber persists your data in an efficient [columnar format](https://en.wikipedia.org/wiki/Column-oriented_DBMS) \([ORC](https://orc.apache.org/)\) on [S3](https://aws.amazon.com/s3/) and uses [Athena](https://aws.amazon.com/athena/) \([Presto](https://prestodb.github.io/)\) to query that data. Athena utilizes thousands of CPU cores to query your data, resulting in [incredibly fast query speeds](https://tech.marksblogg.com/billion-nyc-taxi-rides-aws-athena.html).

### Performance

Timber's S3 / SQL querying pipeline is built to extract every possible performance benefit. This is the result of hard won experience building and maintaining big data S3 pipelines. With Timber you get all of this as part of your account:

1. Timber [dynamically maintains a schema](../under-the-hood/schema-maintenance.md) for each of your sources.
2. Because we maintains a consistent schema we're able to write your data in compressed ORC columnar format for efficient data retrieval.
3. Hourly partitions are used for high-level granular access, helping to reduce the amount of data scanned for each query.

### Asynchronous Processing

Because SQL queries can vary in complexity, they are executed asynchronously, and the status of the query is polled. This is due to the fact that SQL queries can sometimes take a while to complete. This is entirely dependent on the complexity of your query and the amount of data scanned. Performance is _largely_ correlated with the amount of data scanned. You can reduce the execution time by following our [best practices](sql-querying.md#best-practices).

### Data Availability

Timber's S3 / SQL Querying pipeline flushes data on 15 minute intervals, meaning data can be delayed up to 15 minutes before being made available for SQL querying. If you need real-time access please see the [Live Tailing & Search feature](live-tailing.md).

## Limitations

1. SQL queries are read-only, only `SELECT` queries are allowed.
2. Execution time cannot exceed 10 minutes. Beyond this the query will be canceled.
3. Only 10 X your [billing plan's volume](account-management/billing.md#volume) can be scanned within a given billing period. For example, if you have a 10gb [volume billing plan](account-management/billing.md#volume), you can only execute up to 100GB in cumulative data scanned within a [single billing period](account-management/billing.md#billing-period). See the [Usage Calculation section](sql-querying.md#usage-calculation) for more information.

Please contact support if you want to inquire about a limit increase.

