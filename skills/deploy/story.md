# Writing the story

The room's tour is `story` in `pitch.json`: an ordered list of steps. A viewer
reads a step's words, the room puts its app on the stage at the page the step
opens, in the frame the step asks for, runs its calls on that viewer's own
copy of the demo, and rings the part of the screen it points at. "Explore
freely" leaves the tour at any point, and the outline lets a viewer start at
any step.

## The shape

```json
{
  "$schema": "https://getroomi.com/schema/pitch.json",
  "name": "Columbus",
  "kind": "static",
  "output": ".next-build",
  "apps": [
    { "id": "columbus", "name": "Columbus", "path": "/", "device": "laptop" }
  ],
  "story": [
    {
      "id": "score",
      "title": "A score with its reasons",
      "say": "Project Kestrel scores 74 against the house's own mandate. Each of the eight axes gives its reason, and the binding one is named: a 10–12% target sits below the house's 12–18% floor.",
      "app": "columbus",
      "path": "/transactions/kestrel/overview/"
    }
  ]
}
```

| Field | What it is |
|---|---|
| `id` | Lower-case letters, digits and hyphens; unique in the story |
| `title` | What happens, in a few words (60 characters at most) |
| `say` | What the viewer should notice, in one or two sentences (300 at most) |
| `app` | The id of an app in `apps`: the one the step is seen in |
| `path` | Optional. The page of that app the step opens, on the build's own origin, starting with `/`. Without it, the app is shown where the viewer left it |
| `device` | Optional. The frame the room draws the app in for this step only: phone, tablet, laptop, screen or none. The app's own frame comes back when the viewer moves on |
| `run` | Optional. Calls made in order on the build's own origin: `"METHOD /path"`, or `{ "call": "METHOD /path", "body": { … } }` when the route needs a JSON body. A container build only |
| `point` | Optional. A CSS selector in that app's page to ring. Best-effort: nothing breaks if it is not there |

A static build can have a story; its steps open pages, show and point, and
have no `run`.

## How to write one

1. **Find what the demo can be told to do.** On a container build, read its
   launcher, its demo or scenario routes, its seed and its clock: the calls in
   `run` are those routes. Read each handler: what it needs to be true first,
   and what it answers. A JSON reply's `message` is what the room shows as
   "Done: …", and `error` as "Didn't work: …", so a route that answers in
   words reads well. On a static build there is nothing to call, so read its
   screens instead: which ones make the case, and the path each is at.
2. **Tell it in three to six steps.** One idea a step, in the order a person
   would pitch it: who it is for, the moment the product matters, what the
   business sees. A step that only shows an app (no `run`) is fine and often
   right.
