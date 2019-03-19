---
description: Timber service limitations
---

# Limitations

To ensure proper working order of Timber we imposes limitations. Timber's limitations are liberal, it should be rare that you bump into them under normal usage.

1. Log dates cannot exceed 14 days in the past.
2. Log dates cannot exceed 2 hours in the future.
3. Each source is limited to 1000 unique columns. New columns beyond this limit are dropped. Please do not use high cardinality key names!
4. While there is no field depth restriction for Timber the resulting `.` delimited column name must be less than 256 characters.
5. All string values must be less than or equal to 8,192 bytes.
6. Column names must only contain the following characters `/^[A-Za-z0-9#$_-\@]*$/`
7. A single log frame cannot exceed 1mb in size. A frame is any batch of log lines sent to Timber, either over HTTP or other means.

## Limit Increases

If you need a limit increase, please contact support.

