---
description: Timber terms and their definitions
---

# Concepts

## Admin

A [member](concepts.md#member) that has full access to everything within an [organization](concepts.md#organization), such as billing, managing other members, etc.

## Application

A legacy term that has since been renamed to "[source](concepts.md#source)".

## Context

Contextual information that further describes the environment [log](concepts.md#log) data was written in. Timber invests heavily in capturing this information within our [integrations](../usage/integrations/). For example, the "hostname" of the server the log originated from is a piece of context.

## Log

A single log event representing an event that occurred at a specific point in time.

## Member

Refers to a general member of an [organization](concepts.md#organization).

## Organization

The highest organizational unit within Timber. An organization is contains many sources, members, and admin.

## Source

A container for a set of [log](concepts.md#log) data. An [organization](concepts.md#organization) can have many sources and a source has many [logs](concepts.md#log).

