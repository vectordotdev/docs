---
description: Ensure context is carried over to child processes
---

# Copying Context To Child Processes

As described in the ["Setting Context" section](../#setting-context), Timber's context is local to each process. This ensures that each process can maintain context specific to it's state without conflicting with other processes, but there are times where you'll want to carry context over to child processes, such as when using the [`Task` module](https://hexdocs.pm/elixir/Task.html):

```elixir
current_context = Timber.LocalContext.load()

Task.async fn ->
  Timber.LocalContext.save(current_context)
  Logger.info("Logs from a separate process")
end
```

The new process spawned with `Task.async/1` will now contain the same Timber context as its parent. Please note, that the [runtime context's](../#runtime) `vm_pid` will be overridden and remain true to the local process.

