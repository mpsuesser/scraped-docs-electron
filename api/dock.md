---
url: https://www.electronjs.org/docs/latest/api/dock
title: "Dock"
description: ""
access_date: 2026-08-03T17:26:37.553Z
current_date: 2026-08-03T17:26:37.553Z
---

> Control your app in the macOS dock

Process: [Main](../glossary.md#main-process)  
*This class is not exported from the `'electron'` module. It is only available as a return value of other methods in the Electron API.*

> [!-success] -success
> tip
> 
> See also: [A detailed guide about how to implement Dock menus](../tutorial/macos-dock.md).

### Instance Methods

#### dock.bounce(\[type\]) macOS

- `type` string (optional) - Can be `critical` or `informational`. The default is `informational`

Returns `Integer` - an ID representing the request.

When `critical` is passed, the dock icon will bounce until either the application becomes active or the request is canceled.

When `informational` is passed, the dock icon will bounce for one second. However, the request remains active until either the application becomes active or the request is canceled.

> [!-secondary] -secondary
> note
> 
> This method can only be used while the app is not focused; when the app is focused it will return -1.

#### dock.cancelBounce(id) macOS

- `id` Integer

Cancel the bounce of `id`.

#### dock.downloadFinished(filePath) macOS

- `filePath` string

Bounces the Downloads stack if the filePath is inside the Downloads folder.

#### dock.setBadge(text) macOS

- `text` string

Sets the string to be displayed in the dock’s badging area.

> [!-info] -info
> info
> 
> You need to ensure that your application has the permission to display notifications for this method to work.

#### dock.getBadge() macOS

Returns `string` - The badge string of the dock.

#### dock.hide() macOS

Hides the dock icon.

> [!-info] -info
> info
> 
> **Known issue:** Calling `dock.hide()` within one second of a previous call will have no effect. As a workaround, ensure at least one second has elapsed between calls — for example, by deferring with a `setTimeout` of 1100ms or more after a previous call.

#### dock.show() macOS

Returns `Promise<void>` - Resolves when the dock icon is shown.

#### dock.isVisible() macOS

Returns `boolean` - Whether the dock icon is visible.

#### dock.setMenu(menu) macOS

- `menu` [Menu](menu.md)

Sets the application's [dock menu](https://developer.apple.com/design/human-interface-guidelines/dock-menus).

#### dock.getMenu() macOS

Returns `Menu | null` - The application's [dock menu](https://developer.apple.com/design/human-interface-guidelines/dock-menus).

#### dock.setIcon(image) macOS

- `image` ([NativeImage](native-image.md) | string)

Sets the `image` associated with this dock icon.
