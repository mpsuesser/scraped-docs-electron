---
url: https://www.electronjs.org/docs/latest/tutorial/launch-app-from-url-in-another-app
title: "Launch App From Url In Another App"
description: ""
access_date: 2026-08-03T19:38:49.815Z
current_date: 2026-08-03T19:38:49.815Z
---

## Overview

This guide will take you through the process of setting your Electron app as the default handler for a specific [protocol](../api/protocol.md).

By the end of this tutorial, we will have set our app to intercept and handle any clicked URLs that start with a specific protocol. In this guide, the protocol we will use will be " `electron-fiddle://` ".

## Examples

### Main Process (main.js)

First, we will import the required modules from `electron`. These modules help control our application lifecycle and create a native browser window.

```js
const { app, BrowserWindow, shell } = require('electron')

const path = require('node:path')
```

Next, we will proceed to register our application to handle all " `electron-fiddle://` " protocols.

```js
if (process.defaultApp) {
  if (process.argv.length >= 2) {
    app.setAsDefaultProtocolClient('electron-fiddle', process.execPath, [path.resolve(process.argv[1])])
  }
} else {
  app.setAsDefaultProtocolClient('electron-fiddle')
}
```

We will now define the function in charge of creating our browser window and load our application's `index.html` file.

```js
let mainWindow

const createWindow = () => {
  // Create the browser window.
  mainWindow = new BrowserWindow({
    width: 800,
    height: 600,
    webPreferences: {
      preload: path.join(__dirname, 'preload.js')
    }
  })

  mainWindow.loadFile('index.html')
}
```

In this next step, we will create our `BrowserWindow` and tell our application how to handle an event in which an external protocol is clicked.

