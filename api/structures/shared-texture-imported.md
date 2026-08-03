---
url: https://www.electronjs.org/docs/latest/api/structures/shared-texture-imported
title: "Shared Texture Imported"
description: ""
access_date: 2026-08-03T19:38:49.815Z
current_date: 2026-08-03T19:38:49.815Z
---

- `textureId` string - The unique identifier of the imported shared texture.
- `getVideoFrame` Function< [VideoFrame](https://developer.mozilla.org/en-US/docs/Web/API/VideoFrame) > - Create a `VideoFrame` that uses the imported shared texture in the current process. You can call `VideoFrame.close()` once you've finished using the object. The underlying resources will wait for GPU finish internally.
- `release` Function - Release this object's reference of the imported shared texture. The underlying resource will be alive until every reference is released.
- `subtle` [SharedTextureImportedSubtle](shared-texture-imported-subtle.md) - Provides subtle APIs to interact with the imported shared texture for advanced users.
