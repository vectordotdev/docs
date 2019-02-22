# Sending Logs

Using Timber starts with sending your logs to the Timber service. This guide walks through the various steps to complete that as quickly as possible.

## Setup

If you haven't done so already, head over to the integrations section and follow the installation instruction for your desired platform.

{% page-ref page="../setup/integrations/" %}

## Log Formats

Timber recognizes hundreds of log formats, and for most users no changes are necessary, but in some cases it helps to adjust your log format so that Timber can properly parse your logs.

### Apache access & error logs

Timber parses the default Apache access & error logs:

{% tabs %}
{% tab title="Access" %}
```text
127.0.0.1 - frank [10/Oct/2000:13:55:36 -0700] "GET /apache_pb.gif HTTP/1.0" 200 2326
```
{% endtab %}

{% tab title="Error" %}
```text
[Fri Sep 09 10:42:29.902022 2011] [core:error] [pid 35708:tid 4328636416] [client 72.15.99.187] File does not exist: /usr/local/apache2/htdocs/favicon.ico
```
{% endtab %}
{% endtabs %}

### Elixir default log format

Timber parses the default Elixir logger format:

```text
fdsf
```

### Generic date prefixes

Timber makes a best effort to extract and parse log messages that begin with a date / timestamp. Supported formats include:

* fdsf
* fdsfds

### Generic level prefixes

| Format | Description |
| :--- | :--- |
| Apache access & error logs | `127.0.0.1 - frank [10/Oct/2000:13:55:36 -0700] "GET /apache_pb.gif HTTP/1.0" 200 2326 [Fri Sep 09 10:42:29.902022 2011] [core:error] [pid 35708:tid 4328636416] [client 72.15.99.187] File does not exist: /usr/local/apache2/htdocs/favicon.ico` |
| Elixir default log format |  |
| Generic date prefixes |  |
| Generic level prefixes |  |
| JSON |  |
| Logfmt |  |
| Nginx access & errors logs |  |
| Ruby default log format |  |
| Syslog 5424 |  |

## Special / Reserved Fields

Timber tries to be unopinionated with log formats since there is such a large variety of formats, but there are certain fields that are common to all logs. Supporting them allows us to enhance the interface making it easier for your to extract value from your logs:

| Field name | Description |
| :--- | :--- |
| `dt` | [ISO8601 datetime format](https://en.wikipedia.org/wiki/ISO_8601) representing when the log was written. |
| `host` | [Hostname](https://en.wikipedia.org/wiki/Hostname) representing which machine wrote the log. |
| `level` | Keyword value for [Syslog 5424 severity levels](https://en.wikipedia.org/wiki/Syslog#Severity_level). |
| `message` | The actual log message. |
| `priority` | [Numeric Syslog 5424 severity.](https://en.wikipedia.org/wiki/Syslog#Severity_level) |

## Limitations

1. Your schema has a number of limitations. If you haven't already, please read the [Dynamic Schema Maintenance doc ](../under-the-hood/schema-maintenance.md)and its [limitations](../under-the-hood/schema-maintenance.md#limitations).
2. The Timber ingest HTTP endpoint has a number of limitations. If you haven't already, please read the [HTTP documentation](../setup/integrations/http-api/).

