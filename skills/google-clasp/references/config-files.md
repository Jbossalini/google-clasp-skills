# Configuration Files Reference

## `.clasp.json` — Project Settings

Created by `clasp create-script` or `clasp clone-script`. Lives in the project root.

```json
{
  "scriptId": "1a2b3c...",
  "rootDir": "build/",
  "projectId": "project-id-xxxxxxxxxxxxxxxxxxx",
  "scriptExtensions": [".js", ".gs"],
  "htmlExtensions": [".html"],
  "filePushOrder": ["globals.js", "main.js"],
  "skipSubdirectories": false
}
```

### Fields

| Field | Required | Default | Description |
|---|---|---|---|
| `scriptId` | **Yes** | — | Google Script project ID. Found in the URL: `script.google.com/d/<ID>/edit` |
| `rootDir` | No | `"."` | Local directory for project files. Clasp pushes/pulls from here. |
| `projectId` | No | — | GCP project ID. Required for `clasp tail-logs`, `clasp run-function`, API commands. |
| `scriptExtensions` | No | `[".js", ".gs"]` | File extensions treated as script files. First extension is used when pulling. |
| `htmlExtensions` | No | `[".html"]` | File extensions treated as HTML files. |
| `filePushOrder` | No | — | Array of files to push first (execution-order-dependent scripts). Paths relative to `.clasp.json` directory. |
| `skipSubdirectories` | No | `false` | Legacy compat: set `true` to ignore subdirs without `.claspignore`. |
| `fileExtension` | No | — | **Deprecated.** Use `scriptExtensions` instead. |

---

## `appsscript.json` — Apps Script Manifest

Every project has this file. Defines runtime config, scopes, deployment settings.

```json
{
  "timeZone": "America/Chicago",
  "runtimeVersion": "V8",
  "exceptionLogging": "STACKDRIVER",
  "dependencies": {
    "enabledAdvancedServices": [
      {
        "userSymbol": "Sheets",
        "version": "v4",
        "serviceId": "sheets"
      }
    ],
    "libraries": []
  },
  "oauthScopes": [
    "https://www.googleapis.com/auth/spreadsheets"
  ],
  "webapp": {
    "executeAs": "USER_DEPLOYING",
    "access": "ANYONE_ANONYMOUS"
  },
  "executionApi": {
    "access": "ANYONE"
  },
  "urlFetchWhitelist": [
    "https://api.example.com/"
  ]
}
```

### Fields

| Field | Description |
|---|---|
| `timeZone` | ZoneId string, e.g. `"America/New_York"`, `"Europe/London"` |
| `runtimeVersion` | `"V8"` (recommended), `"STABLE"`, or `"DEPRECATED_ES5"` |
| `exceptionLogging` | `"STACKDRIVER"` or `"NONE"` |
| `oauthScopes` | Explicit scope array. Overrides auto-detection. Omit for automatic. |
| `dependencies.enabledAdvancedServices` | Advanced Google services (Sheets API, Drive API, etc.) |
| `dependencies.libraries` | External Apps Script libraries |
| `webapp.executeAs` | `"USER_DEPLOYING"` or `"USER_ACCESSING"` |
| `webapp.access` | `"MYSELF"`, `"DOMAIN"`, `"ANYONE"`, `"ANYONE_ANONYMOUS"` |
| `executionApi.access` | Required for `clasp run-function`. Set to `"ANYONE"` or `"DOMAIN"`. |
| `urlFetchWhitelist` | HTTPS URL prefixes for `UrlFetchApp`. Required for production. |
| `addOns` | Google Workspace add-on configuration |
| `chat` | Google Chat app configuration |
| `sheets` | Sheets macro configuration |

---

## `.claspignore` — Push Exclusion Patterns

Uses [multimatch](https://github.com/sindresorhus/multimatch) syntax (not identical to `.gitignore`). Patterns are relative to `rootDir`.

### Default behavior (no `.claspignore` present)

```
**/**
!appsscript.json
!**/*.gs
!**/*.js
!**/*.ts
!**/*.html
.git/**
node_modules/**
```

### Example: bundler output only

```
**/**
!appsscript.json
!build/main.js
```

### Important

- To ignore a directory, use `**/dirname/**` (not just `dirname/`)
- Patterns are applied relative to `rootDir`

---

## `.clasprc.json` — OAuth Credentials

Stored at `~/.clasprc.json` (global) or in the project directory (local). Contains OAuth tokens.

**Never commit this file.** Add to `.gitignore`.

---

## Environment Variables

| Variable | Description |
|---|---|
| `clasp_config_project` | Override `.clasp.json` location (must start with `.`) |
| `clasp_config_ignore` | Override `.claspignore` location |
| `clasp_config_auth` | Override `.clasprc.json` location (must start with `.`) |

---

## Global CLI Options

| Flag | Description |
|---|---|
| `--user <n>` | Use named credential set (default: `default`) |
| `--adc` | Use Application Default Credentials (experimental) |
| `--project <file>` | Read settings from alternate file instead of `.clasp.json` |
| `--ignore <file>` | Read ignore patterns from alternate file |
| `--json` | JSON output for machine parsing |
