---
name: read-web-page
description: Read one or more specific web pages as clean markdown — main content, no navigation or markup. Use whenever the user gives a URL to read or summarize, or after a search when you need a full page rather than the top-three preview. Covers saving pages to grep later and extracting a page's links. For searching the web, use web-search.
agents: researcher
---

# Fetch a page as markdown

`fetch https://sqlite.org/wal.html` returns the main content as markdown —
headings, lists, tables, code and links kept, navigation and markup discarded.

- Read several at once by listing URLs; each is separated by a header.
- Remove the length cap on a long page with `--max-chars 0`.
- Save pages worth keeping instead of holding them in context:
  `fetch --save sources https://…`, then `rg -n -i "rate limit" sources`.
- Get just the links — to find an API or a feed: `fetch --links https://…`.
- There is no browser and no JavaScript engine, so a page that renders
  client-side comes back empty. Look for an API endpoint, an RSS/JSON feed, or the
  same content on another site.
