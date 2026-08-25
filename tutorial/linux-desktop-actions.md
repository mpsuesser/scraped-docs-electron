---
url: https://www.electronjs.org/docs/latest/tutorial/linux-desktop-actions
title: "Linux Desktop Actions"
description: ""
access_date: 2026-08-25T01:08:12.922Z
current_date: 2026-08-25T01:08:12.922Z
---

## Overview

On many Linux environments, you can add custom entries to the system launcher by modifying the `.desktop` file. For details, see the [freedesktop.org Desktop Entry Specification](https://specifications.freedesktop.org/desktop-entry-spec/desktop-entry-spec-latest.html).

To create a shortcut, you need to provide `Name` and `Exec` properties for the entry you want to add to the shortcut menu. The desktop will execute the command defined in the `Exec` field after the user clicks the shortcut menu item. An example of the `.desktop` file may look as follows:

```markdown
Actions=PlayPause;Next;Previous

[Desktop Action PlayPause]
Name=Play-Pause
Exec=audacious -t

[Desktop Action Next]
Name=Next
Exec=audacious -f

[Desktop Action Previous]
Name=Previous
Exec=audacious -r
```

The preferred way for the desktop to instruct your application on what to do is using parameters. You can find them in your application in the global variable `process.argv`.
