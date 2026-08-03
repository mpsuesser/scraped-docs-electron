---
url: https://www.electronjs.org/docs/latest/api/structures/window-session-end-event
title: "Window Session End Event"
description: ""
access_date: 2026-08-03T17:26:37.553Z
current_date: 2026-08-03T17:26:37.553Z
---

- `reasons` string\[\] - List of reasons for shutdown. Can be 'shutdown', 'close-app', 'critical', or 'logoff'.

Unfortunately, Windows does not offer a way to differentiate between a shutdown and a reboot, meaning the 'shutdown' reason is triggered in both scenarios. For more details on the `WM_ENDSESSION` message and its associated reasons, refer to the [MSDN documentation](https://learn.microsoft.com/en-us/windows/win32/shutdown/wm-endsession).
