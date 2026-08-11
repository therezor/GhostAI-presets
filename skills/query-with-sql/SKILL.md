---
name: query-with-sql
description: Answer data questions that need real SQL — several joins, group-by with having, window functions — over a CSV or a SQLite database. Use whenever the user attaches a .db or .sqlite file, or asks something over CSVs that is awkward in a single mlr pipeline. Use analyze-tabular-data for simple sums, filters, and counts.
agents: data-analyst
---

# Query with sqlite3

A `.db` or `.sqlite` file is SQLite — see what is there first, then query.

- Inspect: `sqlite3 uploads/app.db .tables`, then
  `sqlite3 uploads/app.db '.schema orders'`.
- Query a database, CSV out: `sqlite3 -csv uploads/app.db 'select region,
  sum(qty) from orders group by region'`.
- Query CSVs by importing first:
  `bash -lc "sqlite3 report.db '.mode csv' '.import uploads/sales.csv sales'
  '.import uploads/regions.csv regions'"`, then run the join against `report.db`.
  `.import` builds each table from the header row.
- Use `-csv` (or `-json`) and redirect large results to a file; name the path.
- Show the exact SQL behind every number.
