---
name: google-clasp
description: Build, manage, and deploy Google Apps Script projects using the clasp CLI (v3.x). Use when the user needs to create, push, pull, version, or deploy Apps Script code from the command line or via an AI agent. Covers project setup, appsscript.json manifests, .clasp.json configuration, .claspignore patterns, authentication, MCP server mode, bundler pipelines, and Apps Script language constraints. Also use when working with container-bound scripts (Sheets, Docs, Slides, Forms), web apps, API executables, or any Google Workspace automation built on Apps Script.
---

# Google Clasp — Apps Script CLI

Clasp (**C**ommand **L**ine **A**pps **S**cript **P**rojects) lets you develop Google Apps Script projects locally. You can push/pull code, manage deployments and versions, and run functions remotely — all from the terminal.

> **Important**: Clasp v3.x requires **Node.js >= 22.0.0** and `clasp login` before any commands. The Apps Script API must be enabled at https://script.google.com/home/usersettings.

## Project Structure

A clasp project has these key files. See [references/config-files.md](references/config-files.md) for full field documentation.

```
my-project/
├── .clasp.json          # Project settings (scriptId, rootDir, etc.)
├── .claspignore         # Files to exclude from push
├── .clasprc.json        # OAuth credentials (NEVER commit)
├── appsscript.json      # Apps Script manifest (runtime, scopes, etc.)
├── Code.js              # Your script files (.js, .gs)
└── Page.html            # HTML files for HtmlService
```

### Minimal `.clasp.json`

```json
{
  "scriptId": "YOUR_SCRIPT_ID",
  "rootDir": "./"
}
```

### Minimal `appsscript.json`

```json
{
  "timeZone": "America/Chicago",
  "runtimeVersion": "V8",
  "exceptionLogging": "STACKDRIVER"
}
```

## Core Workflow

### Create a project

```bash
clasp create-script --title "My Script" --type standalone
clasp create-script --type sheets --title "Sheet Helper"
clasp create-script --title "Bound Script" --parentId "SPREADSHEET_ID"
```

### Clone an existing project

```bash
clasp clone-script "SCRIPT_ID"
clasp clone-script "https://script.google.com/d/SCRIPT_ID/edit" --rootDir ./src
```

### Push and pull code

```bash
clasp push                # Replace entire remote project with local files
clasp push --force        # Also overwrite remote manifest
clasp push --watch        # Auto-push on local changes
clasp pull                # Download remote → local
clasp show-file-status    # Preview what will be pushed
```

**Critical**: `clasp push` is a **full project replacement**, not incremental. Always treat local as the single source of truth.

### Version and deploy

```bash
clasp create-version "v1.0 - initial release"
clasp create-deployment --description "Production"
clasp create-deployment --versionNumber 4
clasp update-deployment DEPLOYMENT_ID
clasp list-deployments
clasp delete-deployment DEPLOYMENT_ID
```

### Run functions remotely

Requires custom OAuth credentials and API executable deployment. See [references/run-setup.md](references/run-setup.md).

```bash
clasp run-function 'sendEmail'
clasp run-function 'processData' --params '["arg1", 42, true]'
```

### View logs

```bash
clasp tail-logs --watch     # Real-time StackDriver logs
clasp tail-logs --json
clasp setup-logs            # Configure GCP project for logging
```

### Manage APIs

```bash
clasp list-apis
clasp enable-api sheets
clasp disable-api drive
```

## Authentication

```bash
clasp login                                    # Default credentials
clasp login --user staging                     # Named account
clasp push --user staging                      # Use named creds
clasp login --user myuser --creds creds.json   # Custom OAuth client
clasp show-authorized-user                     # Check auth status
clasp logout
```

For custom OAuth credentials (recommended for orgs), see [references/auth-setup.md](references/auth-setup.md).

## MCP Server Mode (Experimental)

Clasp can run as an MCP server for AI agent integration:

```bash
clasp mcp    # Start MCP server via STDIO
```

**Install for Claude Code:**
```bash
/plugin install @google/clasp
# or manually:
claude mcp add clasp -- npx -y @google/clasp mcp
```

**Generic MCP config:**
```json
{
  "mcpServers": {
    "clasp": {
      "command": "clasp",
      "args": ["mcp"]
    }
  }
}
```

MCP mode does not need to start from the project directory. Switching projects does not require restart; switching credentials does.

## Apps Script Constraints

When writing code for Apps Script, observe these rules:

- **No ES modules** — No `import`/`export`. All files share a single global scope. Use a bundler if you need modules.
- **No npm at runtime** — Dependencies must be bundled at build time or loaded via Apps Script libraries.
- **File types** — Only `.js`/`.gs` (scripts) and `.html` (HtmlService templates) are recognized.
- **Execution order** — Files load alphabetically by default. Use `filePushOrder` in `.clasp.json` to control order.
- **Global scope is shared** — All top-level declarations across all files share one namespace.
- **`console.log` for logs** — Use `console.log()` (not `Logger.log()`) for StackDriver-visible logs via `clasp tail-logs`.
- **Reserved functions** — `onOpen`, `onEdit`, `onInstall`, `doGet`, `doPost` have special trigger behavior.
- **6-minute limit** — Most scripts. Web app triggers have 30-second limits.

## Using a Bundler (TypeScript / ESM)

Clasp v3.x dropped built-in TypeScript transpilation. Use Rollup, Webpack, or esbuild:

```
project/
├── .clasp.json          # rootDir: "build/"
├── build/               # clasp pushes from here
│   ├── appsscript.json
│   └── main.js
├── src/
│   └── index.ts
├── rollup.config.js
└── package.json
```

Workflow: `npm run build` → `clasp push`

## Common Patterns

### Web app

Add to `appsscript.json`:
```json
{
  "webapp": {
    "executeAs": "USER_DEPLOYING",
    "access": "ANYONE_ANONYMOUS"
  }
}
```

Implement `doGet(e)` and/or `doPost(e)` in your script.

### Container-bound script (Sheets)

```bash
clasp create-script --title "Sheet Helper" --parentId "SPREADSHEET_ID"
```

### Add OAuth scopes explicitly

In `appsscript.json`:
```json
{
  "oauthScopes": [
    "https://www.googleapis.com/auth/spreadsheets",
    "https://www.googleapis.com/auth/script.external_request"
  ]
}
```

### `.claspignore` for bundler projects

```
**/**
!appsscript.json
!main.js
```

## Debugging

```bash
DEBUG=clasp:* clasp push    # Verbose debug output
node -v                     # Must be >= 22.0.0
```

Common issues and solutions are documented in [references/troubleshooting.md](references/troubleshooting.md).

## Command Quick Reference

| Task | Command |
|---|---|
| Login | `clasp login` |
| Create project | `clasp create-script --title "Name"` |
| Clone project | `clasp clone-script "SCRIPT_ID"` |
| Push code | `clasp push` |
| Pull code | `clasp pull` |
| Watch & push | `clasp push --watch` |
| Create version | `clasp create-version "desc"` |
| Deploy | `clasp create-deployment --description "desc"` |
| List deployments | `clasp list-deployments` |
| Remove deployment | `clasp delete-deployment ID` |
| Run function | `clasp run-function 'funcName'` |
| View logs | `clasp tail-logs --watch` |
| Start MCP server | `clasp mcp` |
