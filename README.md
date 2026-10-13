# Roomi for Claude Code

Say "deploy it to roomi" in the repository where you built a demo, and Claude Code puts it live in a private room with a guided tour. It rehearses first and publishes only on your yes. Then it makes a link for each viewer, and tells you who opened it, how far through the tour they got and what they said.

[Roomi](https://getroomi.com) is for sending the real thing instead of a deck. This plugin is the way in from Claude Code: it brings two skills and a connection to Roomi with the same tools as the [connector for Claude](https://getroomi.com/docs/claude).

## Install

In Claude Code, add the marketplace, then install the plugin from it:

```text
/plugin marketplace add KazKato91/roomi-claude-plugin
/plugin install roomi@roomi
```

On Claude Code 2.1.275 or later, one command does both:

```text
/plugin install roomi --marketplace KazKato91/roomi-claude-plugin
```

From your shell instead:

```sh
claude plugin marketplace add KazKato91/roomi-claude-plugin
claude plugin install roomi@roomi
```

The first time Claude Code connects to Roomi, it opens your browser to sign in; `/mcp` shows the connection. Sign in with GitHub or Google. There is no separate sign-up, and every workspace starts on Free.

Deploying from a repository also needs the `roomi` command line tool, with Node 24 or later. If it is missing, the skill offers to install it for you with `npm install -g @getroomi/cli`, and runs that only on your yes. [Install the CLI](https://getroomi.com/docs/install) has the details.

To get a new version, run `claude plugin update roomi@roomi` in your shell, or turn on auto-update for the marketplace on the **Marketplaces** tab of `/plugin`.

## What to say

In the demo's repository:

```text
deploy it to roomi
put this on roomi
write a tour for this demo
list it on the community
```

## What it does

- **`/roomi:deploy`** finds the project, writes `pitch.json` with `roomi init` if there is none, drafts the guided tour from the demo's own screens and routes, validates it, and rehearses the deploy on your machine. It shows you the rehearsal's own output, then asks one question: publish? After a yes, it offers a viewer link, or on Free a Community listing.
- **`/roomi:list-on-community`** drafts the project's Community card from its tour, rehearses it with Roomi's checks, and lists it only on your yes.
- **The connection** gives Claude Roomi's tools: make and revoke viewer links, read who opened a room and what they did, read feedback, add pages, and the Community. [MCP tools](https://getroomi.com/docs/mcp) lists every one.

## What it never does without your yes

- Publish a build. A yes counts only for the rehearsal it answers; if anything changes, it rehearses and asks again.
- Change your code. Anything beyond the files `roomi init` writes (`pitch.json`, `pitch.README.md`, `PITCH.md`, and one line in `AGENTS.md` and `CLAUDE.md`) is shown as a diff first.
- Install the CLI, make or revoke a link, add a page, or save, list or withdraw a Community card.
- Sign in for you. `roomi login` and the connection's sign-in both happen in your browser.
- Repeat a link's address after the reply that hands it to you.

## What it runs and what it sends

The plugin runs no code of its own: no hooks, no scripts, no programs. It is two skills, which are instructions Claude reads, and one connection.

- **The connection** goes to `https://mcp.getroomi.com/mcp` and nowhere else. It signs in with OAuth, in your browser. The plugin sends no token from your machine to it.
- **The skills** run the `roomi` CLI in your terminal, through Claude Code, with the permissions you give it. The CLI builds and runs the demo on your machine and scans it for secrets. It talks to Roomi's API, `https://api.getroomi.com`, and the build itself is uploaded only by `roomi deploy --commit`, which the skill runs only after your yes. The CLI signs in with the token `roomi login` keeps on your machine: in the Keychain on a Mac, elsewhere in a file only you can read.

## Privacy

Demos run on mock data. What Roomi holds is real from the first day: your account, your projects, and what each viewer did in a room and said back to you. [Security and privacy](https://getroomi.com/docs/security) explains how builds are isolated and what the platform keeps, and the [privacy policy](https://getroomi.com/privacy) is the full account.

## Docs

- [Deploying from Claude](https://getroomi.com/docs/claude): the plugin and the connector, side by side
- [Writing the tour](https://getroomi.com/docs/story): the story the room tells a viewer
- [pitch.json](https://getroomi.com/docs/pitch-json): every field of the manifest
- [Listing on the Community](https://getroomi.com/docs/listing)
- [Troubleshooting](https://getroomi.com/docs/troubleshooting)

## Licence

The plugin's files are under the MIT licence: see [LICENSE](LICENSE). The Roomi name and mark are not licensed with them. Using Roomi itself is under its [terms](https://getroomi.com/terms).
