---
url: https://www.electronjs.org/docs/latest/api/structures/clipboard-bookmark
title: "Clipboard Bookmark"
description: ""
access_date: 2026-08-25T01:08:12.922Z
current_date: 2026-08-25T01:08:12.922Z
---

- `title` string - The title of the bookmark.
- `url` string - The URL of the bookmark.

A `ClipboardBookmark` is the payload used by the `electron application/bookmark` clipboard custom format. It is passed to [`clipboard.write()`](../clipboard.md#clipboardwritedata) as a [`ClipboardItem`](../clipboard-item.md) `data` value, and is what `getType('electron application/bookmark')` resolves to when reading via [`clipboard.read()`](../clipboard.md#clipboardread).
