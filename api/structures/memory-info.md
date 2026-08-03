---
url: https://www.electronjs.org/docs/latest/api/structures/memory-info
title: "Memory Info"
description: ""
access_date: 2026-08-03T18:54:45.323Z
current_date: 2026-08-03T18:54:45.323Z
---

- `workingSetSize` Integer - The amount of memory currently pinned to actual physical RAM.
- `peakWorkingSetSize` Integer - The maximum amount of memory that has ever been pinned to actual physical RAM.
- `privateBytes` Integer (optional) *Windows* - The amount of memory not shared by other processes, such as JS heap or HTML content.

Note that all statistics are reported in Kilobytes.
