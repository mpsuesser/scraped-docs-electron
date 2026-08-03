---
url: https://www.electronjs.org/docs/latest/tutorial/window-customization
title: "Window Customization"
description: ""
access_date: 2026-08-03T17:26:37.553Z
current_date: 2026-08-03T17:26:37.553Z
---

The [`BrowserWindow`](../api/browser-window.md) module is the foundation of your Electron application, and it exposes many APIs that let you customize the look and behavior of your app’s windows. This section covers how to implement various use cases for window customization on macOS, Windows, and Linux.

> [!-secondary] -secondary
> note
> 
> `BrowserWindow` is a subclass of the [`BaseWindow`](../api/base-window.md) module. Both modules allow you to create and manage application windows in Electron, with the main difference being that `BrowserWindow` supports a single, full size web view while `BaseWindow` supports composing many web views. `BaseWindow` can be used interchangeably with `BrowserWindow` in the examples of the documents in this section.

## [📄️Custom Title Bar](custom-title-bar.md)

Application windows have a default chrome applied by the OS. Not to be confused with the Google Chrome browser, window \_chrome\_ refers to the parts of the window (e.g. title bar, toolbars, controls) that are not a part of the main web content. While the default title bar provided by the OS chrome is sufficient for simple use cases, many applications opt to remove it. Implementing a custom title bar can help your application feel more modern and consistent across platforms.

## [📄️Custom Window Interactions](custom-window-interactions.md)

By default, windows are dragged using the title bar provided by the OS chrome. Apps that remove the default title bar need to use the app-region CSS property to define specific areas that can be used to drag the window. Setting app-region: drag marks a rectangular area as draggable.

## [📄️Custom Window Styles](custom-window-styles.md)

!Frameless Window
