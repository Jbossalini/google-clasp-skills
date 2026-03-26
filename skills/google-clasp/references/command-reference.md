# Complete Command Reference (Clasp v3.x)

## Authentication

```bash
clasp login [options]
  --no-localhost              Manual code entry (headless)
  --creds <file>              Custom OAuth client credentials
  --user <n>                  Save/use named credential set
  --use-project-scopes        Use scopes from appsscript.json
  --include-clasp-scopes      Add default clasp scopes (with --use-project-scopes)
  --extra-scopes <scopes>     Comma-separated additional scopes
  --redirect-port <port>      Custom local redirect port

clasp logout

clasp show-authorized-user [--json]
```

## Project Management

```bash
clasp create-script [options]
  --title <title>             Project title
  --type <type>               standalone|docs|sheets|slides|forms|webapp|api
  --rootDir <dir>             Local directory for files
  --parentId <id>             Bind to existing Google document

clasp clone-script <scriptId|URL> [versionNumber] [--rootDir <dir>]

clasp delete-script [--force]
```

## Code Sync

```bash
clasp push [options]
  --force                     Overwrite remote manifest
  --watch                     Auto-push on local changes

clasp pull [options]
  --versionNumber <n>         Pull specific version
  --deleteUnusedFiles         Remove local files not on server
  --force                     Auto-confirm with --deleteUnusedFiles

clasp show-file-status [--json]
```

## Versions

```bash
clasp create-version [description]
clasp list-versions [scriptId]
```

## Deployments

```bash
clasp create-deployment [options]
  --versionNumber <n>         Deploy specific version
  --description <desc>        Deployment description
  --deploymentId <id>         Redeploy existing deployment

clasp update-deployment <deploymentId> [options]
  --versionNumber <n>
  --description <desc>

clasp list-deployments [scriptId]

clasp delete-deployment [deploymentId]
  --all                       Remove all deployments
```

## Remote Execution

```bash
clasp run-function [functionName] [options]
  --nondev                    Run in production mode
  --params <json-array>       JSON array of parameters
  --user <n>                  Run as specific user
```

## Logs & APIs

```bash
clasp tail-logs [options]
  --json                      JSON output
  --watch                     Real-time tail (5s interval)
  --simplified                Remove timestamps

clasp setup-logs [--json]

clasp list-apis
clasp enable-api <api>
clasp disable-api <api>
```

## Navigation

```bash
clasp open-script
clasp open-web-app
clasp open-container
clasp open-credentials-setup
clasp open-api-console
clasp open-logs
```

## Other

```bash
clasp list-scripts
clasp mcp                    # Start MCP server (experimental)
clasp help
```

## Global Options (available on all commands)

```
--user <n>          Named credential set
--adc               Application Default Credentials
--project <file>    Alternate .clasp.json path
--ignore <file>     Alternate .claspignore path
--json              Machine-readable output
```
