---
description: Show the full DDL schema for a Cloud Spanner database
allowed-tools:
  - mcp__plugin_spanner_spanner__get_database_ddl
---

Ask the user for the GCP project ID, instance ID, and database name if not provided in their message.

Fetch the schema using `mcp__plugin_spanner_spanner__get_database_ddl` with database set to `projects/<project-id>/instances/<instance-id>/databases/<database>`.

Present a structured summary:
- List each table with its columns, types, and primary key
- List any secondary indexes
- List any custom schemas, proto bundles, or user-defined functions
- Show row deletion policies if present
