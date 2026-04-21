# spanner-mcp-extension

Google Cloud Spanner MCP integration for **Claude Code** and **Gemini CLI**. Query databases, inspect schemas, execute DML, and manage Spanner instances directly from your AI assistant.

## Features

- List Spanner instances and databases
- Inspect full DDL schemas (tables, indexes, proto bundles, functions)
- Run read-only SQL queries safely
- Execute DML (INSERT, UPDATE, DELETE) with confirmation before commit

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
| `/spanner-plugin:list-instances` | List all Spanner instances in a GCP project |
| `/spanner-plugin:list-databases` | List all databases in an instance |
| `/spanner-plugin:describe-schema` | Show the full DDL schema for a database |
| `/spanner-plugin:query` | Run a read-only SQL query |
| `/spanner-plugin:execute-dml` | Execute an INSERT, UPDATE, or DELETE statement |

### Agent

`@spanner-plugin:spanner-explorer` — an interactive agent for exploring instances, browsing schemas, and running queries conversationally.

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
