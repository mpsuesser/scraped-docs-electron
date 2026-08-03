---
url: https://www.electronjs.org/docs/latest/tutorial/application-menu
title: "Application Menu"
description: ""
access_date: 2026-08-03T18:12:31.121Z
current_date: 2026-08-03T18:12:31.121Z
---

Each Electron app has a single top-level application menu.

- On macOS, this menu is shown in the system [menu bar](https://support.apple.com/en-ca/guide/mac-help/mchlp1446/mac).
- On Windows and Linux, this menu is shown at the top of each [BaseWindow](../api/base-window.md).

## Building application menus

The application menu can be set by passing a [Menu](../api/menu.md) instance into the [`Menu.setApplicationMenu`](../api/menu.md#menusetapplicationmenumenu) static function.

When building an application menu in Electron, each top-level array menu item **must be a submenu**.

Electron will set a default menu for your app if this API is never called. Below is an example of that default menu being created manually using shorthand [`MenuItem` roles](menus.md#roles).

```js
const { shell } = require('electron/common')
const { app, Menu } = require('electron/main')

const isMac = process.platform === 'darwin'
const template = [
  // { role: 'appMenu' }
  ...(isMac
    ? [{
        label: app.name,
        submenu: [
          { role: 'about' },
          { type: 'separator' },
          { role: 'services' },
          { type: 'separator' },
          { role: 'hide' },
          { role: 'hideOthers' },
          { role: 'unhide' },
          { type: 'separator' },
          { role: 'quit' }
        ]
      }]
    : []),
  // { role: 'fileMenu' }
  {
    label: 'File',
    submenu: [
      isMac ? { role: 'close' } : { role: 'quit' }
    ]
  },
  // { role: 'editMenu' }
  {
    label: 'Edit',
    submenu: [
      { role: 'undo' },
      { role: 'redo' },
      { type: 'separator' },
      { role: 'cut' },
      { role: 'copy' },
      { role: 'paste' },
      ...(isMac
        ? [
            { role: 'pasteAndMatchStyle' },
            { role: 'delete' },
            { role: 'selectAll' },
            { type: 'separator' },
            {
              label: 'Speech',
              submenu: [
                { role: 'startSpeaking' },
                { role: 'stopSpeaking' }
              ]
            }
          ]
        : [
            { role: 'delete' },
            { type: 'separator' },
            { role: 'selectAll' }
          ])
    ]
  },
  // { role: 'viewMenu' }
  {
    label: 'View',
    submenu: [
      { role: 'reload' },
      { role: 'forceReload' },
      { role: 'toggleDevTools' },
      { type: 'separator' },
      { role: 'resetZoom' },
      { role: 'zoomIn' },
      { role: 'zoomOut' },
      { type: 'separator' },
      { role: 'togglefullscreen' }
    ]
  },
  // { role: 'windowMenu' }
  {
    label: 'Window',
    submenu: [
      { role: 'minimize' },
      { role: 'zoom' },
      ...(isMac
        ? [
            { type: 'separator' },
            { role: 'front' },
            { type: 'separator' },
            { role: 'window' }
          ]
        : [
            { role: 'close' }
          ])
    ]
  },
  {
    role: 'help',
    submenu: [
      {
        label: 'Learn More',
        click: async () => {
          const { shell } = require('electron')
          await shell.openExternal('https://electronjs.org')
        }
      }
    ]
  }
]

const menu = Menu.buildFromTemplate(template)
Menu.setApplicationMenu(menu)
```

> **Info:**
> 
> On macOS, the first submenu of the application menu will **always** have your application's name as its label. In general, you can populate this submenu by conditionally adding a menu item with the `appMenu` role.

> **Tip:**
> 
> For additional descriptions of available roles, see the [`MenuItem` roles](menus.md#roles) section of the general Menus guide.

### Using standard OS menu roles

Defining each submenu explicitly can get very verbose. If you want to re-use default submenus in your app, you can use various submenu-related roles provided by Electron.

```js
const { shell } = require('electron/common')
const { app, Menu } = require('electron/main')

const template = [
  ...(process.platform === 'darwin'
    ? [{ role: 'appMenu' }]
    : []),
  { role: 'fileMenu' },
  { role: 'editMenu' },
  { role: 'viewMenu' },
  { role: 'windowMenu' },
  {
    role: 'help',
    submenu: [
      {
        label: 'Learn More',
        click: async () => {
          const { shell } = require('electron')
          await shell.openExternal('https://electronjs.org')
        }
      }
    ]
  }
]

const menu = Menu.buildFromTemplate(template)
Menu.setApplicationMenu(menu)
```

> **Note:**
> 
> On macOS, the `help` role defines a top-level Help submenu that has a search bar for other menu items. It requires items to be added to its `submenu` to function.

## Setting window-specific application menus Linux Windows

Since the root application menu exists on each `BaseWindow` on Windows and Linux, you can override it with a window-specific `Menu` instance via the [`win.setMenu`](../api/browser-window.md#winsetmenumenu-linux-windows) method.

```js
const { BrowserWindow, Menu } = require('electron/main')

const win = new BrowserWindow()
const menu = Menu.buildFromTemplate([
  {
    label: 'my custom menu',
    submenu: [
      { role: 'copy' },
      { role: 'paste' }
    ]
  }
])
win.setMenu(menu)
```

> **Tip:**
> 
> You can remove a specific window's application menu by calling the [`win.removeMenu`](../api/base-window.md#winremovemenu-linux-windows) API.
