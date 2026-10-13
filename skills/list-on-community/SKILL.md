---
name: list-on-community
description: Lists a deployed Roomi project on the Roomi Community. It drafts the Community card (a title, one line and a cover) from the project's story, rehearses it with the pre-flight check and the listing's own checks, saves it and lists it only when the user says yes, then reports where the listing stands. Use when the user says "list it on the community", "put this on the Roomi community", "write a community card", "make it public", asks where their listing stands, or right after roomi:deploy publishes a project.
argument-hint: "[path to the project, if not the current one]"
---

# List on the Roomi Community

Listing puts a deployed project on the Community: public at one address,
behind a card strangers see. Your job is to draft the card, show it, and stop
at each decision. A separate skill from `roomi:deploy` because it
is asked for on its own (days after a deploy, or to see where it stands), and
because it changes something public, which deserves its own yes.

## What listing means, to say before anything is asked

- **Checked by Roomi.** Asking to list runs Roomi's checks on
  the card and the build. If they pass, it is listed at once; if one fails, it
  is not listed, and the user sees which part and why. Roomi may
  take a listing off later, with a comment saying why; asking again then
  waits for review.
- **Public means public.** Anyone with the address opens it, with no link and
  no account. Five people can be in it at once. On Free, its analytics are
  total views only.
- **Changes are checked again.** Changing a listed card or deploying a new
  build runs the checks again: it stays listed unless one fails. A new cover
  shows on the Community once its own check has passed.
- **It needs a live build.** Deploy first (`roomi:deploy`).

## Rules that do not bend

- **Nothing changes without a yes to its rehearsal.** Every `roomi community`
  command rehearses without `--commit`, and the connector's tools without
  `commit: true`. Show the rehearsal's own output, then ask. A yes to an
  earlier rehearsal does not carry over once anything changes.
- **The card names nobody real.** It is for strangers, so it is written for
  someone who does not know the business, and names no real client. The
  rehearsal runs a check that names anything that might identify one (a
  real-looking address, a name that keeps coming up, an image named like a
  logo): tell the user what each might reveal, and offer neutral wording.
- **Never call the people who look at a card anything but Roomi.**

## Steps

1. **Find the project.** The folder named, or the repository you are in,
   with its `pitch.json`. `roomi community status` says whether it has a card
   and where its listing stands. If nothing is live, say so and offer
   `roomi:deploy`; stop.

2. **Draft the card.** Read the `story` in `pitch.json`, and `PITCH.md`'s
   account of the demo if there is one, and write:
   - a **title** of up to 60 characters, saying what the demo is;
   - **one line** of up to 140, saying what it does for someone, without a
     real company's name.

   For the **cover**, a PNG, JPEG or WebP image of at most 1 MB: a screenshot
   of the demo's main screen is best. Use one the repository already has
   (look in the build output, `public/`, `docs/`, or what the story's pages
   show), or ask the user for one. Never make up an image of a real product
   or logo.

3. **Show the draft.** The title, the line and the cover's file, as you would
   send them. Change it as the user asks.

4. **Rehearse the card.** Run
   `roomi community card --title "<title>" --line "<line>" --cover <file>`.
   Show its output: the card as it would be, and what the check found. If the
   check names anything real, say what it might reveal and offer neutral
   wording; go back to step 3 on a change.

5. **Ask, then save.** One direct question: "Save this card?". On a clear yes
   only, run the same command with `--commit` and show its output.

6. **Rehearse the listing.** Run `roomi community list`. Show what it says
   listing means, whether the checks would list it now or what they would
   block, and the pre-flight check.

7. **Ask, then list.** One direct question: "List it on the Community?".
   On a clear yes only, run `roomi community list --commit` and show its
   output.

8. **Report the status.** Say where it stands: listed, with its address; not
   listed, with what a check found and what to change; or waiting for review.
   `roomi community status` (or asking you again) says it later.
   `roomi community withdraw` takes it off, and rehearses first too.

## Without a terminal, or without roomi

The roomi MCP server has the same steps as tools: `community_card`
for status, `set_community_card` for the card (`coverFile` for an image
already in the live build, which the platform copies; `cover` for an image's
bytes as base64), `request_listing` to list it, `withdraw_listing`, and
`appeal_listing` to ask Roomi to review a block or a decision to take
it off. Each rehearses without `commit: true`; the same rules hold.
`list_notices` reads what Roomi has told the user.

## Where it is written down

- https://getroomi.com/docs/listing: listing from Claude, the CLI or the app
- https://getroomi.com/docs/community: the card, the checks and the public address
