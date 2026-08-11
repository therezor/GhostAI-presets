---
name: resize-image
description: Resize, convert, or crop a single still image. Use whenever the user wants to resize, scale, shrink, convert the format of, crop, or compress a photo or image — .jpg/.png/.heic/.webp/.tiff and friends. Use transcode-video for anything with a timeline; this is for one picture.
agents: media-ops
---

# One picture is a magick job, not a filter graph

`magick` is the tool for a still image; the output extension picks the format.

- Resize to a width, keeping aspect:
  `magick uploads/photo.heic -resize 1600x photo.jpg`. To a height: `-resize x1080`.
  Fit inside a box without upscaling: `-resize '1600x1600>'`.
- Convert format: `magick uploads/photo.heic photo.jpg` (HEIC/WEBP/TIFF in,
  JPG/PNG out).
- Control JPEG size with `-quality 85`.
- Crop a region: `magick in.jpg -crop 800x600+100+50 +repage out.jpg` (WxH+X+Y;
  `+repage` resets the virtual canvas so the crop is clean).
- Identify what an image is: `magick identify uploads/photo.heic`.
- Write to a new path; do not overwrite the original.
