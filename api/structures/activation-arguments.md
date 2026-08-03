---
url: https://www.electronjs.org/docs/latest/api/structures/activation-arguments
title: "Activation Arguments"
description: ""
access_date: 2026-08-03T17:26:37.553Z
current_date: 2026-08-03T17:26:37.553Z
---

> Used on Windows only.

- `type` string - The type of activation that launched the app: `'click'`, `'action'`, or `'reply'`.
- `arguments` string - The raw activation arguments string from Windows.
- `actionIndex` number (optional) - For `'action'` type, the index of the button that was clicked.
- `reply` string (optional) - For `'reply'` type, the text the user entered in the reply field.
- `userInputs` Record<string, string> (optional) - A dictionary of all user inputs from the notification.