This code will be different in Windows and Linux compared to macOS. This is due to both platforms emitting the `second-instance` event rather than the `open-url` event and Windows requiring additional code in order to open the contents of the protocol link within the same Electron instance. Read more about this [here](../api/app.md#apprequestsingleinstancelockadditionaldata).

#### Windows and Linux code:

```js
const gotTheLock = app.requestSingleInstanceLock()

if (!gotTheLock) {
  app.quit()
} else {
  app.on('second-instance', (event, commandLine, workingDirectory) => {
    // Someone tried to run a second instance, we should focus our window.
    if (mainWindow) {
      if (mainWindow.isMinimized()) mainWindow.restore()
      mainWindow.focus()
    }
    // the commandLine is array of strings in which last element is deep link url
    dialog.showErrorBox('Welcome Back', \`You arrived from: ${commandLine.pop()}\`)
  })

  // Create mainWindow, load the rest of the app, etc...
  app.whenReady().then(() => {
    createWindow()
    // Check for deep link on cold start
    if (process.argv.length >= 2) {
      const lastArg = process.argv[process.argv.length - 1]
      if (lastArg.startsWith('electron-fiddle://')) {
        dialog.showErrorBox('Welcome Back', \`You arrived from: ${lastArg}\`)
      }
    }
  })
}
```

#### macOS code:

```js
// This method will be called when Electron has finished
// initialization and is ready to create browser windows.
// Some APIs can only be used after this event occurs.
app.whenReady().then(() => {
  createWindow()
})

// Handle the protocol. In this case, we choose to show an Error Box.
app.on('open-url', (event, url) => {
  dialog.showErrorBox('Welcome Back', \`You arrived from: ${url}\`)
})
```

Finally, we will add some additional code to handle when someone closes our application.

```js
// Quit when all windows are closed, except on macOS. There, it's common
// for applications and their menu bar to stay active until the user quits
// explicitly with Cmd + Q.
app.on('window-all-closed', () => {
  if (process.platform !== 'darwin') app.quit()
})
```

## Important notes

### Packaging

On macOS and Linux, this feature will only work when your app is packaged. It will not work when you're launching it in development from the command-line. When you package your app you'll need to make sure the macOS `Info.plist` and the Linux `.desktop` files for the app are updated to include the new protocol handler. Some of the Electron tools for bundling and distributing apps handle this for you.

#### Electron Forge

If you're using Electron Forge, adjust `packagerConfig` for macOS support, and the configuration for the appropriate Linux makers for Linux support, in your [Forge configuration](https://www.electronforge.io/configuration) *(please note the following example only shows the bare minimum needed to add the configuration changes)*:

```json
{
  "config": {
    "forge": {
      "packagerConfig": {
        "protocols": [
          {
            "name": "Electron Fiddle",
            "schemes": ["electron-fiddle"]
          }
        ]
      },
      "makers": [
        {
          "name": "@electron-forge/maker-deb",
          "config": {
            "mimeType": ["x-scheme-handler/electron-fiddle"]
          }
        }
      ]
    }
  }
}
```

#### Electron Packager

For macOS support:

If you're using Electron Packager's API, adding support for protocol handlers is similar to how Electron Forge is handled, except `protocols` is part of the Packager options passed to the `packager` function.

```js
const packager = require('@electron/packager')

packager({
  // ...other options...
  protocols: [
    {
      name: 'Electron Fiddle',
      schemes: ['electron-fiddle']
    }
  ]

}).then(paths => console.log(\`SUCCESS: Created ${paths.join(', ')}\`))
  .catch(err => console.error(\`ERROR: ${err.message}\`))
```

If you're using Electron Packager's CLI, use the `--protocol` and `--protocol-name` flags. For example:

```shell
npx electron-packager . --protocol=electron-fiddle --protocol-name="Electron Fiddle"
```

## Conclusion

After you start your Electron app, you can enter in a URL in your browser that contains the custom protocol, for example `"electron-fiddle://open"` and observe that the application will respond and show an error dialog box.

#### main.js

```js
// Modules to control application life and create native browser window
const { app, BrowserWindow, ipcMain, shell, dialog } = require('electron/main')
const path = require('node:path')

let mainWindow

if (process.defaultApp) {
  if (process.argv.length >= 2) {
    app.setAsDefaultProtocolClient('electron-fiddle', process.execPath, [path.resolve(process.argv[1])])
  }
} else {
  app.setAsDefaultProtocolClient('electron-fiddle')
}

const gotTheLock = app.requestSingleInstanceLock()

if (!gotTheLock) {
  app.quit()
} else {
  app.on('second-instance', (event, commandLine, workingDirectory) => {
    // Someone tried to run a second instance, we should focus our window.
    if (mainWindow) {
      if (mainWindow.isMinimized()) mainWindow.restore()
      mainWindow.focus()
    }

    dialog.showErrorBox('Welcome Back', \`You arrived from: ${commandLine.pop().slice(0, -1)}\`)
  })

  // Create mainWindow, load the rest of the app, etc...
  app.whenReady().then(() => {
    createWindow()
  })

  app.on('open-url', (event, url) => {
    dialog.showErrorBox('Welcome Back', \`You arrived from: ${url}\`)
  })
}

function createWindow () {
  // Create the browser window.
  mainWindow = new BrowserWindow({
    width: 800,
    height: 600,
    webPreferences: {
      preload: path.join(__dirname, 'preload.js')
    }
  })

  mainWindow.loadFile('index.html')
}

// Quit when all windows are closed, except on macOS. There, it's common
// for applications and their menu bar to stay active until the user quits
// explicitly with Cmd + Q.
app.on('window-all-closed', function () {
  if (process.platform !== 'darwin') app.quit()
})

// Handle window controls via IPC
ipcMain.on('shell:open', () => {
  const pageDirectory = __dirname.replace('app.asar', 'app.asar.unpacked')
  const pagePath = path.join('file://', pageDirectory, 'index.html')
  shell.openExternal(pagePath)
})
```

#### preload.js

```js
const { contextBridge, ipcRenderer } = require('electron/renderer')

contextBridge.exposeInMainWorld('shell', {
  open: () => ipcRenderer.send('shell:open')
})
```

#### index.html

```html
<!DOCTYPE html>
<html>

<head>
  <meta charset="UTF-8">
  <!-- https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP -->
  <meta http-equiv="Content-Security-Policy" content="default-src 'self'; script-src 'self'">
  <meta http-equiv="X-Content-Security-Policy" content="default-src 'self'; script-src 'self'">
  <title>app.setAsDefaultProtocol Demo</title>
</head>

<body>
  <h1>App Default Protocol Demo</h1>

  <p>The protocol API allows us to register a custom protocol and intercept existing protocol requests.</p>
  <p>These methods allow you to set and unset the protocols your app should be the default app for. Similar to when a
    browser asks to be your default for viewing web pages.</p>

  <p>Open the <a href="../api/protocol.md">full protocol API documentation</a> in your
    browser.</p>

  -----

  <h3>Demo</h3>
  <p>
    First: Launch current page in browser
    <button id="open-in-browser" class="js-container-target demo-toggle-button">
        Click to Launch Browser
      </button>
  </p>

  <p>
    Then: Launch the app from a web link!
    <a href="electron-fiddle://open">Click here to launch the app</a>
  </p>

  ----

  <p>You can set your app as the default app to open for a specific protocol. For instance, in this demo we set this app
    as the default for <code>electron-fiddle://</code>. The demo button above will launch a page in your default
    browser with a link. Click that link and it will re-launch this app.</p>

  <h3>Packaging</h3>
  <p>This feature will only work on macOS when your app is packaged. It will not work when you're launching it in
    development from the command-line. When you package your app you'll need to make sure the macOS <code>plist</code>
    for the app is updated to include the new protocol handler. If you're using <code>@electron/packager</code> then you
    can add the flag <code>--extend-info</code> with a path to the <code>plist</code> you've created. The one for this
    app is below:</p>

  <p>
  <h5>macOS plist</h5>
  <pre><code>
    &lt;?xml version="1.0" encoding="UTF-8"?&gt;
    &lt;!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd"&gt;
            &lt;plist version="1.0"&gt;
                &lt;dict&gt;
                    &lt;key&gt;CFBundleURLTypes&lt;/key&gt;
                    &lt;array&gt;
                        &lt;dict&gt;
                            &lt;key&gt;CFBundleURLSchemes&lt;/key&gt;
                            &lt;array&gt;
                                &lt;string&gt;electron-api-demos&lt;/string&gt;
                            &lt;/array&gt;
                            &lt;key&gt;CFBundleURLName&lt;/key&gt;
                            &lt;string&gt;Electron API Demos Protocol&lt;/string&gt;
                        &lt;/dict&gt;
                    &lt;/array&gt;
                    &lt;key&gt;ElectronTeamID&lt;/key&gt;
                    &lt;string&gt;VEKTX9H2N7&lt;/string&gt;
                &lt;/dict&gt;
            &lt;/plist&gt;
        </code>
    </pre>
  <p>

    <!-- You can also require other files to run in this process -->
    <script src="./renderer.js"></script>
</body>

</html>
```

#### renderer.js

```js
// This file is required by the index.html file and will
// be executed in the renderer process for that window.
// All APIs exposed by the context bridge are available here.

// Binds the buttons to the context bridge API.
document.getElementById('open-in-browser').addEventListener('click', () => {
  window.shell.open()
})
```
