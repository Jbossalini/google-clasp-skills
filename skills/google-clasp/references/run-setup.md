# Remote Function Execution Setup

`clasp run-function` lets you execute Apps Script functions from the command line. This requires additional setup beyond basic `clasp login`.

## Prerequisites

1. **Custom OAuth credentials** — You must use your own GCP project credentials (not the default clasp client).
2. **API executable deployment** — The script must be deployed as an API executable.
3. **Correct scopes** — Your login must include the scopes used by the script.
4. **GCP project association** — The script must be linked to a GCP project.

## Step-by-Step Setup

### 1. Create custom credentials

Follow the steps in [auth-setup.md](auth-setup.md) to create a GCP project with a Desktop OAuth client.

### 2. Login with project scopes

```bash
clasp login --user myuser --creds client_secret.json --use-project-scopes
```

### 3. Deploy as API executable

In the Apps Script editor (script.google.com):

1. Click **Deploy** → **New deployment**
2. Select type: **API Executable**
3. Set access to appropriate level
4. Click **Deploy**

### 4. Configure the manifest

Ensure `appsscript.json` has:

```json
{
  "executionApi": {
    "access": "ANYONE"
  }
}
```

Push this change: `clasp push`

### 5. Set the GCP project

Either add `projectId` to `.clasp.json`:

```json
{
  "scriptId": "...",
  "projectId": "project-id-xxxxxxxxxxxxxxxxxxx"
}
```

Or let clasp prompt you when needed.

## Running Functions

```bash
clasp run-function 'sendEmail'
clasp run-function 'addOptions' --params '["string", 123, {"test": "for"}, true]'
clasp run-function --user testaccount myFunction
clasp run-function 'myFunc' --nondev
```

## Common Issues

| Problem | Solution |
|---|---|
| "The caller does not have permission" | Re-login with `--use-project-scopes --creds creds.json` |
| "Script is not deployed as API executable" | Deploy via Apps Script editor as API Executable |
| "Access not granted" | Set `executionApi.access` to `"ANYONE"` in manifest |
| "API not enabled" | Enable Apps Script API on your GCP project |
| Scope errors | Add explicit `oauthScopes` to `appsscript.json` and re-login with `--use-project-scopes` |
