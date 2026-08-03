---
url: https://www.electronjs.org/docs/latest/api/structures/service-worker-info
title: "Service Worker Info"
description: ""
access_date: 2026-08-03T17:26:37.553Z
current_date: 2026-08-03T17:26:37.553Z
---

- `scriptUrl` string - The full URL to the script that this service worker runs
- `scope` string - The base URL that this service worker is active for.
- `renderProcessId` number - The virtual ID of the process that this service worker is running in. This is not an OS level PID. This aligns with the ID set used for `webContents.getProcessId()`.
- `versionId` number - ID of the service worker version
