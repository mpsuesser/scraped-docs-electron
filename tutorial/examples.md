---
url: https://www.electronjs.org/docs/latest/tutorial/examples
title: "Examples"
description: ""
access_date: 2026-08-03T17:26:37.553Z
current_date: 2026-08-03T17:26:37.553Z
---

In this section, we have collected a set of guides for common features that you may want to implement in your Electron application. Each guide contains a practical example in a minimal, self-contained example app. The easiest way to run these examples is by downloading [Electron Fiddle](https://www.electronjs.org/fiddle).

Once Fiddle is installed, you can press on the "Open in Fiddle" button that you will find below code samples like the following one:

```js
const { app, BrowserWindow } = require('electron/main')
const path = require('node:path')

function createWindow () {
  const win = new BrowserWindow({
    width: 800,
    height: 600,
    webPreferences: {
      preload: path.join(__dirname, 'preload.js')
    }
  })

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

## How to...?

You can find the full list of "How to?" in the sidebar. If there is something that you would like to do that is not documented, please join our [Discord server](https://discord.com/invite/electronjs) and let us know!
