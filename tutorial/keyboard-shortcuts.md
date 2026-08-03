---
url: https://www.electronjs.org/docs/latest/tutorial/keyboard-shortcuts
title: "Keyboard Shortcuts"
description: ""
access_date: 2026-08-03T19:00:42.552Z
current_date: 2026-08-03T19:00:42.552Z
---

## Accelerators

Accelerators are strings that can be used to represent keyboard shortcuts throughout your Electron. These strings can contain multiple modifier keys and a single key code joined by the `+` character.

> **Note:**
> 
> Accelerators are **case-insensitive**.

### Available modifiers

- `Command` (or `Cmd` for short)
- `Control` (or `Ctrl` for short)
- `CommandOrControl` (or `CmdOrCtrl` for short)
- `Alt`
- `Option`
- `AltGr`
- `Shift`
- `Super` (or `Meta` as alias)

### Available key codes

- `0` to `9`
- `A` to `Z`
- `F1` to `F24`
- Various Punctuation: `)`, `!`, `@`, `#`, `$`, `%`, `^`, `&`, `*`, `(`, `:`, `;`, `:`, `+`, `=`, `<`, `,`, `_`, `-`, `>`, `.`, `?`, `/`, `~`, `` ` ``, `{`, `]`, `[`, `|`, `\`, `}`, `"`
- `Plus`
- `Space`
- `Tab`
- `Capslock`
- `Numlock`
- `Scrolllock`
- `Backspace`
- `Delete`
- `Insert`
- `Return` (or `Enter` as alias)
- `Up`, `Down`, `Left` and `Right`
- `Home` and `End`
- `PageUp` and `PageDown`
- `Escape` (or `Esc` for short)
- `VolumeUp`, `VolumeDown` and `VolumeMute`
- `MediaNextTrack`, `MediaPreviousTrack`, `MediaStop` and `MediaPlayPause`
- `PrintScreen`
- NumPad Keys
	- `num0` - `num9`
		- `numdec` - decimal key
		- `numadd` - numpad `+` key
		- `numsub` - numpad `-` key
		- `nummult` - numpad `*` key
		- `numdiv` - numpad `÷` key

### Cross-platform modifiers

Many modifier accelerators map to different keys between operating systems.

| Modifier | macOS | Windows and Linux |
| --- | --- | --- |
| `CommandOrControl` | Command (⌘) | Control |
| `Command` | Command (⌘) | N/A |
| `Control` | Control (^) | Control |
| `Alt` | Option (⌥) | Alt |
| `Option` | Option (⌥) | N/A |
| `Super` (`Meta`) | Command (⌘) | Windows (⊞) |

> **Info:**
> 
> - On Linux and Windows, the `Command` modifier does not have any effect. In general, you should use the `CommandOrControl` modifier instead, which represents ⌘ Cmd on macOS and Ctrl on Linux and Windows.
> - Use `Alt` instead of `Option`. The ⌥ Opt key only exists on macOS, whereas the `Alt` will map to the appropriate modifier on all platforms.

#### Examples

Here are some examples of cross-platform Electron accelerators for common editing operations:

- Copy: `CommandOrControl+C`
- Paste: `CommandOrControl+V`
- Undo: `CommandOrControl+Z`
- Redo: `CommandOrControl+Shift+Z`

## Local shortcuts

**Local** keyboard shortcuts are triggered only when the application is focused. These shortcuts map to specific menu items within the app's main [application menu](application-menu.md).

To define a local keyboard shortcut, you need to configure the `accelerator` property when creating a [MenuItem](../api/menu-item.md). Then, the `click` event associated to that menu item will trigger upon using that accelerator.

```js
const { dialog, Menu, MenuItem } = require('electron/main')

const menu = new Menu()

// The first submenu needs to be the app menu on macOS
if (process.platform === 'darwin') {
  const appMenu = new MenuItem({ role: 'appMenu' })
  menu.append(appMenu)
}

const submenu = Menu.buildFromTemplate([{
  label: 'Open a Dialog',
  click: () => dialog.showMessageBox({ message: 'Hello World!' }),
  accelerator: 'CommandOrControl+Alt+R'
}])
menu.append(new MenuItem({ label: 'Custom Menu', submenu }))

Menu.setApplicationMenu(menu)
```

