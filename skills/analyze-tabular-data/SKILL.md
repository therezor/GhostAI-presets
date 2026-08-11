---
name: analyze-tabular-data
description: Summarize, filter, and reshape CSV/TSV/JSON data and answer questions about it with the command shown. Use whenever the user asks for totals, averages, counts, top-N, grouping, or filtering over a spreadsheet or data export, or "what does this CSV say". Use query-with-sql instead when the answer needs several joins or a group-by with having.
agents: data-analyst
---

# Miller (mlr) is the first tool for a data file

`mlr` reads CSV, TSV and JSON as records and converts between the three.

- **Look before you aggregate:** `mlr --c2p head -n 5 uploads/sales.csv` and a row
  count (`mlr --icsv --opprint count uploads/sales.csv`) catch a wrong delimiter,
  a header read as data, or a stray total row — before any becomes a confident
  wrong number.
- Summaries: `mlr --icsv --opprint stats1 -a sum,mean,count -f qty uploads/sales.csv`;
  add `-g region` to group.
- Filter and sort:
  `mlr --icsv --opprint filter '$qty > 3' then sort -nr qty uploads/sales.csv`.
- Convert: `mlr --icsv --ojson cat uploads/sales.csv`, or `--c2p` for a readable
  table.
- Show the command behind every number, and round in the prose, not the pipeline.
  Write long output to a file (`… > summary.csv`) and name the path rather than
  pasting a hundred rows.
