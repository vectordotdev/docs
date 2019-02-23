---
description: How Timber manages your log schema
---

# Dynamic Schema Maintenance

When you send log data to Timber, Timber maintains a dynamic schema of all the fields you send. Think of this like one big database table. Your schema is compromised of column names and types.

## How It Works

When you send a log to Timber we track and persist all of the column names and types that you send us. This is what we call a "schema". The schema is specific to each individual [source](concepts.md#source) that you setup, it is not organization wide. This gives you boundaries to reduce noise and create clean schemas depending on the source of your log data. Let's look at an example:

```javascript
{
    "dt": "2019-02-05T12:34:22Z",
    "level": "info",
    "message": "Completed 200 OK in 56.2ms",
    "context": {
        "host": "34.22.123.22",
        "user_id": 44332
    },
    "http_response_sent": {
        "status": 200,
        "duration_ms": 56.2
    }
}
```

When we receive this event we'll create the following columns for you:

| Name | Type |
| :--- | :--- |
| `dt` | `datetime` |
| `level` | `string` |
| `message` | `string` |
| `context.host` | `string` |
| `context.user_id` | `int` |
| `http_response_event.status` | `int` |
| `http_response_event.duration_ms` | `float` |

As you send more data, and more columns, we'll continue to add to this list. It's entirely dynamic, you do not need to do anything more than send us data.

## Supported Types

Timber supports the following data types:

1. `datetime` - A timestamp in the UTC format. Timber normalizes all values to UTC.
2. `string` - A binary value no more than 8,192 bytes.
3. `int` - 32-bit integer
4. `float` - Floating point number
5. `boolean` - Binary `true` or `false` value
6. `object` - As noted above, Timber flattens objects into individually typed columns.
7. `array` - Timber has limited support for arrays. All array values are automatically encoded to JSON and stored as `string` fields.

### Why are arrays encoded to JSON?

As noted in our [architecture document](architecture.md), Timber uses a columnar database for log storage that is strictly typed. Maintaining a dynamic schema for lists is impossible because all items must share the same shape and type. This is why we encode arrays to JSON. You can still access this data and [search](../usage/searching.md) this data using our searching tools.

## Column Expiration & Cleanup

Timber keeps track of when a column was last seen. If a column's last seen value exceeds the length of your plan retention we'll automatically delete the column. Nothing else is required in your part. This helps to keep your schema tidy, allowing you to freely rename your columns without consequence.

## Limitations

1. Each source is limited to 1000 columns. New columns beyond this limit are dropped. Please be sure not to use high cardinality key names as a result.
2. While there is no field depth restriction for Timber the resulting `.` delimited column name must be less than 256 characters.
3. All string values must be less than or equal to 8,192 bytes.
4. Column names must only contain the following characters `/^[A-Za-z0-9#$_-\@]*$/`

