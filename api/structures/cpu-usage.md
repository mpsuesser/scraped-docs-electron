---
url: https://www.electronjs.org/docs/latest/api/structures/cpu-usage
title: "Cpu Usage"
description: ""
access_date: 2026-08-03T18:54:45.323Z
current_date: 2026-08-03T18:54:45.323Z
---

- `percentCPUUsage` number - Percentage of CPU used since the last call to getCPUUsage. First call returns 0.
- `cumulativeCPUUsage` number (optional) - Total seconds of CPU time used since process startup.
- `idleWakeupsPerSecond` number - The number of average idle CPU wakeups per second since the last call to getCPUUsage. First call returns 0. Will always return 0 on Windows.
