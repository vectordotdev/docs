---
description: Access and search your logs in real-time
---

# Live Tailing

Timber's live-tail feature lets you access and search your logs in real-time. This feature is designed for simple, fast, real-time access to your logs. For complex querying please see our [SQL Querying feature](../sql-querying.md).

## Interface

Timber offers live tailing on both the Timber app as well as the Timber CLI. Please choose your interface:

{% page-ref page="app.md" %}

{% page-ref page="cli.md" %}

## Query Syntax

Timber's live tail implements a limited Lucene style syntax:

### Reserved Characters

Timber reserves characters to implement our query syntax. Literal search can be achieved by escaping the character with a `\` or wrapping the query in quotes.

| Character\(s\) | Description |
| :--- | :--- |
| `\` | Treats the following character as literal |
| `-` | Negates the following condition |
| `!` | Negates the following condition |
| `*` | Wildcard match 0 or more characters |
| `?` | Wildcard matching exactly 1 character |
| `(` and `)` | Groups conditions |
| `"` | Denotes a literal phrase, characters contained are treated literally |
| `AND` | Both conditions, on each side of `AND`, must evaluate to true |
| `OR` | One condition, on either side of `OR`, must evaluate to true |
| `:` | Attribute on the left must equal the value on the right |
| `:>` | Attribute on the left must be greater than the value on the right |
| `:>=` | Attribute on the left must be greater than or equal to the value on the right |
| `:<` | Attribute on the left must be less than the value on the right |
| `:<=` | Attribute on the left must be less than or equal to the value on the right |

### Examples

#### Text

| Example | Description |
| :--- | :--- |
| `paul bunyan` | Contain `paul` _and_ `bunyan` |
| `"paul bunyan"` | Literal search, contains the `paul bunyan` phrase exactly |
| `pau*` | Contains any word starting with `pau` followed by any number of characters |
| `pau?` | Contains any word starting with `pau` followed by 1 character |
| `pau??` | Contains any word starting with `pau` followed by 2 characters |

#### Attribute Examples

| Example | Description |
| :--- | :--- |
| `user.name:"paul bunyan"` | The `user.name` field is equal to `paul bunyan` |
| `http_response.status:>=500` | The `http_response.status` field is greater than `500` |
|  |  |

#### Operator Examples

| Example | Description |
| :--- | :--- |
| `-paul` | Does not contain `paul` |
| `!paul` | Does not contain `paul` |
| `paul AND bunyan` | Both conditions, on each side of `AND`, must evaluate to true |
| `NOT paul` | Does not contain `paul` |
| `paul OR bunyan` | One condition, on either side of `OR`, must evaluate to true |
| `(paul OR bunyan) AND ox` | Contains `paul` or `bunyan` and `ox` |

