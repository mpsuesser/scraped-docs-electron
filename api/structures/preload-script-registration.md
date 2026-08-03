---
url: https://www.electronjs.org/docs/latest/api/structures/preload-script-registration
title: "Preload Script Registration"
description: ""
access_date: 2026-08-03T18:54:45.323Z
current_date: 2026-08-03T18:54:45.323Z
---

- `type` string - Context type where the preload script will be executed. Possible values include `frame` or `service-worker`.
- `id` string (optional) - Unique ID of preload script. Defaults to a random UUID.
- `filePath` string - Path of the script file. Must be an absolute path.
