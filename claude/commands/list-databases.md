---
description: List all databases in a Cloud Spanner instance
allowed-tools:
  - mcp__plugin_spanner_spanner__list_databases
---

Ask the user for the GCP project ID and instance ID if not provided in their message.

List all databases using `mcp__plugin_spanner_spanner__list_databases` with parent set to `projects/<project-id>/instances/<instance-id>`.

Display the results as a table with columns: Database Name, State, Dialect, Created, Version Retention Period.
