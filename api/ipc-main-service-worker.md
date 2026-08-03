---
url: https://www.electronjs.org/docs/latest/api/ipc-main-service-worker
title: "Ipc Main Service Worker"
description: ""
access_date: 2026-08-03T19:38:49.815Z
current_date: 2026-08-03T19:38:49.815Z
---

> Communicate asynchronously from the main process to service workers.

Process: [Main](../glossary.md#main-process)

> **Note:**
> 
> This API is a subtle variation of [`IpcMain`](ipc-main.md) —targeted for communicating with service workers. For communicating with web frames, consult the `IpcMain` documentation.

> **Warning:**
> 
> Electron's built-in classes cannot be subclassed in user code. For more information, see [the FAQ](../faq.md#class-inheritance-does-not-work-with-electron-built-in-modules).

### Instance Methods

#### ipcMainServiceWorker.on(channel, listener)

- `channel` string
- `listener` Function

Listens to `channel`, when a new message arrives `listener` would be called with `listener(event, args...)`.

#### ipcMainServiceWorker.once(channel, listener)

- `channel` string
- `listener` Function

Adds a one time `listener` function for the event. This `listener` is invoked only the next time a message is sent to `channel`, after which it is removed.

#### ipcMainServiceWorker.removeListener(channel, listener)

- `channel` string
- `listener` Function
	- `...args` any\[\]

Removes the specified `listener` from the listener array for the specified `channel`.

#### ipcMainServiceWorker.removeAllListeners(\[channel\])

- `channel` string (optional)

Removes listeners of the specified `channel`.

#### ipcMainServiceWorker.handle(channel, listener)

- `channel` string
- `listener` Function<Promise<any> | any>

#### ipcMainServiceWorker.handleOnce(channel, listener)

- `channel` string
- `listener` Function<Promise<any> | any>

Handles a single `invoke` able IPC message, then removes the listener. See `ipcMainServiceWorker.handle(channel, listener)`.

#### ipcMainServiceWorker.removeHandler(channel)

- `channel` string

Removes any handler for `channel`, if present.
