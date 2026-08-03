---
url: https://www.electronjs.org/docs/latest/tutorial/represented-file
title: "Represented File"
description: ""
access_date: 2026-08-03T18:54:45.323Z
current_date: 2026-08-03T18:54:45.323Z
---

## Overview

On macOS, you can set a represented file for any window in your application. The represented file's icon will be shown in the title bar, and when users `Command-Click` or `Control-Click`, a popup with a path to the file will be shown.

![Represented File](https://cloud.githubusercontent.com/assets/639601/5082061/670a949a-6f14-11e4-987a-9aaa04b23c1d.png)

> NOTE: The screenshot above is an example where this feature is used to indicate the currently opened file in the Atom text editor.

You can also set the edited state for a window so that the file icon can indicate whether the document in this window has been modified.

To set the represented file of window, you can use the [BrowserWindow.setRepresentedFilename](../api/browser-window.md#winsetrepresentedfilenamefilename-macos) and [BrowserWindow.setDocumentEdited](../api/browser-window.md#winsetdocumenteditededited-macos) APIs.

## Example

#### main.js

```js
const { app, BrowserWindow } = require('electron/main')
const os = require('node:os')

function createWindow () {
  const win = new BrowserWindow({
    width: 800,
    height: 600
  })

  win.setRepresentedFilename(os.homedir())
  win.setDocumentEdited(true)

  win.loadFile('index.html')
}

app.whenReady().then(() => {
  createWindow()

  app.on('activate', () => {
    if (BrowserWindow.getAllWindows().length === 0) {
      createWindow()
    }
  })
})

app.on('window-all-closed', () => {
  if (process.platform !== 'darwin') {
    app.quit()
  }
})
```

#### index.html

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>Hello World!</title>
    <meta http-equiv="Content-Security-Policy" content="script-src 'self' 'unsafe-inline';" />
    <link rel="stylesheet" type="text/css" href="./styles.css">
</head>
<body>
    <h1>Hello World!</h1>
    <p>
      Click on the title with the <pre>Command</pre> or <pre>Control</pre> key pressed.
      You should see a popup with the represented file at the top.
    </p>
  </body>
</body>
</html>
```

After launching the Electron application, click on the title with `Command` or `Control` key pressed. You should see a popup with the represented file at the top. In this guide, this is the current user's home directory:

![Represented file](https://www.electronjs.org/assets/images/represented-file-95ff830878c432520d91d1e95f4b55ac.png)
