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

To use the Timber CLI you'll need to [create an API key](../../usage/account-management/api-keys.md#creating). Simply copy your API key and run, replacing `YOUR_API_KEY` with your new API key value:

```bash
timber auth YOUR_API_KEY
```

This will store your API key in `~/.timber` where it will be read and used for each command. Alternatively, you can supply the `--api-key` flag with each command or set the `TIMBER_API_KEY` environment variable.

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

COMMANDS:
     auth     Authenticate with Timber and persist your API key
     tail, t  Live tails logs
     orgs     List organizations that you are a part of
     sources  List sources that you have access to
     views    List saved views that you have access to (currently only console views are displayed)
     api      Make authenticated requests to the Timber API (http://docs.api.timber.io)
     help, h  Shows a list of commands or help for one command

GLOBAL OPTIONS:
   --debug, -d                Output debug messages [$TIMBER_DEBUG]
   --api-key value, -k value  Your timber.io API key [$TIMBER_API_KEY]
   --host value, -H value     Timber.io host, useful for testing (default: "https://api.timber.io") [$TIMBER_HOST]
   --color-output, -C         Set to force color output even if output is not a color terminal [$TIMBER_COLOR]
   --monochrome-output, -M    Disable color output [$TIMBER_NO_COLOR]
   --help, -h                 show help
   --version, -v              print the version
```

## Usage

{% page-ref page="../../usage/live-tailing.md" %}

{% page-ref page="../../usage/sql-querying.md" %}

...and more. Checkout out the [main usage section](../../usage/live-tailing.md) for more guides and tutorials.

