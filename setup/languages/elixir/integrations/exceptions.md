---
description: Turn logged exceptions into structured exception events
---

# Exceptions

{% hint style="warning" %}
The Timber Exception integration requires [installation of the base `timber` Hex package](../#installation) first.
{% endhint %}

Timber integrates directly with Elixir to turn your exceptions into rich structured events. This reduces noise in your logs and makes them easier to search and use.

## Installation

1. Ensure that you have both `:timber` \(version 3.1.0 or later\) and `:timber_exceptions` listed as dependencies in `mix.exs`:  


   {% code-tabs %}
   {% code-tabs-item title="mix.exs" %}
   ```elixir
   def deps do
     [
       {:timber, "~> 3.1"},
       {:timber_exceptions, "~> 2.0"}
     ]
   end
   ```
   {% endcode-tabs-item %}
   {% endcode-tabs %}

2. Run `mix deps.get` to install the dependencies.
3. Add the translator in your application's `start/2` function:  


   {% code-tabs %}
   {% code-tabs-item title="lib/my\_app/application.ex" %}
   ```elixir
   defmodule MyApp
     use Applictation
  
     def start(_type, _args) do
       # ADD THE FOLLOWING LINE:
       :ok = Logger.add_translator({Timber.Exceptions.Translator, :translate})

       opts = [strategy: :one_for_one, name: MyApp.Supervisor]
       Supervisor.start_link(children, opts)
     end
   end
   ```
   {% endcode-tabs-item %}
   {% endcode-tabs %}

## Events

### error

The `error` event is emitted any time your application raises an exception

```javascript
{
    "error": {
        "name": "RuntimeError",
        "message": "function MyModule.bad/0 is undefined (module MyModule is not available)",
        "backtrace": [
            {
                "function": "my_func",
                "file": "lib/my_file.ex",
                "line": 22
            }
        ]
    }
}
```

| Name | Type | Description |
| :--- | :--- | :--- |
| `error.name` | `string` | The module name of the exception. |
| `error.message` | `string` | The message of the exception. |
| `error.backtrace.[n].function` | `string` | The backtrace item's function name. |
| `error.backtrace.[n].file` | `string` | The backtrace item's file name. |
| `error.backtrace.[n].line` | `int` | The backtrace item's line number |

