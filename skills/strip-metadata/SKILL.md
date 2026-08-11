---
name: strip-metadata
description: Read or remove a file's metadata — EXIF, GPS location, camera, author, timestamps — before sharing it. Use whenever the user asks to strip, remove, clean, or check metadata/EXIF/GPS on a photo, PDF, or document, or "where and when was this taken". Covers reading tags and stripping them in place or on a copy.
agents: media-ops
---

# Read, then strip, metadata with exiftool

- Read everything a file says about itself: `exiftool uploads/photo.jpg` (`-json`
  for machine-readable, `-s -s -s -GPSLatitude` for a single value). This answers
  when and where a photo was taken and who authored a document.
- Strip every tag in place before sharing: `exiftool -all= photo.jpg`. exiftool
  keeps a `photo.jpg_original` backup by default — add `-overwrite_original` to
  skip it.
- Strip onto a copy instead of touching the original:
  `exiftool -all= -o clean.jpg uploads/photo.jpg`.
- Metadata carries GPS and device serial numbers — say what you removed, so the
  user knows what was in there.
