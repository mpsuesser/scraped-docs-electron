---
url: https://www.electronjs.org/docs/latest/api/image-view
title: "Image View"
description: ""
access_date: 2026-08-09T12:16:04.900Z
current_date: 2026-08-09T12:16:04.900Z
---

> A View that displays an image.

Process: [Main](../glossary.md#main-process)

This module cannot be used until the `ready` event of the `app` module is emitted.

Useful for showing splash screens that will be swapped for `WebContentsView` s when the content finishes loading.

Note that `ImageView` is experimental and may be changed or removed in the future.

```js
const { BaseWindow, ImageView, nativeImage, WebContentsView } = require('electron')

const path = require('node:path')

const win = new BaseWindow({ width: 800, height: 600 })

// Create a "splash screen" image to display while the WebContentsView loads
const splashView = new ImageView()
const splashImage = nativeImage.createFromPath(path.join(__dirname, 'loading.png'))
splashView.setImage(splashImage)
win.setContentView(splashView)

const webContentsView = new WebContentsView()
webContentsView.webContents.once('did-finish-load', () => {
  // Now that the WebContentsView has loaded, swap out the "splash screen" ImageView
  win.setContentView(webContentsView)
})
webContentsView.webContents.loadURL('https://electronjs.org')
```

## Class: ImageView extends View

History

| Version(s) | Changes |
| --- | --- |
| [ ```markdown >=37.0.0 ``` ](https://github.com/electron/electron/pull/46760)[ ```markdown ^36.4.0 ``` ](https://github.com/electron/electron/pull/46760) | ```markdown API ADDED ``` |

> A View that displays an image.

Process: [Main](../glossary.md#main-process)

`ImageView` inherits from [`View`](view.md).

`ImageView` is an [EventEmitter](https://nodejs.org/api/events.html#events_class_eventemitter).

> **Warning:**
> 
> Electron's built-in classes cannot be subclassed in user code. For more information, see [the FAQ](../faq.md#class-inheritance-does-not-work-with-electron-built-in-modules).

### new ImageView() Experimental

History

| Version(s) | Changes |
| --- | --- |
| [ ```markdown >=37.0.0 ``` ](https://github.com/electron/electron/pull/46760)[ ```markdown ^36.4.0 ``` ](https://github.com/electron/electron/pull/46760) | ```markdown API ADDED ``` |

Creates an ImageView.

### Instance Methods

The following methods are available on instances of the `ImageView` class, in addition to those inherited from [View](view.md):

#### image.setImage(image) Experimental

History

| Version(s) | Changes |
| --- | --- |
| [ ```markdown >=37.0.0 ``` ](https://github.com/electron/electron/pull/46760)[ ```markdown ^36.4.0 ``` ](https://github.com/electron/electron/pull/46760) | ```markdown API ADDED ``` |

- `image` NativeImage

Sets the image for this `ImageView`. Note that only image formats supported by `NativeImage` can be used with an `ImageView`.
