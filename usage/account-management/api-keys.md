---
description: Programmatic access to your Timber account
---

# API Keys

API keys grant access to all resources within your organization. They should be used for programmatic access to your Timber account.

{% hint style="warning" %}
Timber's API keys grant [admin level access](role-based-access-control.md#permissions) to your organization's resources. Please treat your API keys with care!
{% endhint %}

## Getting Started

{% hint style="info" %}
Each organization comes with a default API key. This API key is used during setup of your sources. See [this FAQ](api-keys.md#when-should-i-create-a-new-api-key-or-reuse-an-existing-one) for more information.
{% endhint %}

1. [Open the Timber web app](https://app.timber.io).
2. Navigate to the [S](../../clients/web-app/#the-console)ettings section using the [main navigation](../../clients/web-app/#2-main-navigation).
3. Click on the "API keys" tab.

## Usage

{% hint style="info" %}
You must have [`admin` level privileges](role-based-access-control.md#permissions) to access or modify API keys
{% endhint %}

### Obtaining An API

You can obtain an API key by [accessing the "API keys" section within your organization settings](api-keys.md#getting-started). Once there you have 2 choices:

1. Create a new API key by clicking "Create Key"
2. Copying the key of an existing API key.

Please read [this FAQ](api-keys.md#when-should-i-create-a-new-api-key-or-reuse-an-existing-one) on when to create or reuse an API key.

### Deleting / Revoking / Rotating An API Key

You can delete an API key by navigating to the "API K

### Using An API Key

You can use the API key to interface with Timber's service.



## How It Works

Timber's API keys are [JSON web tokens](https://jwt.io/). They are used within the [`Authorization` header](../../setup/protocols/http/#authorization) when making requests to the [Timber API](http://docs.api.timber.io). They are scoped to all resources beneath the organization they belong.

## FAQs

### When should I create a new API key or reuse an existing one?

We recommend creating an API for each operator \(machine or person\). This gives you the ability to revoke access for an individual operator if necessary.

For example, let's say you need an API key to [send logs to Timber from your application](../../setup/languages/), and you also need an API key for a developer on your team to use the [Timber CLI](../../clients/cli/). You should create 2 separate API keys for both of these operators, this way if your developer leaves the company you can simply [delete their API key](api-keys.md#deleting-revoking-rotating-an-api-key) without disrupting your app.

### Can I grant custom access for each API key?

Not at this time. Timber's API keys grant the holder [admin level privileges](role-based-access-control.md#permissions) to your organization's resources. Treat your API keys with care!

### Do API keys expire?

No, not at this time. As long as the API key lives in your account it is active. Timber currently does not implement a token / exchange access system with expiring keys.

