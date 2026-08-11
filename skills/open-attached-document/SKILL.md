---
name: open-attached-document
description: Turn an attached document into text you can read and search — Word, PowerPoint, Excel, OpenDocument, RTF, EPUB, PDF. Use whenever the user asks what a document says, to summarize or extract from a .docx/.pptx/.xlsx/.pdf they attached, or "read the file I sent". This has no OCR — for a scanned or photographed PDF with no text layer, use the researcher agent's read-attachment instead.
agents: data-analyst
---

# Convert a document once, then read the saved copy

An attachment is a path (usually under `uploads/`), not its contents — you must
open it before you can answer.

- Convert with `anydoc`, which emits markdown that keeps headings, lists and
  tables: `anydoc uploads/report.docx`. It reads Word, PowerPoint, Excel,
  OpenDocument, RTF, EPUB, CSV and PDF.
- **Save it once and reuse it** rather than converting again for every question:
  `bash -lc 'anydoc uploads/report.docx > report.md'`, then read or `rg` the saved
  `report.md`.
- Quote the wording behind every claim and name its section — a paraphrase cannot
  be checked.
- No OCR here: a scanned PDF or a photo of a page has no text layer and `anydoc`
  returns nothing. Say so, and hand it to the researcher's `doc` (read-attachment),
  which OCRs.
