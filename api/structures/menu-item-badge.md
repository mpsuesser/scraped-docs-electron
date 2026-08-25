---
url: https://www.electronjs.org/docs/latest/api/structures/menu-item-badge
title: "Menu Item Badge"
description: ""
access_date: 2026-08-25T01:08:12.922Z
current_date: 2026-08-25T01:08:12.922Z
---

- `type` string (optional) - Can be `alerts`, `updates`, `new-items` or `none`. Default is `none`. See [Creating badges of a specific type](https://developer.apple.com/documentation/appkit/nsmenuitembadge#Creating-badges-of-a-specific-type) for further explanation of these types.
- `count` number (optional) - The number of items the badge displays. Required for the `alerts`, `updates` and `new-items` types; cannot be used with `none`.
- `content` string (optional) - A custom string to display in the badge. Required for, and only usable with, the `none` type.

If you use one of the predefined badge types (not `none`), the system localizes and pluralizes the badge for you. If you create your own custom badge string, you need to localize and pluralize that string yourself.
