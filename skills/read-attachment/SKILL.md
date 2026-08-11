---
name: read-attachment
description: Read a file the user attached as text — PDF, Word, Excel, PowerPoint, an image, or a scan. Use whenever the user refers to a file, document, or PDF they attached and asks what it says or to summarize or extract from it. Unlike the data-analyst agent, this OCRs scanned PDFs and photos of text automatically, including non-English.
agents: researcher
---

# Open an attachment with doc

A file someone attaches arrives as a path (under `uploads/`) and is the one thing
you cannot see without opening. `doc uploads/report.pdf` reads a PDF, an Office
file, an image, or plain text.

- A scanned PDF or a photo of text has no text layer and is OCRed automatically —
  this is what sets `doc` apart from a plain converter.
- Non-English scan: name the language, e.g. `doc --lang deu uploads/scan.pdf`
  (also fra, spa, ita, por, nld, rus, ukr, swe, nor, dan, fin; join several with
  `+`, like `deu+eng`).
- Remove the length cap with `--max-chars 0`, or save the extracted text with
  `--save notes/`.
- Quote wording with its page or section, and say plainly if a page could not be
  read.
