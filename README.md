# google-clasp-skills

Agent skills for building and deploying Google Apps Script projects using [Google Clasp](https://github.com/google/clasp).

## Install

```bash
npx skills add jbossalini/google-clasp-skills
```

## Skills

| Skill | Description |
|---|---|
| **google-clasp** | Comprehensive skill for creating, managing, and deploying Google Apps Script projects using the `clasp` CLI. Covers project setup, push/pull, versions, deployments, manifest configuration, and the MCP server mode. |

## What is Clasp?

Clasp (**C**ommand **L**ine **A**pps **S**cript **P**rojects) lets you develop Google Apps Script projects locally from the terminal. Push/pull code, manage deployments, run functions remotely, and integrate with AI agents via its experimental MCP server.

## Requirements

- Node.js >= 18.0.0 (LTS recommended)
- `npm install -g @google/clasp`
- Apps Script API enabled: https://script.google.com/home/usersettings
- Run `clasp login` before use

## License

MIT
