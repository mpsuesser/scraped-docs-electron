---
url: https://www.electronjs.org/docs/latest/tutorial/navigation-history
title: "Navigation History"
description: ""
access_date: 2026-08-03T17:26:37.553Z
current_date: 2026-08-03T17:26:37.553Z
---

## Overview

The [NavigationHistory](../api/navigation-history.md) class allows you to manage and interact with the browsing history of your Electron application. This powerful feature enables you to create intuitive navigation experiences for your users.

## Accessing NavigationHistory

Navigation history is stored per [`WebContents`](../api/web-contents.md) instance. To access a specific instance of the NavigationHistory class, use the WebContents class's [`contents.navigationHistory` instance property](../api/web-contents.md#contentsnavigationhistory-readonly).

```js
const { BrowserWindow } = require('electron')

const mainWindow = new BrowserWindow()
const { navigationHistory } = mainWindow.webContents
```

## Navigating through history

Easily implement back and forward navigation:

```js
// Go back
if (navigationHistory.canGoBack()) {
  navigationHistory.goBack()
}

// Go forward
if (navigationHistory.canGoForward()) {
  navigationHistory.goForward()
}
```

## Accessing history entries

Retrieve and display the user's browsing history:

```js
const entries = navigationHistory.getAllEntries()

entries.forEach((entry) => {
  console.log(\`${entry.title}: ${entry.url}\`)
})
```

Each navigation entry corresponds to a specific page. The indexing system follows a sequential order:

- Index 0: Represents the earliest visited page.
- Index N: Represents the most recent page visited.

## Navigating to specific entries

Allow users to jump to any point in their browsing history:

```js
// Navigate to the 5th entry in the history, if the index is valid
navigationHistory.goToIndex(4)

// Navigate to the 2nd entry forward from the current position
if (navigationHistory.canGoToOffset(2)) {
  navigationHistory.goToOffset(2)
}
```

## Restoring history

A common flow is that you want to restore the history of a webContents - for instance to implement an "undo close tab" feature. To do so, you can call `navigationHistory.restore({ index, entries })`. This will restore the webContent's navigation history and the webContents location in said history, meaning that `goBack()` and `goForward()` navigate you through the stack as expected.

```js
const firstWindow = new BrowserWindow()

// Later, you want a second window to have the same history and navigation position
async function restore () {
  const entries = firstWindow.webContents.navigationHistory.getAllEntries()
  const index = firstWindow.webContents.navigationHistory.getActiveIndex()

  const secondWindow = new BrowserWindow()
  await secondWindow.webContents.navigationHistory.restore({ index, entries })
}
```

Here's a full example that you can open with Electron Fiddle:

- main.js
- preload.js
- index.html
- renderer.js
- style.css

```js
const { app, BrowserWindow, BrowserView, ipcMain } = require('electron')
const path = require('path')

function createWindow () {
  const mainWindow = new BrowserWindow({
    width: 1000,
    height: 800,
    webPreferences: {
      preload: path.join(__dirname, 'preload.js'),
      nodeIntegration: false,
      contextIsolation: true
    }
  })

  mainWindow.loadFile('index.html')

  const view = new BrowserView()
  mainWindow.setBrowserView(view)
  view.setBounds({ x: 0, y: 100, width: 1000, height: 800 })
  view.setAutoResize({ width: true, height: true })

  const navigationHistory = view.webContents.navigationHistory
  ipcMain.handle('nav:back', () =>
    navigationHistory.goBack()
  )

  ipcMain.handle('nav:forward', () => {
    navigationHistory.goForward()
  })

  ipcMain.handle('nav:canGoBack', () => navigationHistory.canGoBack())
  ipcMain.handle('nav:canGoForward', () => navigationHistory.canGoForward())
  ipcMain.handle('nav:loadURL', (_, url) =>
    view.webContents.loadURL(url)
  )
  ipcMain.handle('nav:getCurrentURL', () => view.webContents.getURL())
  ipcMain.handle('nav:getHistory', () => {
    return navigationHistory.getAllEntries()
  })

  view.webContents.on('did-navigate', () => {
    mainWindow.webContents.send('nav:updated')
  })

  view.webContents.on('did-navigate-in-page', () => {
    mainWindow.webContents.send('nav:updated')
  })
}

app.whenReady().then(createWindow)

app.on('window-all-closed', () => {
  if (process.platform !== 'darwin') app.quit()
})

app.on('activate', () => {
  if (BrowserWindow.getAllWindows().length === 0) createWindow()
})
```
