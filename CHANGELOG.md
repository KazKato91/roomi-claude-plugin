# Changelog

Each release of the Roomi plugin for Claude Code, newest first. The version is the one in `.claude-plugin/plugin.json`; Claude Code updates an installed copy only when it changes.

## 0.2.0

- `/roomi:deploy` knows the type of room. `roomi init` asks whether a demo is a standard room or an immersive one, and the skill relays that question rather than choosing: it sets `"room": "immersive"` only on your yes. If the workspace's plan has no immersive rooms, the rehearsal says so and the skill asks how you want to go on. Immersive rooms need `@getroomi/cli` 0.2.0 or later.

## 0.1.0

The first public release.

- `/roomi:deploy`: deploys the repository's demo with the `roomi` CLI. It writes the guided tour, validates and rehearses, and publishes only on your yes. Then it offers a viewer link, or on Free a Community listing.
- `/roomi:list-on-community`: drafts a project's Community card from its tour, rehearses it, and lists it only on your yes.
- A connection to Roomi at `https://mcp.getroomi.com/mcp`, signed in with OAuth, with the same tools as the connector for Claude.
