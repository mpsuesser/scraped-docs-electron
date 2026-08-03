---
url: https://www.electronjs.org/docs/latest/api/structures/process-memory-info
title: "Process Memory Info"
description: ""
access_date: 2026-08-03T19:08:43.151Z
current_date: 2026-08-03T19:08:43.151Z
---

- `residentSet` Integer *Linux* *Windows* - The amount of memory currently pinned to actual physical RAM in Kilobytes.
- `private` Integer - The amount of memory not shared by other processes, such as JS heap or HTML content in Kilobytes.
- `shared` Integer - The amount of memory shared between processes, typically memory consumed by the Electron code itself in Kilobytes.
