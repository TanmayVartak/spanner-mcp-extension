---
description: List all Cloud Spanner instances in a GCP project
allowed-tools:
  - mcp__plugin_spanner-plugin_spanner__list_instances
---

Ask the user for the GCP project ID if not provided in their message.

List all Spanner instances in the project using `mcp__plugin_spanner-plugin_spanner__list_instances` with parent set to `projects/<project-id>`.

Display the results as a table with columns: Instance Name, Display Name, State, Node Count / Processing Units, Location.
