# Changelog

Each release of the Roomi plugin for Claude Code, newest first. The version is the one in `.claude-plugin/plugin.json`; Claude Code updates an installed copy only when it changes.

## 0.1.0

The first public release.

- `/roomi:deploy`: deploys the repository's demo with the `roomi` CLI. It writes the guided tour, validates and rehearses, and publishes only on your yes. Then it offers a viewer link, or on Free a Community listing.
- `/roomi:list-on-community`: drafts a project's Community card from its tour, rehearses it, and lists it only on your yes.
- A connection to Roomi at `https://mcp.getroomi.com/mcp`, signed in with OAuth, with the same tools as the connector for Claude.
