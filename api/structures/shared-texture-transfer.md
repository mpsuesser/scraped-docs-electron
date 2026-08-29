---
url: https://www.electronjs.org/docs/latest/api/structures/shared-texture-transfer
title: "Shared Texture Transfer"
description: ""
access_date: 2026-08-29T00:42:01.290Z
current_date: 2026-08-29T00:42:01.290Z
---

- `transfer` string *Readonly* - The opaque transfer data of the shared texture. This can be transferred across Electron processes.
- `syncToken` string *Readonly* - The opaque sync token data for frame creation.
- `pixelFormat` string *Readonly* - The pixel format of the transferring texture.
- `codedSize` [Size](size.md) *Readonly* - The full dimensions of the shared texture.
- `visibleRect` [Rectangle](rectangle.md) *Readonly* - A subsection of \[0, 0, codedSize.width(), codedSize.height()\]. In common cases, it is the full section area.
- `timestamp` number *Readonly* - A timestamp in microseconds that will be reflected to `VideoFrame`.

Use `sharedTexture.subtle.finishTransferSharedTexture` to get [SharedTextureImportedSubtle](shared-texture-imported-subtle.md) back.
