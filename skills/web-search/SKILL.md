---
name: web-search
description: Answer a question from the live web by searching and reading the top results. Use whenever the user asks to look something up, research a topic, find current information, check what is happening now, or answer a factual question you cannot answer from memory. Use read-web-page to open a specific URL, and read-attachment for a local file.
agents: researcher
---

# Search reads the top results for you

`search` prints the numbered results *and* the text of the top three pages, so one
call usually answers the question.

- Write the query as plain words, no quote characters — they become part of the
  query: `search best local model for tool calling`.
- **Answer from the page text, not the snippets.** One search is usually enough;
  if it did not answer, `fetch` one of the URLs it returned rather than searching
  again with different words.
- For anything about now: `search --recent day openai release` — a plain search
  matches front pages, not today's stories (`week`, `month`, `year` too).
- Narrow when needed: `--site docs.python.org`, `--region uk-en`, `-n 10` for a
  longer list, `--read 5` to read more pages (or `--read 0` for links only).
- Cite the URLs your answer rests on.
