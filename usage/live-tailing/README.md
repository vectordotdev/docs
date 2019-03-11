---
description: Live tail your Timber logs
---

# Live Tailing

Timber's live-tail feature lets you access and search your logs in real-time; logs are made available in seconds.

## Interface

Timber offers live tailing on both the Timber app as well as the Timber CLI. Please choose your interface:

{% page-ref page="app.md" %}

{% page-ref page="cli.md" %}

## Query Syntax

| Condition | Example | Description |
| :--- | :--- | :--- |
| **Keywords** | `paul bunyan` | Contains `paul` _and_ `bunyan`, _separately_. |
| **Phrases** | `"paul bunyan"` | Contains the phrase `paul bunyan`. |
| **OR** | `paul OR bunyan` | Contains `paul` _or_ `bunyan`. |
| **\* Wildcard**\(zero or _more_\) | `paul*bunyan` | Contains `paul` and `bunyan` with zero or _more_ characters inbetween. |
| **? Wildcard**\(zero or _one_\) | `paul?bunyan` | Contains `paul` and `bunyan` with zero or _one_ characters inbetween. |
| **Negation**\(single\) | `-paul` | Does _not_ contain `paul`. |
| **Negation**\(multiple\) | `-(paul bunyan)` | Does _not_ contain the phrase `paul bunyan`. |