3. **Make every step stand on its own.** A viewer can jump to any step from
   the outline, so a step sets up what it needs itself. If a scenario only
   works at a certain demo time or after certain data exists, put that call
   first in the same step ([Container demos](https://getroomi.com/docs/containers#story-calls)
   has a worked example). A step that shows a particular screen names it
   with `path`, so it opens there whatever the viewer clicked before.
4. **Show it where it lands.** `app` is where the effect is seen, which is
   often not where it was caused: a check-in made on the phone is seen on the
   coach's iPad. `path` is the page of that app the step is about: the room
   opens the app there, and the viewer can click on from it as usual. Leave
   `path` out to show the app wherever the viewer left it.
5. **One app per product, not per screen.** An app in `apps` is a separate
   surface with its own tab in the room: Bullring's client app on a phone,
   the coach's iPad, the HQ console, the arena's wall screen. The screens of
   one web app are one app, and each step reaches its screen with `path`. A
   different frame is not a different app either: to show the same page on a
   phone, give the step `"device": "phone"`.
6. **Point at something stable.** Prefer an id, a `data-` attribute or a class
   the demo's own code names, on the page the step opens. Never a generated
   class name. Leave `point` out rather than guess.
7. **Write `say` for the viewer, not the builder.** What to notice and why it
   matters, in the product's words. No instructions about the room.

## A worked example: Columbus

Columbus is an interactive prototype of a private-markets introduction
service: a register of family offices (it calls them houses) on one side, a
catalogue of transactions on the other, and a suitability score between them
with its reasons written out. Every house, deal and figure in it is invented.
It is a Next.js app exported as files, so it is a static build: nothing runs
on a server, and no step has `run`.

It is one app, a web app with a side bar. The story stops at four of its
pages, each named by the step's `path`, and then shows the first page again
in a phone's frame:

```json
"apps": [{ "id": "columbus", "name": "Columbus", "path": "/", "device": "laptop" }],
"story": [
  {
    "id": "briefing",
    "title": "What needs the house today",
    "say": "Arundel House, an invented family office, opens Columbus on a concierge rather than a feed: two things are waiting on them, one closes in four days, and nothing else needs them today.",
    "app": "columbus",
    "path": "/"
  },
  {
    "id": "score",
    "title": "A score with its reasons",
    "say": "Project Kestrel scores 74 against the house's own mandate. Each of the eight axes gives its reason, and the binding one is named: a 10–12% target sits below the house's 12–18% floor.",
    "app": "columbus",
    "path": "/transactions/kestrel/overview/"
  },
  {
    "id": "table",
    "title": "A seat at the table",
    "say": "Four family offices take Project Anvil's £40m round between them. Arundel House's seat is £12.0m, and the cap table shows what it would hold: 7.4% fully diluted.",
    "app": "columbus",
    "path": "/transactions/anvil/structure/"
  },
  {
    "id": "audited",
    "title": "The score, held to account",
    "say": "Columbus keeps the score it gave on the day the house committed, and sets it beside what happened: Thornbury scored 91 at entry and has returned 2.00× since. Old scores are never revised.",
    "app": "columbus",
    "path": "/portfolio/thornbury/overview/"
  },
  {
    "id": "phone",
    "title": "The same briefing on a phone",
    "say": "On a phone the navigation folds into a drawer and tables become cards, so a principal can read what is waiting on them between meetings.",
    "app": "columbus",
    "path": "/",
    "device": "phone"
  }
]
```

Why it is written the way it is:

- **One app, five pages.** Columbus is one product, so the room shows one
  app, with no switcher. Each step opens the page it talks about, with the
  trailing slash the export writes its folders with
  (`/transactions/kestrel/overview/`), and the viewer can click around from
  there as they would in Columbus itself.
- **Every step stands on its own.** There is nothing to set up on a static
  build, and every step names its page, so a viewer who jumps to step 3 from
  the outline sees the Anvil table as the step describes it, wherever they
  had clicked to before.
- **The phone is a frame, not an app.** The last step is the first page
  again with `"device": "phone"`: the same build, drawn at a phone's size.
  When the viewer moves on or leaves the tour, the laptop comes back.
- **The order is the pitch.** What the house sees first, the one idea that
  sets the product apart (a score with its reasons), the mechanic that makes
  money (a table of houses), the claim only this product makes (old scores
  kept and audited), and the proof it works away from a desk.
- **`say` is in the product's words**, about Arundel House and its deals, not
  about the room or the buttons. Every figure in it is one the page shows.
- **No `point`.** Columbus's own code names no ids or `data-` attributes on
  these pages, and its classes are Tailwind's, so there is nothing stable to
  ring. The step leaves it out rather than guess.

A container build tells its story the same way, and its steps can also run
calls on the viewer's copy first: [Container demos](https://getroomi.com/docs/containers#story-calls)
has Bullring's four-step story, which sets its demo's clock before each
scenario. Bullring really is four apps (the client's phone app, the coach's
iPad, the HQ console and the arena's wall), each at its own path on one
server, so its `apps` has four and its steps move between them.

## Checking it

`roomi validate` checks the story without building anything, and `roomi
deploy` checks it again first: each refuses a story whose step names an app
that is not in `apps`, repeats an id, has a malformed call, a `path` that
does not start with `/` or a `device` the room has no frame for, a misspelt
field, or runs calls on a static build, and points at the docs for each.

A step's page is then checked the way the room will serve it. On a static
build, each `path` must be a file in the output: the path itself, or the
`index.html` inside it (`/transactions/kestrel/overview/` is
`.next-build/transactions/kestrel/overview/index.html`). `roomi validate`
looks in the output as it is on disk, when there is one, and `roomi deploy`
looks again in what it has just built, and stops before uploading if a page
is missing. A route a single-page app draws in the browser has no file of its
own, so it cannot be a step's page; the app's own path can, and the viewer
clicks on from there.

On a container build the rehearsal runs every step's calls, in order, on its
local copy, then asks the copy for the step's page, and prints `ok` or `FAIL`
per step with what the demo said. A page must answer with a 2xx, or with a
3xx that sends the viewer on. A `FAIL` is a step a viewer would see fail: fix
the story (usually a missing first call, or a mistyped path), not the demo,
and rehearse again. A static build's steps have nothing to run, so its
rehearsal lists them under what would go live, with the page and frame each
opens. `point` selectors, and whether a page shows what its step says, are
not checked by either.

Look at them in the room itself before you publish: `roomi dev`, beside the
demo's own dev server, serves the real room on your machine and opens it.
Take the tour there: each step opens its page in its frame, and its `point`
is rung or it is not. A step's page the dev server answers with a 404, and a
call the demo refuses, are listed on the room in the rehearsal's words, and
the room redraws each time you save `pitch.json`. Nothing in it is recorded
or sent. [Preview locally](https://getroomi.com/docs/preview) has the details.
