# spanner-mcp-extension

Google Cloud Spanner MCP integration for **Claude Code** and **Gemini CLI**. Inspect schemas, run read-only SQL queries, and execute DML directly from your AI assistant.

## Features

- Inspect full DDL schemas (tables, indexes, proto bundles, functions)
- Run read-only SQL queries safely
- Execute DML (INSERT, UPDATE, DELETE) with confirmation before commit
- Automatic auth token refresh on session reconnect

---

## Prerequisites

### Install gcloud CLI

Download and install the [Google Cloud CLI](https://cloud.google.com/sdk/docs/install) for your platform:

**macOS (Homebrew):**
```bash
brew install --cask google-cloud-sdk
```

**Linux:**
```bash
curl https://sdk.cloud.google.com | bash
exec -l $SHELL
```

**Windows:** Download the installer from the [gcloud CLI install page](https://cloud.google.com/sdk/docs/install).

After installation, initialize:
```bash
gcloud init
```

### Authentication

**Option 1 — Application Default Credentials (recommended for local development):**
```bash
gcloud auth application-default login
```

**Option 2 — Service Account (recommended for CI/CD or production):**
```bash
gcloud auth activate-service-account --key-file=KEY_FILE_PATH
export GOOGLE_APPLICATION_CREDENTIALS=KEY_FILE_PATH
```

---

## Claude Code

### Installation

Installing is a two-step process: first add the marketplace, then install the plugin.

**Step 1 — Add the marketplace:**

```bash
/plugin marketplace add TanmayVartak/spanner-mcp-extension
```

**Step 2 — Install the plugin:**

```bash
/plugin install spanner-plugin@spanner-marketplace
```

After installing, run `/reload-plugins` to activate the plugin.

### MCP Server

The plugin connects to the Spanner MCP server at `https://spanner.googleapis.com/mcp` using your local `gcloud` credentials. Make sure you are authenticated:

```bash
gcloud auth application-default login
```

### Commands

Plugin commands are namespaced under `spanner-plugin:`:

| Command | Description |
|---|---|
| `/spanner-plugin:describe-schema` | Show the full DDL schema for a database |
| `/spanner-plugin:query` | Run a read-only SQL query |
| `/spanner-plugin:execute-dml` | Execute an INSERT, UPDATE, or DELETE statement |

### Agent

`@spanner-plugin:spanner-explorer` — an interactive agent for exploring instances, browsing schemas, and running queries conversationally.

### Auth Token Refresh & Retry Policy

The plugin fetches a fresh GCP auth token when the MCP connection is initialized. The token is cached in-memory for the lifetime of the connection (GCP access tokens expire after ~1 hour).

**Auto-reconnect:** Claude Code automatically detects when the transport drops and reinitializes the connection, including fetching a fresh token via the `headersHelper`. No manual action is required in most cases.

**Failure flow (e.g. after a long idle session):**
1. A tool call is made with a stale cached token → auth failure
2. Claude Code detects the dropped connection and triggers automatic reconnect
3. The reconnect fetches a fresh token (OAuth fallback + `headersHelper`) — takes ~33 seconds
4. The plugin's retry policy kicks in: waits 35 seconds, retries the original tool call
5. If the retry fails, waits 10 more seconds and tries one final time
6. On success the session continues normally; the transient error is not surfaced to the user

To manually force a token refresh at any time, run `/mcp reconnect`.

---

## Gemini CLI

### Installation

Install the extension via the Gemini CLI (requires v0.6.0 or above):

```bash
gemini extension install https://github.com/TanmayVartak/spanner-mcp-extension
```

Check your version with:

```bash
gemini --version
```

### MCP Server

The extension connects to the Spanner MCP server using a bearer token from `gcloud`. Authenticate using one of the options in the [Prerequisites](#prerequisites) section above.

---

## Safety

- `SELECT` queries always use a read-only execution path
- DML statements require explicit confirmation before committing
- Objects (tables, indexes, instances, databases) are never dropped unless you explicitly request it — `ALTER` is used instead of drop-and-recreate
