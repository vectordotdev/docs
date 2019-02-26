---
description: Timber terms and definitions
---

# Termionology

## Admin

A [member](concepts.md#member) that has full access to everything within an [organization](concepts.md#organization), such as billing, managing other members, etc.

## Application

A legacy term that has since been renamed to "[source](concepts.md#source)".

## Archiving

Archiving refers to the archival of your log data on an external system. Learn more in our [Archiving docs](../usage/archiving.md).

## Context

Contextual information that further describes the environment [log](concepts.md#log) data was written in. Timber invests heavily in capturing this information within our [integrations](). For example, the "hostname" of the server the log originated from is a piece of context.

## ElasticSearch

One of the primary reasons we started Timber 😁 

## Log

A single log event representing an event that occurred at a specific point in time.

## Member

Refers to a general member of an [organization](concepts.md#organization).

## Organization

The highest organizational unit within Timber. An organization is contains many sources, members, and admin.

## Role

A [member's](concepts.md#member) role dictates their access within Timber. Learn more in our [Team Management doc](../usage/account-management/team-management.md).

## Schema

Refers to your application's log schema. Learn more in our [Dynamic Schema Maintenance doc](schema-maintenance.md).

## Search

A search represents a search of your log data. Learn more in our [Search docs](../usage/searching.md).

## Source

A container for a set of [log](concepts.md#log) data. An [organization](concepts.md#organization) can have many sources and a source has many [logs](concepts.md#log).

