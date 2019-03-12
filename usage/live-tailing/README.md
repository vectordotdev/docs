---
description: Access and search your logs in real-time
---

# Live Tailing

Timber's live-tail feature lets you access and search your logs in real-time. This feature is designed for simple, fast, real-time access to your logs. For complex querying please see our [SQL Querying feature](../sql-querying.md).

## Starting

{% tabs %}
{% tab title="App" %}

{% endtab %}

{% tab title="CLI" %}

{% endtab %}
{% endtabs %}

## Searching

### Query Syntax

Timber's live tail implements a limited Lucene style syntax:

#### Reserved Characters

Timber reserves the following characters to implement our query syntax. Reserved characters can be escaped with a `\` or wrapped in `"` to be treated literally.

| Character\(s\) | Description |
| :--- | :--- |
| `\` | Treats the following character as literal |
| `-` | Negates the following condition |
| `!` | Negates the following condition |
| `*` | Wildcard matching 0 or more characters |
| `?` | Wildcard matching _exactly_ 1 character |
| `(...)` | Groups conditions for specificity |
| `"..."` | Denotes a literal phrase, characters contained are treated literally |
| `AND` | Both conditions, on each side of `AND`, must evaluate to true |
| `OR` | One condition, on either side of `OR`, must evaluate to true |
| `:` | Attribute on the left must equal the value on the right |
| `:-` | Attribute on the left does not equal the value on the right |
| `:>` | Attribute on the left must be greater than the value on the right |
| `:>=` | Attribute on the left must be greater than or equal to the value on the right |
| `:<` | Attribute on the left must be less than the value on the right |
| `:<=` | Attribute on the left must be less than or equal to the value on the right |

#### Text Searching Examples

| Example | Description |
| :--- | :--- |
| `paul bunyan` | Contain `paul` _and_ `bunyan` |
| `"paul bunyan"` | Literal search, contains the `paul bunyan` phrase exactly |
| `pau*` | Contains any word starting with `pau` followed by any number of characters |
| `pau?` | Contains any word starting with `pau` followed by 1 character |
| `pau??` | Contains any word starting with `pau` followed by 2 characters |

#### Attribute Searching Example

Attributes are any field you send with your log data. Nested fields accessed with a `.` delimited path:

| Example | Description |
| :--- | :--- |
| `user.name:"paul bunyan"` | The `user.name` field is equal to `paul bunyan` |
| `http_response_sent.status:>=500` | The `http_response_sent.status` field is greater than `500` |
| `http_respopnse_sent.status:-500` | The `http_response_sent.status` field does not equal `500` |

#### Operator Examples

| Example | Description |
| :--- | :--- |
| `-paul` | Does not contain `paul` |
| `!paul` | Does not contain `paul` |
| `paul AND bunyan` | Both conditions, on each side of `AND`, must evaluate to true |
| `NOT paul` | Does not contain `paul` |
| `paul OR bunyan` | One condition, on either side of `OR`, must evaluate to true |
| `(paul OR bunyan) AND ox` | Contains `paul` or `bunyan,` and `ox` |
| `-(paul OR bunyan) AND ox` | Does not contain `paul` or `bunyan,` and contains `ox` |

### Within A Date Range

### Faceting

### Line Format

## Jumping To A Date

## Viewing Context



