---
url: https://www.electronjs.org/docs/latest/tutorial/custom-title-bar
title: "Custom Title Bar"
description: ""
access_date: 2026-08-03T19:38:49.815Z
current_date: 2026-08-03T19:38:49.815Z
---

## Basic tutorial

Application windows have a default [chrome](https://developer.mozilla.org/en-US/docs/Glossary/Chrome) applied by the OS. Not to be confused with the Google Chrome browser, window *chrome* refers to the parts of the window (e.g. title bar, toolbars, controls) that are not a part of the main web content. While the default title bar provided by the OS chrome is sufficient for simple use cases, many applications opt to remove it. Implementing a custom title bar can help your application feel more modern and consistent across platforms.

You can follow along with this tutorial by opening Fiddle with the following starter code.

#### main.js

```js
const { app, BrowserWindow } = require('electron')

function createWindow () {
  const win = new BrowserWindow({})
  win.loadURL('https://example.com')
}

app.whenReady().then(() => {
  createWindow()
})
```

### Remove the default title bar

Let’s start by configuring a window with native window controls and a hidden title bar. To remove the default title bar, set the [BaseWindowContructorOptions](../api/structures/base-window-options.md) `titleBarStyle` param in the `BrowserWindow` constructor to `'hidden'`.

#### main.js

```js
const { app, BrowserWindow } = require('electron')

function createWindow () {
  const win = new BrowserWindow({
    // remove the default titlebar
    titleBarStyle: 'hidden'
  })
  win.loadURL('https://example.com')
}

app.whenReady().then(() => {
  createWindow()
})
```

### Add native window controls Windows Linux

On macOS, setting `titleBarStyle: 'hidden'` removes the title bar while keeping the window’s traffic light controls available in the upper left hand corner. However on Windows and Linux, you’ll need to add window controls back into your `BrowserWindow` by setting the [BaseWindowContructorOptions](../api/structures/base-window-options.md) `titleBarOverlay` param in the `BrowserWindow` constructor.

#### main.js

```js
const { app, BrowserWindow } = require('electron')

function createWindow () {
  const win = new BrowserWindow({
    // remove the default titlebar
    titleBarStyle: 'hidden',
    // expose window controls in Windows/Linux
    ...(process.platform !== 'darwin' ? { titleBarOverlay: true } : {})
  })
  win.loadURL('https://example.com')
}

app.whenReady().then(() => {
  createWindow()
})
```

Setting `titleBarOverlay: true` is the simplest way to expose window controls back into your `BrowserWindow`. If you’re interested in customizing the window controls further, check out the sections [Custom traffic lights](#custom-traffic-lights-macos) and [Custom window controls](#custom-window-controls) that cover this in more detail.

### Create a custom title bar

Now, let’s implement a simple custom title bar in the `webContents` of our `BrowserWindow`. There’s nothing fancy here, just HTML and CSS!

#### main.js

```js
const { app, BrowserWindow } = require('electron')

function createWindow () {
  const win = new BrowserWindow({
    // remove the default titlebar
    titleBarStyle: 'hidden',
    // expose window controls in Windows/Linux
    ...(process.platform !== 'darwin' ? { titleBarOverlay: true } : {})
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
    <title>Custom Titlebar App</title>
  </head>
  <body>
      <!-- mount your title bar at the top of you application's body tag -->
    <div class="titlebar">Cool titlebar</div>
  </body>
</html>
```

#### styles.css

```markdown
body {
    margin: 0;
}

.titlebar {
  height: 30px;
  background: blue;
  color: white;
  display: flex;
  justify-content: center;
  align-items: center;
}
```

Currently our application window can’t be moved. Since we’ve removed the default title bar, the application needs to tell Electron which regions are draggable. We’ll do this by adding the CSS style `app-region: drag` to the custom title bar. Now we can drag the custom title bar to reposition our app window!

#### main.js

```js
const { app, BrowserWindow } = require('electron')

function createWindow () {
  const win = new BrowserWindow({
    // remove the default titlebar
    titleBarStyle: 'hidden',
    // expose window controls in Windows/Linux
    ...(process.platform !== 'darwin' ? { titleBarOverlay: true } : {})
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
    <title>Custom Titlebar App</title>
  </head>
  <body>
      <!-- mount your title bar at the top of you application's body tag -->
    <div class="titlebar">Cool titlebar</div>
  </body>
</html>
```

#### styles.css

```markdown
body {
  margin: 0;
}
.titlebar {
  height: 30px;
  background: blue;
  color: white;
  display: flex;
  justify-content: center;
  align-items: center;
  app-region: drag;
}
```

For more information around how to manage drag regions defined by your electron application, see the [Custom draggable regions](custom-window-interactions.md#custom-draggable-regions) section below.

One more step: we should make sure our title bar content doesn't overlap with the native window controls. Buttons can appear on the right or left side of the frame (or both) depending on RTL and the user's settings. We can create a safe area using the CSS variables `env(titlebar-area-x, 0px)` and `env(titlebar-area-width, 100%)`.

#### main.js

```js
const { app, BrowserWindow } = require('electron')

function createWindow () {
  const win = new BrowserWindow({
    // remove the default titlebar
    titleBarStyle: 'hidden',
    // expose window controls in Windows/Linux
    ...(process.platform !== 'darwin' ? { titleBarOverlay: true } : {})
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
    <title>Custom Titlebar App</title>
  </head>
  <body>
      <!-- mount your title bar at the top of you application's body tag -->
    <div class="titlebar">Cool titlebar</div>
  </body>
</html>
```

#### styles.css

```markdown
body {
  margin: 0;
}
.titlebar {
  background: blue;
  color: white;
  display: flex;
  justify-content: center;
  align-items: center;
  app-region: drag;
  
  margin-left: env(titlebar-area-x, 0);
  width:       env(titlebar-area-width, 100%);
  height:      env(titlebar-area-height, 30px);
  box-sizing: border-box;
  border: 1px dashed red;
}
```

Congratulations, you've just implemented a basic custom title bar!

## Advanced window customization

### Custom traffic lights macOS

#### Customize the look of your traffic lights macOS

The `customButtonsOnHover` title bar style will hide the traffic lights until you hover over them. This is useful if you want to create custom traffic lights in your HTML but still use the native UI to control the window.

```js
const { BrowserWindow } = require('electron')

const win = new BrowserWindow({ titleBarStyle: 'customButtonsOnHover' })
```

#### Customize the traffic light position macOS

To modify the position of the traffic light window controls, there are two configuration options available.

Applying `hiddenInset` title bar style will shift the vertical inset of the traffic lights by a fixed amount.

```js
const { BrowserWindow } = require('electron')

const win = new BrowserWindow({ titleBarStyle: 'hiddenInset' })
```

If you need more granular control over the positioning of the traffic lights, you can pass a set of coordinates to the `trafficLightPosition` option in the `BrowserWindow` constructor.

```js
const { BrowserWindow } = require('electron')

const win = new BrowserWindow({
  titleBarStyle: 'hidden',
  trafficLightPosition: { x: 10, y: 10 }
})
```

#### Show and hide the traffic lights programmatically macOS

You can also show and hide the traffic lights programmatically from the main process. The `win.setWindowButtonVisibility` forces traffic lights to be shown or hidden depending on the value of its boolean parameter.

```js
const { BrowserWindow } = require('electron')

const win = new BrowserWindow()
// hides the traffic lights
win.setWindowButtonVisibility(false)
```

> **Note:**
> 
> Given the number of APIs available, there are many ways of achieving this. For instance, combining `frame: false` with `win.setWindowButtonVisibility(true)` will yield the same layout outcome as setting `titleBarStyle: 'hidden'`.

#### Custom window controls

The [Window Controls Overlay API](https://github.com/WICG/window-controls-overlay/blob/main/explainer.md) is a web standard that gives web apps the ability to customize their title bar region when installed on desktop. Electron exposes this API through the `titleBarOverlay` option in the `BrowserWindow` constructor. When `titleBarOverlay` is enabled, the window controls become exposed in their default position, and DOM elements cannot use the area underneath this region.

> **Note:**
> 
> `titleBarOverlay` requires the `titleBarStyle` param in the `BrowserWindow` constructor to have a value other than `default`.

The custom title bar tutorial covers a [basic example](#add-native-window-controls-windows-linux) of exposing window controls by setting `titleBarOverlay: true`. The height, color (*Windows* *Linux*), and symbol colors (*Windows*) of the window controls can be customized further by setting `titleBarOverlay` to an object.

The value passed to the `height` property must be an integer. The `color` and `symbolColor` properties accept `rgba()`, `hsla()`, and `#RRGGBBAA` color formats and support transparency. If a color option is not specified, the color will default to its system color for the window control buttons. Similarly, if the height option is not specified, the window controls will default to the standard system height:

```js
const { BrowserWindow } = require('electron')

const win = new BrowserWindow({
  titleBarStyle: 'hidden',
  titleBarOverlay: {
    color: '#2f3241',
    symbolColor: '#74b1be',
    height: 60
  }
})
```

> **Note:**
> 
> Once your title bar overlay is enabled from the main process, you can access the overlay's color and dimension values from a renderer using a set of readonly [JavaScript APIs](https://github.com/WICG/window-controls-overlay/blob/main/explainer.md#javascript-apis) and [CSS Environment Variables](https://github.com/WICG/window-controls-overlay/blob/main/explainer.md#css-environment-variables).
