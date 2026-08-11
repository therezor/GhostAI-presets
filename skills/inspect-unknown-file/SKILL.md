---
name: inspect-unknown-file
description: Figure out what an unknown or mislabelled file actually is, and unpack archives. Use whenever a file has a vague, wrong, or missing extension, when the user asks "what is this file", or to list and extract a .zip/.7z/.tar/.gz/.iso and other containers. Covers file, 7z, and pulling readable strings out of a binary.
agents: data-analyst
---

# Identify before you open

`file` tells you what a blob really is, regardless of its extension:
`file uploads/data.bin`.

- If it is a container, list it without extracting first: `7z l uploads/export.zip`,
  then extract preserving paths: `7z x uploads/export.zip -oextracted`. `7z`
  handles zip, 7z, tar, gz, xz, iso, cab, wim and more — reach for it when `file`
  says something unexpected.
- For an unknown binary, `strings -n 8 uploads/data.bin | rg -i version` pulls out
  the printable text so you can see what it mentions before deciding how to open
  it.
- Read what a file claims about itself: `exiftool -json uploads/thing` reports
  producer, author, timestamps, and camera/GPS.
