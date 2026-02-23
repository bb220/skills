### Supported Methods: Synchronous vs. Asynchronous

| **Method** | **Description** | **Synchronous** | **Asynchronous** |
|---|---|---|---|
| `templates.get()` | Retrieves a template by name | `promptlayer_client.templates.get()` | `async_promptlayer_client.templates.get()` |
| `templates.all()` | Retrieves all templates | `promptlayer_client.templates.all()` | `async_promptlayer_client.templates.all()` |
| `run()` | Executes a prompt template | `promptlayer_client.run()` | `async_promptlayer_client.run()` |
| `run_workflow()` | Executes an Agent | `promptlayer_client.run_workflow()` | `async_promptlayer_client.run_workflow()` |
| `track.metadata()` | Tracks metadata | `promptlayer_client.track.metadata()` | `async_promptlayer_client.track.metadata()` |
| `track.group()` | Tracks a group | `promptlayer_client.track.group()` | `async_promptlayer_client.track.group()` |
| `track.prompt()` | Tracks a prompt | `promptlayer_client.track.prompt()` | `async_promptlayer_client.track.prompt()` |
| `track.score()` | Tracks a score | `promptlayer_client.track.score()` | `async_promptlayer_client.track.score()` |
| `group.create()` | Creates a new group | `promptlayer_client.group.create()` | `async_promptlayer_client.group.create()` |
| `log_request()` | Logs a request | `promptlayer_client.log_request()` | `async_promptlayer_client.log_request()` |

> **Note:** All asynchronous methods require an active event loop. Use them within an `async` function and run with `asyncio.run()` or another event loop manager (e.g., `await` in Jupyter notebooks).