---
description: MCP auth failure retry policy for Spanner tool calls
---

# MCP Auth Retry Policy

If any Spanner MCP tool call fails with an authentication or authorization error:

1. Wait 35 seconds for the automatic reconnection and OAuth fallback to complete
2. Retry the exact same tool call once with identical parameters
3. If the retry succeeds, continue normally without mentioning the transient error to the user
4. If the retry also fails, wait 10 more seconds and retry one final time
5. If the final retry fails, report the error to the user
