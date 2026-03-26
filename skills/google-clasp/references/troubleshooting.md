# Troubleshooting

## Debug Logging

```bash
DEBUG=clasp:* clasp push    # Enable verbose debug output
```

Include debug output in any bug reports.

## Common Errors

### "Script API not enabled"

Enable the Apps Script API at: https://script.google.com/home/usersettings

### "Push replaces everything"

`clasp push` replaces the **entire** remote project — it is not incremental and not like `git push`. Always use local as the single source of truth. If you edited in the online editor, `clasp pull` first, then apply your changes locally.

### Files not showing up after push

- Check `.claspignore` patterns — ensure your files aren't excluded
- Verify file extensions match `scriptExtensions` / `htmlExtensions` in `.clasp.json`
- Files starting with `.` are always ignored
- Run `clasp show-file-status` to see what will be pushed

### `clasp run-function` fails

- Must use custom OAuth credentials: `clasp login --creds creds.json`
- Script must be deployed as API executable
- `executionApi.access` must be set in `appsscript.json`
- Required APIs must be enabled on your GCP project
- Login must include project scopes: `--use-project-scopes`

See [run-setup.md](run-setup.md) for full setup guide.

### Scope / permission errors

- Add explicit `oauthScopes` to `appsscript.json`
- Re-login with `--use-project-scopes` if using `clasp run-function`
- Adding scopes to manifest overrides auto-detection — ensure ALL needed scopes are listed

### Node.js version error

Clasp v3.x requires Node.js >= 18.0.0 (LTS recommended):

```bash
node -v                    # Check version
npm install -g npm         # Update npm
npx n latest               # Upgrade Node (not on Windows)
```

### Proxy issues

Clasp supports proxies via the Google APIs Node module:

```bash
export HTTP_PROXY=http://proxy:8080
export HTTPS_PROXY=http://proxy:8080
```

## v2.x → v3.x Migration

### Command renames

| v2.x | v3.x |
|---|---|
| `clasp open` | `clasp open-script` |
| `clasp open --web` | `clasp open-web-app` |
| `clasp open --addon` | `clasp open-container` |
| `clasp open --creds` | `clasp open-credentials-setup` |
| `clasp login --creds <file>` | `clasp login -u <n> --creds <file>` |
| `clasp logs --open` | `clasp open-logs` |
| `clasp apis enable <api>` | `clasp enable-api <api>` |
| `clasp apis disable <api>` | `clasp disable-api <api>` |
| `clasp deploy -i <id>` | `clasp update-deployment <id>` |
| `clasp create` | `clasp create-script` |
| `clasp clone` | `clasp clone-script` |

### TypeScript support removed

Clasp v3.x no longer transpiles TypeScript. Use a bundler like Rollup, Webpack, or esbuild. See community templates:

- https://github.com/WildH0g/apps-script-engine-template
- https://github.com/tomoyanakano/clasp-typescript-template
- https://github.com/google/aside
- https://github.com/sqrrrl/apps-script-typescript-rollup-starter
