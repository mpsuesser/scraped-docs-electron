---
url: https://www.electronjs.org/docs/latest/tutorial/devices
title: "Devices"
description: ""
access_date: 2026-08-03T18:12:31.121Z
current_date: 2026-08-03T18:12:31.121Z
---

Like Chromium based browsers, Electron provides access to device hardware through web APIs. For the most part these APIs work like they do in a browser, but there are some differences that need to be taken into account. The primary difference between Electron and browsers is what happens when device access is requested. In a browser, users are presented with a popup where they can grant access to an individual device. In Electron APIs are provided which can be used by a developer to either automatically pick a device or prompt users to pick a device via a developer created interface.

## Web Bluetooth API

The [Web Bluetooth API](https://web.dev/bluetooth/) can be used to communicate with bluetooth devices. In order to use this API in Electron, developers will need to handle the [`select-bluetooth-device` event on the webContents](../api/web-contents.md#event-select-bluetooth-device) associated with the device request.

Additionally, [`ses.setBluetoothPairingHandler(handler)`](../api/session.md#sessetbluetoothpairinghandlerhandler-windows-linux) can be used to handle pairing to bluetooth devices on Windows or Linux when additional validation such as a pin is needed.

### Example

This example demonstrates an Electron application that automatically selects the first available bluetooth device when the `Test Bluetooth` button is clicked.

#### main.js

```js
const { app, BrowserWindow, ipcMain } = require('electron/main')
const path = require('node:path')

let bluetoothPinCallback
let selectBluetoothCallback

function createWindow () {
  const mainWindow = new BrowserWindow({
    width: 800,
    height: 600,
    webPreferences: {
      preload: path.join(__dirname, 'preload.js')
    }
  })

  mainWindow.webContents.on('select-bluetooth-device', (event, deviceList, callback) => {
    event.preventDefault()
    selectBluetoothCallback = callback
    const result = deviceList.find((device) => {
      return device.deviceName === 'test'
    })
    if (result) {
      callback(result.deviceId)
    } else {
      // The device wasn't found so we need to either wait longer (eg until the
      // device is turned on) or until the user cancels the request
    }
  })

  ipcMain.on('cancel-bluetooth-request', (event) => {
    selectBluetoothCallback('')
  })

  // Listen for a message from the renderer to get the response for the Bluetooth pairing.
  ipcMain.on('bluetooth-pairing-response', (event, response) => {
    bluetoothPinCallback(response)
  })

  mainWindow.webContents.session.setBluetoothPairingHandler((details, callback) => {
    bluetoothPinCallback = callback
    // Send a message to the renderer to prompt the user to confirm the pairing.
    mainWindow.webContents.send('bluetooth-pairing-request', details)
  })

  mainWindow.loadFile('index.html')
}

app.whenReady().then(() => {
  createWindow()

  app.on('activate', function () {
    if (BrowserWindow.getAllWindows().length === 0) createWindow()
  })
})

app.on('window-all-closed', function () {
  if (process.platform !== 'darwin') app.quit()
})
```

#### preload.js

```js
const { contextBridge, ipcRenderer } = require('electron/renderer')

contextBridge.exposeInMainWorld('electronAPI', {
  cancelBluetoothRequest: () => ipcRenderer.send('cancel-bluetooth-request'),
  bluetoothPairingRequest: (callback) => ipcRenderer.on('bluetooth-pairing-request', () => callback()),
  bluetoothPairingResponse: (response) => ipcRenderer.send('bluetooth-pairing-response', response)
})
```

#### index.html

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8">
    <meta http-equiv="Content-Security-Policy" content="default-src 'self'; script-src 'self'">
    <title>Web Bluetooth API</title>
  </head>
  <body>
    <h1>Web Bluetooth API</h1>

    <button id="clickme">Test Bluetooth</button>
    <button id="cancel">Cancel Bluetooth Request</button>

    <p>Currently selected bluetooth device: <strong id="device-name"></strong></p>

    <script src="./renderer.js"></script>
  </body>
</html>
```

#### renderer.js

```js
async function testIt () {
  const device = await navigator.bluetooth.requestDevice({
    acceptAllDevices: true
  })
  document.getElementById('device-name').innerHTML = device.name || \`ID: ${device.id}\`
}

document.getElementById('clickme').addEventListener('click', testIt)

function cancelRequest () {
  window.electronAPI.cancelBluetoothRequest()
}

document.getElementById('cancel').addEventListener('click', cancelRequest)

window.electronAPI.bluetoothPairingRequest((event, details) => {
  const response = {}

  switch (details.pairingKind) {
    case 'confirm': {
      response.confirmed = window.confirm(\`Do you want to connect to device ${details.deviceId}?\`)
      break
    }
    case 'confirmPin': {
      response.confirmed = window.confirm(\`Does the pin ${details.pin} match the pin displayed on device ${details.deviceId}?\`)
      break
    }
    case 'providePin': {
      const pin = window.prompt(\`Please provide a pin for ${details.deviceId}.\`)
      if (pin) {
        response.pin = pin
        response.confirmed = true
      } else {
        response.confirmed = false
      }
    }
  }

  window.electronAPI.bluetoothPairingResponse(response)
})
```

## WebHID API

The [WebHID API](https://web.dev/hid/) can be used to access HID devices such as keyboards and gamepads. Electron provides several APIs for working with the WebHID API:

- The [`select-hid-device` event on the Session](../api/session.md#event-select-hid-device) can be used to select a HID device when a call to `navigator.hid.requestDevice` is made. Additionally the [`hid-device-added`](../api/session.md#event-hid-device-added) and [`hid-device-removed`](../api/session.md#event-hid-device-removed) events on the Session can be used to handle devices being plugged in or unplugged when handling the `select-hid-device` event. **Note:** These events only fire until the callback from `select-hid-device` is called. They are not intended to be used as a generic hid device listener.
- [`ses.setDevicePermissionHandler(handler)`](../api/session.md#sessetdevicepermissionhandlerhandler) can be used to provide default permissioning to devices without first calling for permission to devices via `navigator.hid.requestDevice`. Additionally, the default behavior of Electron is to store granted device permission through the lifetime of the corresponding WebContents. If longer term storage is needed, a developer can store granted device permissions (eg when handling the `select-hid-device` event) and then read from that storage with `setDevicePermissionHandler`.
- [`ses.setPermissionCheckHandler(handler)`](../api/session.md#sessetpermissioncheckhandlerhandler) can be used to disable HID access for specific origins.

### Blocklist

By default Electron employs the same [blocklist](https://wicg.github.io/webhid/#blocklist) used by Chromium. If you wish to override this behavior, you can do so by setting the `disable-hid-blocklist` flag:

```js
app.commandLine.appendSwitch('disable-hid-blocklist')
```

### Example

This example demonstrates an Electron application that automatically selects HID devices through [`ses.setDevicePermissionHandler(handler)`](../api/session.md#sessetdevicepermissionhandlerhandler) and through [`select-hid-device` event on the Session](../api/session.md#event-select-hid-device) when the `Test WebHID` button is clicked.

#### main.js

```js
const { app, BrowserWindow } = require('electron/main')

function createWindow () {
  const mainWindow = new BrowserWindow({
    width: 800,
    height: 600
  })

  mainWindow.webContents.session.on('select-hid-device', (event, details, callback) => {
    // Add events to handle devices being added or removed before the callback on
    // \`select-hid-device\` is called.
    mainWindow.webContents.session.on('hid-device-added', (event, device) => {
      console.log('hid-device-added FIRED WITH', device)
      // Optionally update details.deviceList
    })

    mainWindow.webContents.session.on('hid-device-removed', (event, device) => {
      console.log('hid-device-removed FIRED WITH', device)
      // Optionally update details.deviceList
    })

    event.preventDefault()
    if (details.deviceList && details.deviceList.length > 0) {
      callback(details.deviceList[0].deviceId)
    }
  })

  mainWindow.webContents.session.setPermissionCheckHandler((webContents, permission, requestingOrigin, details) => {
    if (permission === 'hid' && details.securityOrigin === 'file:///') {
      return true
    }
  })

  mainWindow.webContents.session.setDevicePermissionHandler((details) => {
    if (details.deviceType === 'hid' && details.origin === 'file://') {
      return true
    }
  })

  mainWindow.loadFile('index.html')
}

app.whenReady().then(() => {
  createWindow()

  app.on('activate', function () {
    if (BrowserWindow.getAllWindows().length === 0) createWindow()
  })
})

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
    <meta http-equiv="Content-Security-Policy" content="default-src 'self'; script-src 'self'">
    <title>WebHID API</title>
  </head>
  <body>
    <h1>WebHID API</h1>

    <button id="clickme">Test WebHID</button>

    <h3>HID devices automatically granted access via <i>setDevicePermissionHandler</i></h3>
    <div id="granted-devices"></div>

    <h3>HID devices automatically granted access via <i>select-hid-device</i></h3>
    <div id="granted-devices2"></div>

    <script src="./renderer.js"></script>
  </body>
</html>
```

#### renderer.js

```js
function formatDevices (devices) {
  return devices.map(device => device.productName).join('<hr>')
}

async function testIt () {
  document.getElementById('granted-devices').innerHTML = formatDevices(await navigator.hid.getDevices())
  document.getElementById('granted-devices2').innerHTML = formatDevices(await navigator.hid.requestDevice({ filters: [] }))
}

document.getElementById('clickme').addEventListener('click', testIt)
```

## Web Serial API

The [Web Serial API](https://web.dev/serial/) can be used to access serial devices that are connected via serial port, USB, or Bluetooth. In order to use this API in Electron, developers will need to handle the [`select-serial-port` event on the Session](../api/session.md#event-select-serial-port) associated with the serial port request.

There are several additional APIs for working with the Web Serial API:

- The [`serial-port-added`](../api/session.md#event-serial-port-added) and [`serial-port-removed`](../api/session.md#event-serial-port-removed) events on the Session can be used to handle devices being plugged in or unplugged when handling the `select-serial-port` event. **Note:** These events only fire until the callback from `select-serial-port` is called. They are not intended to be used as a generic serial port listener.
- [`ses.setDevicePermissionHandler(handler)`](../api/session.md#sessetdevicepermissionhandlerhandler) can be used to provide default permissioning to devices without first calling for permission to devices via `navigator.serial.requestPort`. Additionally, the default behavior of Electron is to store granted device permission through the lifetime of the corresponding WebContents. If longer term storage is needed, a developer can store granted device permissions (eg when handling the `select-serial-port` event) and then read from that storage with `setDevicePermissionHandler`.
- [`ses.setPermissionCheckHandler(handler)`](../api/session.md#sessetpermissioncheckhandlerhandler) can be used to disable serial access for specific origins.

### Blocklist

By default Electron employs the same [blocklist](https://wicg.github.io/serial/#blocklist) used by Chromium. If you wish to override this behavior, you can do so by setting the `disable-serial-blocklist` flag:

```js
app.commandLine.appendSwitch('disable-serial-blocklist')
```

### Example

This example demonstrates an Electron application that automatically selects serial devices through [`ses.setDevicePermissionHandler(handler)`](../api/session.md#sessetdevicepermissionhandlerhandler) as well as demonstrating selecting the first available Arduino Uno serial device (if connected) through [`select-serial-port` event on the Session](../api/session.md#event-select-serial-port) when the `Test Web Serial` button is clicked.

#### main.js

```js
const { app, BrowserWindow } = require('electron/main')

function createWindow () {
  const mainWindow = new BrowserWindow({
    width: 800,
    height: 600
  })

  mainWindow.webContents.session.on('select-serial-port', (event, portList, webContents, callback) => {
    // Add listeners to handle ports being added or removed before the callback for \`select-serial-port\`
    // is called.
    mainWindow.webContents.session.on('serial-port-added', (event, port) => {
      console.log('serial-port-added FIRED WITH', port)
      // Optionally update portList to add the new port
    })

    mainWindow.webContents.session.on('serial-port-removed', (event, port) => {
      console.log('serial-port-removed FIRED WITH', port)
      // Optionally update portList to remove the port
    })

    event.preventDefault()
    if (portList && portList.length > 0) {
      callback(portList[0].portId)
    } else {
      // eslint-disable-next-line n/no-callback-literal
      callback('') // Could not find any matching devices
    }
  })

  mainWindow.webContents.session.setPermissionCheckHandler((webContents, permission, requestingOrigin, details) => {
    if (permission === 'serial' && details.securityOrigin === 'file:///') {
      return true
    }

    return false
  })

  mainWindow.webContents.session.setDevicePermissionHandler((details) => {
    if (details.deviceType === 'serial' && details.origin === 'file://') {
      return true
    }

    return false
  })

  mainWindow.loadFile('index.html')

  mainWindow.webContents.openDevTools()
}

app.whenReady().then(() => {
  createWindow()

  app.on('activate', function () {
    if (BrowserWindow.getAllWindows().length === 0) createWindow()
  })
})

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
    <meta http-equiv="Content-Security-Policy" content="default-src 'self'; script-src 'self'">
    <title>Web Serial API</title>
  <body>
    <h1>Web Serial API</h1>

    <button id="clickme">Test Web Serial API</button>

    <p>Matching Arduino Uno device: <strong id="device-name""></strong></p>

    <script src="./renderer.js"></script>
  </body>
</html>
```

#### renderer.js

```js
async function testIt () {
  const filters = [
    { usbVendorId: 0x2341, usbProductId: 0x0043 },
    { usbVendorId: 0x2341, usbProductId: 0x0001 }
  ]
  try {
    const port = await navigator.serial.requestPort({ filters })
    const portInfo = port.getInfo()
    document.getElementById('device-name').innerHTML = \`vendorId: ${portInfo.usbVendorId} | productId: ${portInfo.usbProductId} \`
  } catch (ex) {
    if (ex.name === 'NotFoundError') {
      document.getElementById('device-name').innerHTML = 'Device NOT found'
    } else {
      document.getElementById('device-name').innerHTML = ex
    }
  }
}

document.getElementById('clickme').addEventListener('click', testIt)
```

## WebUSB API

The [WebUSB API](https://web.dev/usb/) can be used to access USB devices. Electron provides several APIs for working with the WebUSB API:

- The [`select-usb-device` event on the Session](../api/session.md#event-select-usb-device) can be used to select a USB device when a call to `navigator.usb.requestDevice` is made. Additionally the [`usb-device-added`](../api/session.md#event-usb-device-added) and [`usb-device-removed`](../api/session.md#event-usb-device-removed) events on the Session can be used to handle devices being plugged in or unplugged when handling the `select-usb-device` event. **Note:** These two events only fire until the callback from `select-usb-device` is called. They are not intended to be used as a generic usb device listener.
- The [`usb-device-revoked` event on the Session](../api/session.md#event-usb-device-revoked) can be used to respond when [device.forget()](https://developer.chrome.com/articles/usb/#revoke-access) is called on a USB device.
- [`ses.setDevicePermissionHandler(handler)`](../api/session.md#sessetdevicepermissionhandlerhandler) can be used to provide default permissioning to devices without first calling for permission to devices via `navigator.usb.requestDevice`. Additionally, the default behavior of Electron is to store granted device permission through the lifetime of the corresponding WebContents. If longer term storage is needed, a developer can store granted device permissions (eg when handling the `select-usb-device` event) and then read from that storage with `setDevicePermissionHandler`.
- [`ses.setPermissionCheckHandler(handler)`](../api/session.md#sessetpermissioncheckhandlerhandler) can be used to disable USB access for specific origins.
- [\`ses.setUSBProtectedClassesHandler](../api/session.md#sessetusbprotectedclasseshandlerhandler) can be used to allow usage of [protected USB classes](https://wicg.github.io/webusb/#usbinterface-interface) that are not available by default.

### Blocklist

By default Electron employs the same [blocklist](https://wicg.github.io/webusb/#blocklist) used by Chromium. If you wish to override this behavior, you can do so by setting the `disable-usb-blocklist` flag:

```js
app.commandLine.appendSwitch('disable-usb-blocklist')
```

### Example

This example demonstrates an Electron application that automatically selects USB devices (if they are attached) through [`ses.setDevicePermissionHandler(handler)`](../api/session.md#sessetdevicepermissionhandlerhandler) and through [`select-usb-device` event on the Session](../api/session.md#event-select-usb-device) when the `Test WebUSB` button is clicked.

#### main.js

```js
const { app, BrowserWindow } = require('electron/main')

function createWindow () {
  const mainWindow = new BrowserWindow({
    width: 800,
    height: 600
  })

  let grantedDeviceThroughPermHandler

  mainWindow.webContents.session.on('select-usb-device', (event, details, callback) => {
    // Add events to handle devices being added or removed before the callback on
    // \`select-usb-device\` is called.
    mainWindow.webContents.session.on('usb-device-added', (event, device) => {
      console.log('usb-device-added FIRED WITH', device)
      // Optionally update details.deviceList
    })

    mainWindow.webContents.session.on('usb-device-removed', (event, device) => {
      console.log('usb-device-removed FIRED WITH', device)
      // Optionally update details.deviceList
    })

    event.preventDefault()
    if (details.deviceList && details.deviceList.length > 0) {
      const deviceToReturn = details.deviceList.find((device) => {
        return !grantedDeviceThroughPermHandler || (device.deviceId !== grantedDeviceThroughPermHandler.deviceId)
      })
      if (deviceToReturn) {
        callback(deviceToReturn.deviceId)
      } else {
        callback()
      }
    }
  })

  mainWindow.webContents.session.setPermissionCheckHandler((webContents, permission, requestingOrigin, details) => {
    if (permission === 'usb' && details.securityOrigin === 'file:///') {
      return true
    }
  })

  mainWindow.webContents.session.setDevicePermissionHandler((details) => {
    if (details.deviceType === 'usb' && details.origin === 'file://') {
      if (!grantedDeviceThroughPermHandler) {
        grantedDeviceThroughPermHandler = details.device
        return true
      } else {
        return false
      }
    }
  })

  mainWindow.webContents.session.setUSBProtectedClassesHandler((details) => {
    return details.protectedClasses.filter((usbClass) => {
      // Exclude classes except for audio classes
      return usbClass.indexOf('audio') === -1
    })
  })

  mainWindow.loadFile('index.html')
}

app.whenReady().then(() => {
  createWindow()

  app.on('activate', function () {
    if (BrowserWindow.getAllWindows().length === 0) createWindow()
  })
})

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
    <meta http-equiv="Content-Security-Policy" content="default-src 'self'; script-src 'self'">
    <title>WebUSB API</title>
  </head>
  <body>
    <h1>WebUSB API</h1>

    <button id="clickme">Test WebUSB</button>

    <h3>USB devices automatically granted access via <i>setDevicePermissionHandler</i></h3>
    <div id="granted-devices"></div>

    <h3>USB devices automatically granted access via <i>select-usb-device</i></h3>
    <div id="granted-devices2"></div>

    <script src="./renderer.js"></script>
  </body>
</html>
```

#### renderer.js

```js
function getDeviceDetails (device) {
  return device.productName || \`Unknown device ${device.deviceId}\`
}

async function testIt () {
  const noDevicesFoundMsg = 'No devices found'
  const grantedDevices = await navigator.usb.getDevices()
  let grantedDeviceList = ''
  if (grantedDevices.length > 0) {
    for (const device of grantedDevices) {
      grantedDeviceList += \`<hr>${getDeviceDetails(device)}</hr>\`
    }
  } else {
    grantedDeviceList = noDevicesFoundMsg
  }
  document.getElementById('granted-devices').innerHTML = grantedDeviceList

  grantedDeviceList = ''
  try {
    const grantedDevice = await navigator.usb.requestDevice({
      filters: []
    })
    grantedDeviceList += \`<hr>${getDeviceDetails(grantedDevice)}</hr>\`
  } catch (ex) {
    if (ex.name === 'NotFoundError') {
      grantedDeviceList = noDevicesFoundMsg
    }
  }
  document.getElementById('granted-devices2').innerHTML = grantedDeviceList
}

document.getElementById('clickme').addEventListener('click', testIt)
```
