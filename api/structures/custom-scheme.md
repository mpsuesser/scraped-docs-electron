---
url: https://www.electronjs.org/docs/latest/api/structures/custom-scheme
title: "Custom Scheme"
description: ""
access_date: 2026-08-03T19:00:42.552Z
current_date: 2026-08-03T19:00:42.552Z
---

- `scheme` string - Custom schemes to be registered with options.
- `privileges` Object (optional)
	- `standard` boolean (optional) - Default false.
		- `secure` boolean (optional) - Default false.
		- `bypassCSP` boolean (optional) - Default false.
		- `allowServiceWorkers` boolean (optional) - Default false.
		- `supportFetchAPI` boolean (optional) - Default false.
		- `corsEnabled` boolean (optional) - Default false.
		- `stream` boolean (optional) - Default false.
		- `codeCache` boolean (optional) - Enable V8 code cache for the scheme, only works when `standard` is also set to true. Default false.
		- `allowExtensions` boolean (optional) - Allow Chrome extensions to be used on pages served over this protocol. Default false.
