---
description: 'How Timber parses, normalizes, and enriches your logs'
---

# Log Processing

This document outlines how Timber processes your logs. When you send logs to Timber it goes through various parsing and normalization rules. To get the most out of Timber you should conform to these rules as best as possible.

## Log Formats

### Generic Date Prefixes

If your log message is prefixed with a date, Timber will attempt to parse it and extract it to the [`dt` field](log-processing.md#reserved-fields). The following date formats are supported:

1. `YYYY-MM-DDTHH:MM:SS.SSSSSS`
2. `YYYY-MM-DD HH:MM:SS.SSSSSS`
3. `YYYY/MM/DDTHH:MM:SS.SSSSSS`
4. `YYYY/MM/DD HH:MM:SS.SSSSSS`

If given the choice, please format your logs in [ISO8601](https://en.wikipedia.org/wiki/ISO_8601) \(UTC\). For example, `2019-02-04T22:12:54.223354Z` , nanosecond precision is highly recommended.

#### Modifiers

Timber looks for common modifiers to help parsing accuracy:

1. Second precision is optional.
2. Any precision up to nanosecond precision is supported.
3. Second precision can be delimited with a `,` instead of a `.`
4. Timezones are optional, if omitted times are assumed to be UTC.
5. Wrapping `[...]` brackets are stripped
6. Trailing `-` delimiters are stripped
7. Trailing `:` delimiters are stripped

### Generic Level Prefixes

If your log message is prefixed with a recognized [Syslog5424 severity](https://en.wikipedia.org/wiki/Syslog#Severity_level), such as `info`, `error`, or `warn`, Timber will parse that and extract it to the [`level` and `severity` fields](log-processing.md#reserved-fields).

#### Modifiers

Timber looks for common modifiers to help parsing accuracy:

1. Wrapping `[...]` brackets are stripped
2. Trailing `-` delimiters are stripped
3. Trailing `:` delimiters are stripped

### Standard Formats

1. Apache access and error logs
2. Elixir `Logger` default format
3. JSON
4. Logfmt
5. Nginx access and error logs
6. Ruby `Logger` default format
7. Syslog5424

In addition to the above formats, Timber parses known integration specific events. Each integration will list any event it parses within [it's own documentation](../setup/integrations/).

## Magic Fields

### \*\_json

Suffxing a field name with `_json` will inform Timber to JSON encode its value. This is useful for `object` or `array` values that you do not want to index. This helps to keep your [schema](schema-maintenance.md) clean and avoid hitting the [column limit](schema-maintenance.md#limitations).

## Normalizers

### Date field normalizing

If you send a field with any of the following names Timber will that as the `dt` field:

1. `dt`
2. `timestamp`
3. `@timestamp`
4. `date`
5. `@date`
6. `time`
7. `@time`
8. `_fluent_date`

## Reserved Fields

Timber reserves the following fields:

| Name | Type | Description |
| :--- | :--- | :--- |
| `dt` | `datetime` | [ISO8601 datetime format](https://en.wikipedia.org/wiki/ISO_8601) representing when the log was written. |
| `host` | `string` | [Hostname](https://en.wikipedia.org/wiki/Hostname) representing which machine wrote the log. |
| `id` | `string` | Unique ID for the log itself. |
| `level` | `string` | Keyword value for [Syslog 5424 severity levels](https://en.wikipedia.org/wiki/Syslog#Severity_level). |
| `message` | `string` | The actual log message. |
| `priority` | `int` | [Numeric Syslog 5424 severity.](https://en.wikipedia.org/wiki/Syslog#Severity_level) |
| `_metadata` | `object` | Namespace for internal Timber metadata. |

