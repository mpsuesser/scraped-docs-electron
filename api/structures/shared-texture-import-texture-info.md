---
url: https://www.electronjs.org/docs/latest/api/structures/shared-texture-import-texture-info
title: "Shared Texture Import Texture Info"
description: ""
access_date: 2026-08-03T18:54:45.323Z
current_date: 2026-08-03T18:54:45.323Z
---

- `pixelFormat` string - The pixel format of the texture.
	- `bgra` - 32bpp BGRA (byte-order), 1 plane.
		- `rgba` - 32bpp RGBA (byte-order), 1 plane.
		- `rgbaf16` - Half float RGBA, 1 plane.
		- `nv12` - 12bpp with Y plane followed by a 2x2 interleaved UV plane.
		- `nv16` - 16bpp with Y plane followed by a 2x1 interleaved UV plane.
		- `p010le` - 4:2:0 10-bit YUV (little-endian), Y plane followed by a 2x2 interleaved UV plane.
- `colorSpace` [ColorSpace](color-space.md) (optional) - The color space of the texture.
- `codedSize` [Size](size.md) - The full dimensions of the shared texture.
- `visibleRect` [Rectangle](rectangle.md) (optional) - A subsection of \[0, 0, codedSize.width, codedSize.height\]. In common cases, it is the full section area.
- `timestamp` number (optional) - A timestamp in microseconds that will be reflected to `VideoFrame`.
- `handle` [SharedTextureHandle](shared-texture-handle.md) - The shared texture handle.
