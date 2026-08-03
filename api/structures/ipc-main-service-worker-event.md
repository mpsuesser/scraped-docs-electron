---
url: https://www.electronjs.org/docs/latest/api/structures/ipc-main-service-worker-event
title: "Ipc Main Service Worker Event"
description: ""
access_date: 2026-08-03T18:12:31.121Z
current_date: 2026-08-03T18:12:31.121Z
---

- `type` String - Possible values include `service-worker`.
- `serviceWorker` [ServiceWorkerMain](../service-worker-main.md) *Readonly* - The service worker that sent this message
- `versionId` Number - The service worker version ID.
- `session` Session - The [`Session`](../session.md) instance with which the event is associated.
- `returnValue` any - Set this to the value to be returned in a synchronous message
- `ports` [MessagePortMain](../message-port-main.md) \[\] - A list of MessagePorts that were transferred with this message
- `reply` Function - A function that will send an IPC message to the renderer frame that sent the original message that you are currently handling. You should use this method to "reply" to the sent message in order to guarantee the reply will go to the correct process and frame.
