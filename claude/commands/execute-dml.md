---
description: Execute a DML statement (INSERT, UPDATE, DELETE) against a Cloud Spanner database
allowed-tools:
  - mcp__plugin_spanner-plugin_spanner__create_session
  - mcp__plugin_spanner-plugin_spanner__execute_sql
  - mcp__plugin_spanner-plugin_spanner__commit
---

Ask the user for:
1. GCP project ID, instance ID, and database name (if not already specified)
2. The DML statement to execute

Warn the user that this will modify data and ask for confirmation before proceeding.

Steps:
1. Create a session using `mcp__plugin_spanner-plugin_spanner__create_session` with database `projects/<project-id>/instances/<instance-id>/databases/<database>`
2. Begin a transaction and execute the DML using `mcp__plugin_spanner-plugin_spanner__execute_sql`
3. Commit the transaction using `mcp__plugin_spanner-plugin_spanner__commit`

Always use parameterized queries. Extract all literal values (strings, numbers, timestamps) from the DML into parameters and pass them via the `parameters` field with explicit types. Never interpolate user-provided values directly into the SQL string. Use the correct parameter syntax for the database dialect:
- Google Standard SQL: named parameters (`@param_name`)
- PostgreSQL: positional parameters (`$1`, `$2`, ...)

Report the number of rows affected.
