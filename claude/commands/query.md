---
description: Run a read-only SQL query against a Cloud Spanner database
allowed-tools:
  - mcp__plugin_spanner-plugin_spanner__create_session
  - mcp__plugin_spanner-plugin_spanner__execute_sql_readonly
---

Ask the user for:
1. GCP project ID, instance ID, and database name (if not already specified)
2. The SQL query to run

Steps:
1. Create a session using `mcp__plugin_spanner-plugin_spanner__create_session` with database `projects/<project-id>/instances/<instance-id>/databases/<database>`
2. Analyse the user's query. If it does not filter on a primary key or an effective index (i.e. it would likely result in a full table scan), first check the size of the specific table being queried by running:
   ```sql
   SELECT table_name, used_bytes
   FROM SPANNER_SYS.TABLE_SIZES_STATS_1HOUR
   WHERE interval_end = (SELECT MAX(interval_end) FROM SPANNER_SYS.TABLE_SIZES_STATS_1HOUR)
     AND table_name = @table_name
   ```
   If the table is large (>100 MB), warn the user about the potential for a costly full scan and recommend adding filters or a LIMIT clause. Do not proceed without acknowledgement.
3. Execute the query using `mcp__plugin_spanner-plugin_spanner__execute_sql_readonly` with the session name and the provided SQL

Always use parameterized queries. Extract all literal values (strings, numbers, timestamps) from the SQL into parameters and pass them via the `parameters` field with explicit types. Never interpolate user-provided values directly into the SQL string. Use the correct parameter syntax for the database dialect:
- Google Standard SQL: named parameters (`@param_name`)
- PostgreSQL: positional parameters (`$1`, `$2`, ...)

Advise the user against selecting more than 50 rows at a time. If the query has no LIMIT clause, suggest adding `LIMIT 50` and ask for confirmation before running without one.

Display results as a formatted table. If the result set is large (>50 rows), summarize and show the first 20 rows.

Apply the retry policy from `/spanner-plugin:mcp-retry-policy` for any authentication or authorization failures.
