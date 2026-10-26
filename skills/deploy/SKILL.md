---
name: deploy
description: Deploys the project in this repository to Roomi with the roomi CLI. It writes the guided story the room tours a viewer through, rehearses (the story's steps included), shows what would go live, and publishes only when the user says yes. Then it offers a viewer link, or on Free a Community listing. Use when the user says "deploy it to roomi", "deploy it to pitch-product", "put this on roomi", "ship this demo to roomi", asks for a pitch room or viewer link for the demo they built here, or asks for a tour or story for that demo.
argument-hint: "[path to the project, if not the current one]"
---

# Deploy to Roomi

Turn "deploy it to roomi" into a rehearsed deploy that publishes only on
the user's yes. The `roomi` CLI does the work: it builds and runs the demo on this
machine, which a remote server cannot do. Your job is to drive it, show what it
says, and stop at each decision.

## Rules that do not bend

- **Never run `roomi deploy --commit` without an explicit yes** to the rehearsal
  you have just shown, in this conversation. "Deploy it" at the start is the
  request to rehearse. It is not a yes to publish. A yes to an earlier
  rehearsal does not carry over after anything changes. If you are unsure
  whether they said yes, ask.
- **Never edit the user's code or config on your own.** `roomi init` writes
  `pitch.json`, `pitch.README.md` and `PITCH.md`, and adds one line pointing at
  `PITCH.md` to the end of `AGENTS.md` and `CLAUDE.md` (making `AGENTS.md` when
  neither exists). Those are the only changes made without a question; say
  which files it touched. Any other change, such as the single-origin change a
  multi-port repo needs, is shown as a diff and applied only on a yes.
- **Show the CLI's output, not a summary of it.** The rehearsal's own words are
  the evidence the user decides on. Quote the part that says what would go
  live, in full.
- **Never print, echo or store a token or a viewer link's URL** anywhere except
  the one reply that hands the link to the user.

## Steps

1. **Find the project.** Use the path given as an argument, or else the
   repository root of the current directory. If there is more than one likely
   project (a monorepo with several apps), list them and ask which one. Do not
   guess.

2. **Check the CLI and the sign-in.** Run `roomi --help`. If `roomi` is missing,
   offer to install it, saying what the command does: it installs the Roomi CLI from npm, and needs Node 24 or later.

   ```sh
   npm install -g @getroomi/cli
   ```

   Run it only on a yes; on a no, give them the command and stop. Once it is
   installed, or if a later command says they are not signed in, ask them to
   run `roomi login` themselves (it opens a browser for them to approve) and
   stop until they say it is done. Never try to sign in for them.

3. **Make sure there is a manifest.** If there is no `pitch.json` at the
   project root, run `roomi init` there. It detects the apps, their ports and
   routes, and writes `pitch.json`, `pitch.README.md` (what it found, and a
   story's shape on these apps) and `PITCH.md`. It never asks with nobody at
   the terminal: when it cannot tell something (usually an app's device), it
   stops, writes nothing, and names the flag that answers, such as
   `--device app=laptop`.
   Relay those questions to the user rather than answering them yourself,
   then run it again with their answers as flags. Show the `pitch.json` it
   wrote. Never pass `--force` unless the user asked to replace theirs.

   **The type of room.** `roomi init` also asks "A standard room, or an
   immersive one?", and with nobody at the terminal it writes a standard
   room. Relay it as the others: pass `--room immersive`, or add
   `"room": "immersive"` beside `kind` in `pitch.json`, only on the user's
   yes. Never choose it for them. The type changes only with a new deploy,
   and every viewer link stays.

   **Then read `PITCH.md`.** It is the guide `roomi init` writes for any
   coding agent, drawn from the docs of the `roomi` installed here: the rules,
   the container build contract, every field of `pitch.json` and how to write
   the story. Rely on it for what `roomi` checks and how; where it and this
   skill differ on a fact about `roomi`, it is the newer. The rules above, on
   when to stop and ask, stand either way. A project with a `pitch.json` and
   no `PITCH.md`, or one that names a command `roomi` refuses, gets
   `roomi init --update`: it rewrites `PITCH.md` and touches nothing else.

4. **If the repo serves on several ports** (`roomi init` or `roomi deploy` will say
   so), a container build needs them behind one origin. Propose the change as
   a diff: what to add or change, and in which files. Explain it in a sentence
   and ask. Apply it only on a yes, and then go back to step 3. On a no, stop
   and say what is blocking the deploy.

5. **Write the story, if there is none.** A viewer's room offers a guided tour
   built from `story` in `pitch.json`: a few steps, each saying what to notice,
   which app to show, and what to run on the demo first. It is how the demo
   gets told when the builder is not in the room, so a demo deployed without
   one is half-deployed. Draft it by reading the demo (its launcher, its demo
   or scenario routes, its seed data, its screens), following the story
   section of `PITCH.md`, or [story.md](story.md), which has Columbus's story
   as a worked example. Show the draft as the `story` block you would add, and
   add it on a yes; it is a change to `pitch.json` like any other. If the manifest has
   `controls.scenarios` and no story, offer to turn them into one: scenarios
   still work, but the tour is the better way to tell it.

6. **Validate.** Run `roomi validate`. It checks `pitch.json` and the story
   against the contract without building anything, including fields it does
   not know (a misspelt field would otherwise be silently ignored), and for a
   container build reads the Dockerfile against the build contract. Each
   problem prints the docs page that explains it; read that page before
   proposing a fix, and propose the fix as in step 5. Go on only when it
   passes.

7. **Look at it in the room.** While the story is being written and fixed,
   `roomi dev` serves the real room on this machine around the demo's own dev
   server (start that first, as the project's README or `package.json` says;
   for a built static demo, `roomi dev --static` needs no dev server). It runs
   until stopped, so start it in the background, and give the user the room's
   address it prints. It redraws the room whenever `pitch.json` or a page
   changes, and prints what is wrong in `roomi validate`'s and the rehearsal's
   words: a step's page that answers 404, a call the demo refused. Read those
   lines after each change and fix the story as in step 5. Nothing in it is
   recorded or sent, so it needs no yes. Stop it when you are done.

8. **Rehearse.** Run `roomi deploy` (no `--commit`). It checks the manifest
   again, scans for secrets, builds, runs the build locally and checks that it
   answers, runs each story step's calls against that local copy and reports
   each step `ok` or `FAIL`, then shows what would go live. Nothing leaves the machine. Show the output. If it failed, show
   the failure and what the CLI suggests, and stop. A step that fails is
   usually a step that needs something set first (a clock, a login, an
   earlier step's data): propose the fix to the story, as in step 5. Do not
   try fixes in the user's code unasked.

   An immersive room's rehearsal adds a `type:` line. If it says `FAIL type`,
   the workspace's plan has no immersive rooms, or they are not available
   yet; nothing was built or sent. Show it, and ask whether to deploy it as
   a standard room (take the `room` line out) or move the workspace's plan
   first. Do not change `room`, or the plan, unasked.

9. **Ask.** End your turn with one direct question, such as "Publish this to
   Roomi?", and wait. Anything other than a clear yes is a no.

10. **Publish, on yes only.** Run `roomi deploy --commit` and show its output,
   including the room URL it reports. Run it as it is: never pipe `roomi`
   through `tail` or anything else, which hides its exit status.

   **If it fails, stop.** Show its output in full, say what it says went
   wrong, and do not run it again: a deploy that failed once fails the same
   way, and each attempt spends one of the plan's deploys. A `FAIL type`
   after `ok   build: registered` means the platform did not keep `room`:
   the build was not made live, so say so and stop. A `fetch failed`
   right after `ok   upload` is not a network blip: the platform refused the
   build while unpacking it, usually for its size. Run `roomi validate`, and
   check the build against the static limits (at most 5,000 files, 32 MB per
   file and 256 MiB unpacked; a Next.js export writes every
   `generateStaticParams` entry as a page plus about five payload files), then
   propose the fix and rehearse again from step 8.

11. **Offer the next step for their plan.** The deploy's closing lines say which.
   On Free there are no private links: offer `roomi open` to preview it, and
   offer to write a Community card and list it there, which is how anyone
   else sees it. On a yes, follow the `roomi:list-on-community` skill:
   it drafts the card from the story, rehearses it, and lists only on a yes.
   On a paid plan,
   ask whether they want a viewer link, and for whom (a name,
   and optionally an email and an expiry in days). If they give an email, ask
   whether Roomi should email the link to that person for them, or whether
   they will send it themselves; never assume either way. On a yes, create it
   with the roomi MCP server's `create_link` tool (`send: true` to email it:
   it rehearses first, then `commit: true` on their yes to what it shows; or
   `send: false`), or with `roomi link <name> [--email <address> --send]` if
   the MCP server is not connected. Give the URL to the user in that reply and
   write it nowhere else. Say that `get_link` (or `roomi link copy <name>`)
   shows it again if it is lost, that it can be revoked with `revoke_link`, and
   offer the Community as well.

## Where the rules are written down

The docs are the reference for everything the CLI checks. Read the page
before guessing at a rule:

- https://getroomi.com/docs: getting started, and every command
- https://getroomi.com/docs/pitch-json: every field of `pitch.json`,
  with an anchor per field (the links `roomi validate` prints go straight
  to them)
- https://getroomi.com/docs/story: the story, as [story.md](story.md)
- https://getroomi.com/docs/containers: the build contract a
  Dockerfile must keep (WORKDIR /app, Node 24, a CMD, a health route, no
  install at start, mock data only)
- https://getroomi.com/docs/static: static builds
- https://getroomi.com/docs/pages: pages, and `roomi pages`
- https://getroomi.com/docs/preview: `roomi dev`, the room on this
  machine

## Pages

The room's "Read more" pages are Markdown or HTML files. Those listed in
`pitch.json`'s `pages` upload with every deploy. To change them without
deploying, `roomi pages list`, `roomi pages add <file> [--title <title>]` and
`roomi pages remove <id>` act at once, so each needs the user's go-ahead
first, as `add_page` does. `roomi pages add` refuses to replace a page of the
same title unless given `--replace`: ask before passing it.

## Afterwards

The same MCP server answers questions about the project once it is live:
`deploy_status`, `viewer_activity`, `read_feedback` and `pipeline` change
nothing. `create_link`, `revoke_link` and `add_page` each change something, and
each needs the user's go-ahead in the conversation first. `get_link` shows a
link's URL again and records that it did; use it when the user asks for one.

The server also has `deploy_static`, with `deploy_guide` and `check_bundle`
beside it, for a static demo held in a chat with no terminal (claude.ai, the
Desktop app); its instructions describe that case. Here, with the project on
disk, use `roomi deploy`: it builds, runs and scans the project first, and
`deploy_static` does none of that. That is the connector's own advice too: it
suggests the CLI for a container demo, a build over its limits, or work in a
repository with Claude Code, and offers to run the install where there is a
terminal, as step 2 does.
