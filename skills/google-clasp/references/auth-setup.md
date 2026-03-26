# Authentication & Credentials Setup

## Standard Login

```bash
clasp login                          # Opens browser, saves to ~/.clasprc.json
clasp login --no-localhost           # Manual code entry (headless environments)
clasp login --redirect-port 37473   # Custom redirect port
clasp logout
clasp show-authorized-user --json
```

## Multiple Users

```bash
clasp login                          # Saves as "default" credentials
clasp login --user staging           # Named "staging" credentials
clasp push --user staging            # Use staging creds for push
clasp run-function --user staging myFunc
```

## Custom OAuth Credentials (Recommended for Orgs)

For enhanced security or when your organization restricts third-party apps:

### Step 1: Create a GCP Project

1. Go to https://console.cloud.google.com/
2. Create a new project

### Step 2: Create OAuth Client

1. Go to APIs & Services → Credentials
2. Create an OAuth 2.0 Client ID
3. Type: **Desktop Application**
4. Download the `client_secret.json` file

### Step 3: Enable Required APIs

| API | Service ID | Required For |
|---|---|---|
| Apps Script API | `script.googleapis.com` | All operations |
| Service Usage API | `serviceusage.googleapis.com` | `list-apis`, `enable-api`, `disable-api` |
| Google Drive API | `drive.googleapis.com` | `list-scripts`, container-bound scripts |
| Cloud Logging API | `logging.googleapis.com` | `tail-logs` |

### Step 4: Login with Custom Creds

```bash
clasp login --user myuser --creds client_secret.json
```

### Required OAuth Scopes (for external projects)

```
https://www.googleapis.com/auth/script.deployments
https://www.googleapis.com/auth/script.projects
https://www.googleapis.com/auth/script.webapp.deploy
https://www.googleapis.com/auth/drive.metadata.readonly
https://www.googleapis.com/auth/drive.file
https://www.googleapis.com/auth/service.management
https://www.googleapis.com/auth/logging.read
https://www.googleapis.com/auth/userinfo.email
https://www.googleapis.com/auth/userinfo.profile
https://www.googleapis.com/auth/cloud-platform
```

## Login with Project Scopes

For `clasp run-function`, login with the scopes defined in your `appsscript.json`:

```bash
clasp login --user myuser --creds creds.json --use-project-scopes
clasp login --user myuser --creds creds.json --use-project-scopes --include-clasp-scopes
```

## Extra Scopes

```bash
clasp login --extra-scopes https://www.googleapis.com/auth/spreadsheets.readonly,https://www.googleapis.com/auth/drive.readonly
```

## Allow-listing Clasp in Your Organization

If your org restricts third-party apps, either:

- **Ask admin to allow-list** clasp's client ID: `1072944905499-vm2v2i5dvn0a0d2o4ca36i1vge8cvbn0.apps.googleusercontent.com`
- **Create an internal GCP project** using the steps above

## Service Accounts (Experimental — Not Working)

Use `--adc` flag with Application Default Credentials:

```bash
clasp push --adc
```

**Limitations:**
- Service accounts cannot own scripts
- Scripts must be shared with the service account as **Editor**
- Marked as experimental/not working in clasp docs