In the above example, a native "Hello World" dialog will open when pressing ⌘ Cmd + ⌥ Opt + R on macOS or Ctrl + Alt + R on other platforms.

> **Tip:**
> 
> Accelerators can work even when menu items are hidden. On macOS, this feature can be disabled by setting `acceleratorWorksWhenHidden: false` when building a `MenuItem`.

> **Tip:**
> 
> On Windows and Linux, the `registerAccelerator` property of the `MenuItem` can be set to `false` so that the accelerator is visible in the system menu but not enabled.

## Global shortcuts

**Global** keyboard shortcuts work even when your app is out of focus. To configure a global keyboard shortcut, you can use the [`globalShortcut.register`](../api/global-shortcut.md#globalshortcutregisteraccelerator-callback) function to specify shortcuts.

```js
const { dialog, globalShortcut } = require('electron/main')

globalShortcut.register('CommandOrControl+Alt+R', () => {
  dialog.showMessageBox({ message: 'Hello World!' })
})
```

To later unregister a shortcut, you can use the [`globalShortcut.unregisterAccelerator`](../api/global-shortcut.md#globalshortcutunregisteraccelerator) function.

```js
const { globalShortcut } = require('electron/main')

globalShortcut.unregister('CommandOrControl+Alt+R')
```

> **Warning:**
> 
> On macOS, there's a long-standing bug with `globalShortcut` that prevents it from working with keyboard layouts other than QWERTY ([electron/electron#19747](https://github.com/electron/electron/issues/19747)).

## Shortcuts within a window

### In the renderer process

If you want to handle keyboard shortcuts within a [BaseWindow](../api/base-window.md), you can listen for the [`keyup`](https://developer.mozilla.org/en-US/docs/Web/API/Element/keyup_event) and [`keydown`](https://developer.mozilla.org/en-US/docs/Web/API/Element/keydown_event) DOM Events inside the renderer process using the [addEventListener](https://developer.mozilla.org/en-US/docs/Web/API/EventTarget/addEventListener) API.

#### main.js

```js
// Modules to control application life and create native browser window
const { app, BrowserWindow } = require('electron/main')

function createWindow () {
  // Create the browser window.
  const mainWindow = new BrowserWindow({
    width: 800,
    height: 600
  })

  // and load the index.html of the app.
  mainWindow.loadFile('index.html')
}

// This method will be called when Electron has finished
// initialization and is ready to create browser windows.
// Some APIs can only be used after this event occurs.
app.whenReady().then(() => {
  createWindow()

  app.on('activate', function () {
    // On macOS it's common to re-create a window in the app when the
    // dock icon is clicked and there are no other windows open.
    if (BrowserWindow.getAllWindows().length === 0) createWindow()
  })
})

// Quit when all windows are closed, except on macOS. There, it's common
// for applications and their menu bar to stay active until the user quits
// explicitly with Cmd + Q.
app.on('window-all-closed', function () {
  if (process.platform !== 'darwin') app.quit()
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
    <title>Hello World!</title>
  </head>
  <body>
    <h1>Hello World!</h1>

    <p>Hit any key with this window focused to see it captured here.</p>
    <div><span>Last Key Pressed: </span><span id="last-keypress"></span></div>
    <script src="./renderer.js"></script>
  </body>
</html>
```

#### renderer.js

```js
function handleKeyPress (event) {
  // You can put code here to handle the keypress.
  document.getElementById('last-keypress').innerText = event.key
  console.log(\`You pressed ${event.key}\`)
}

window.addEventListener('keyup', handleKeyPress, true)
```

> **Note:**
> 
> The third parameter `true` indicates that the listener will always receive key presses before other listeners so they can't have `stopPropagation()` called on them.

#### Intercepting events in the main process

The [`before-input-event`](../api/web-contents.md#event-before-input-event) event is emitted before dispatching `keydown` and `keyup` events in the renderer process. It can be used to catch and handle custom shortcuts that are not visible in the menu.

```js
const { app, BrowserWindow } = require('electron/main')

app.whenReady().then(() => {
  const win = new BrowserWindow()

  win.loadFile('index.html')
  win.webContents.on('before-input-event', (event, input) => {
    if (input.control && input.key.toLowerCase() === 'i') {
      console.log('Pressed Control+I')
      event.preventDefault()
    }
  })
})
```
