---
description: Timber's command line interface
---

# CLI

Timber integrates with the command line through the [`timber` command line utility](https://github.com/timberio/cli), allowing you to access and search your logs directly from your terminal.

## Features

* [Live-tail & search your logs](../../usage/live-tailing.md)
* [SQL query your logs](../../usage/sql-querying.md)
* List organizations
* [List sources / applications](../../usage/source-management.md)
* Issue authenticated requests to the [Timber API](https://docs.api.timber.io)

## Installation

### Mac OS

Timber is easily installed through the [homebrew package manager](https://brew.sh/):

```bash
brew tap timberio/brew && brew install timber
```

You can update `timber` over time via:

```text
brew update && brew upgrade timber
```

### Pre-built binaries

If you're not on Mac, or prefer not to use [homebrew](https://brew.sh/), you can install one of our pre-built binaries. These can be found in the [releases section](https://github.com/timberio/cli/releases) of the [`cli` repository](https://github.com/timberio/cli):

* [Darwin AMD64](https://github.com/timberio/cli/blob/master)
* [FreeBSD AMD64](https://github.com/timberio/cli/blob/master)
* [Linux AMD64](https://github.com/timberio/cli/blob/master)
* [Linux ARM](https://github.com/timberio/cli/blob/master)
* [Linux ARM64](https://github.com/timberio/cli/blob/master)
* [NetBSD AMD64](https://github.com/timberio/cli/blob/master)
* [OpenBSD AMD64](https://github.com/timberio/cli/blob/master)

### Building From Source

If all else fails, you can build `timber` from source. Building from source requires the [`go` runtime](https://golang.org/doc/install):

```bash
git clone git@github.com:timberio/cli.git timber
cd timber
go build
./timber help
```

We recommend moving `timber` into your `$PATH`.

## Configuration

### Authentication

To use the Timber CLI you'll need to [create an API key](../../usage/account-management/api-keys.md#creating). Simply copy your API key and run `timber auth [api_key]`, replacing `[api_key]` with your new API key value.

```bash
timber auth [api_key]
```

This will store your API key in the `~/.timber` folder. Going forward, this key will be used for all timber commands. Alternatively, you can supply the `--api-key` flag with each command or set the `TIMBER_API_KEY` environment variable.

### Switching Between Organizations

The Timber CLI offers support for switching between multiple organizations:

1. Run `timber auth list` to list your API keys
2. Run `timber auth switch [org_id]` to switch to another API key.

If you don't see your API key, [add it as described above](./#authentication).

### ANSI Coloring

By default, Timber will use [ANSI coloring](https://en.wikipedia.org/wiki/ANSI_escape_code#Colors), you can disable this with the `--monochrome-output` flag or by setting the `TIMBER_NO_COLOR` env var.

## Updating

### Mac OS

```bash
brew update
brew upgrade timber
```

### Other

Simply delete the `timber` binary and [re-install it](./#installation).

## Commands

Commands and options can be accessed with the `timber help` command:

```text
NAME:
   timber - Command line interface for the Timber.io logging service

USAGE:
   timber [global options] command [command options] [arguments...]

VERSION:
   0.2.0

COMMANDS:
     auth         Manage authentication for the Timber CLI
     tail, t      Live tails logs
     sources      Manage your Timber sources
     sql-queries  Manage SQL queries
     views        Manage your saved views (only console views are supported in the CLI)
     api          Issue authenticated requests to the Timber API (http://docs.api.timber.io)
     help, h      Shows a list of commands or help for one command

GLOBAL OPTIONS:
   --api-key value, -K value    Your timber.io API key [$TIMBER_API_KEY]
   --color-output, -C           Set to force color output even if output is not a color terminal [$TIMBER_COLOR]
   --max-columns value          Maximum number of columns to display in a table (default: 7) [$TIMBER_MAX_COLUMNS]
   --max-column-length value    Maximum length of a single column value (default: 20) [$TIMBER_MAX_COLUMNS]
   --max-per-page value         Maximum number of items to display per page (default: 25) [$TIMBER_MAX_PER_PAGE]
   --monochrome-output, -M      Disable color output [$TIMBER_NO_COLOR]
   --debug, -D                  Output debug messages [$TIMBER_DEBUG]
   --host value, -H value       Timber.io host, useful for testing (default: "https://api.timber.io") [$TIMBER_HOST]
   --time-zone value, -Z value  Time zone, such as "Local", "UTC", or "America/New_York" (default: "Local") [$TIMBER_TIME_ZONE]
   --help, -h                   show help
   --version, -v                print the version
```

## Usage

{% page-ref page="../../usage/live-tailing.md" %}

{% page-ref page="../../usage/sql-querying.md" %}

...and more. Checkout out the [main usage section](../../usage/live-tailing.md) for more guides and tutorials.

