---
url: https://www.electronjs.org/docs/latest/tutorial/custom-window-styles
title: "Custom Window Styles"
description: ""
access_date: 2026-08-03T18:54:45.323Z
current_date: 2026-08-03T18:54:45.323Z
---

## Frameless windows

![Frameless Window](https://www.electronjs.org/assets/images/frameless-window-0492ee8065dcf7d9663bd43a05d8b5c1.png)

A frameless window removes all [chrome](https://developer.mozilla.org/en-US/docs/Glossary/Chrome) applied by the OS, including window controls.

To create a frameless window, set the [BaseWindowContructorOptions](../api/structures/base-window-options.md) `frame` param in the `BrowserWindow` constructor to `false`.

#### main.js

```js
const { app, BrowserWindow } = require('electron')

function createWindow () {
  const win = new BrowserWindow({
    width: 300,
    height: 200,
    frame: false
  })
  win.loadURL('https://example.com')
}

app.whenReady().then(() => {
  createWindow()
})
```

On Wayland (Linux), frameless windows have GTK drop shadows and extended resize boundaries by default. To create a fully frameless window with no decorations, set `hasShadow: false` in the window constructor options.

## Transparent windows

![Transparent Window](https://www.electronjs.org/assets/images/transparent-window-a75b78ec87da59f7213c8b955f665235.png) ![Transparent Window in macOS Mission Control](https://www.electronjs.org/assets/images/transparent-window-mission-control-67fdc1c38d63577d3002d179565e2fe5.png)

To create a fully transparent window, set the [BaseWindowContructorOptions](../api/structures/base-window-options.md) `transparent` param in the `BrowserWindow` constructor to `true`.

The following fiddle takes advantage of a transparent window and CSS styling to create the illusion of a circular window.

#### main.js

```js
const { app, BrowserWindow } = require('electron')

function createWindow () {
  const win = new BrowserWindow({
    width: 100,
    height: 100,
    resizable: false,
    frame: false,
    transparent: true
  })
  win.loadFile('index.html')
}

app.whenReady().then(() => {
  createWindow()
})
```

#### index.html

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8">
    <!-- https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP -->
    <meta http-equiv="Content-Security-Policy" content="default-src 'self'; script-src 'self'; style-src 'self' 'unsafe-inline'">
    <link href="./styles.css" rel="stylesheet">
    <title>Transparent Hello World</title>
  </head>
  <body>
    <div class="white-circle">
        <div>Hello World!</div>
    </div>
  </body>
</html>
```

#### styles.css

```markdown
body {
    margin: 0;
    padding: 0;
    background-color: rgba(0, 0, 0, 0); /* Transparent background */
}
.white-circle {
    width: 100px;
    height: 100px;
    background-color: white;
    border-radius: 50%;
    display: flex;
    align-items: center;
    justify-content: center;
    app-region: drag;
    user-select: none;
}
```

### Limitations

- You cannot click through the transparent area. See [#1335](https://github.com/electron/electron/issues/1335) for details.
- Transparent windows are not resizable. Setting `resizable` to `true` may make a transparent window stop working on some platforms.
- The CSS [`blur()`](https://developer.mozilla.org/en-US/docs/Web/CSS/filter-function/blur\(\)) filter only applies to the window's web contents, so there is no way to apply blur effect to the content below the window (i.e. other applications open on the user's system).
- The window will not be transparent when DevTools is opened.
- On *Windows*:
	- Transparent windows can not be maximized using the Windows system menu or by double clicking the title bar. The reasoning behind this can be seen on PR [#28207](https://github.com/electron/electron/pull/28207).
- On *macOS*:
	- The native window shadow will not be shown on a transparent window.
