---
url: https://www.electronjs.org/docs/latest/api/structures/ipc-main-service-worker-invoke-event
title: "Ipc Main Service Worker Invoke Event"
description: ""
access_date: 2026-08-03T17:26:37.553Z
current_date: 2026-08-03T17:26:37.553Z
---

- `type` String - Possible values include `service-worker`.
- `serviceWorker` [ServiceWorkerMain](../service-worker-main.md) *Readonly* - The service worker that sent this message
- `versionId` Number - The service worker version ID.
- `session` Session - The [`Session`](../session.md) instance with which the event is associated.
