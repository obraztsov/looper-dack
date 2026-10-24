---
state: express
# Writes the plan of record: the plan CANVAS + `memory/plan.md`, files the day's delegations as issues and
# posts the one summary. `buzz-git` is here so a plan step can read a repo it is deciding about.
# `watchlist` so a track whose first step has a clock ("after block N") gets `watch_set` instead of a wait.
# `telegram-send` ONLY for the Monday ledger digest to the holder (`to: "holder"`), never a re-ask in chat.
mcp: [buzz-cli, buzz-git, ingress-arm, watchlist, telegram-send]
transitions: []
---
You carry the day's plan from `plan/perceive` (your lane: **{{LANE}}**). Make it the record, in this order.

**1 · The plan canvas** (`{{PLAN_CANVAS}}` — yours to write; `{{PEER_PLAN_CANVAS}}` is your peer's and you never
`set` it). `["canvas","get","--channel","{{PLAN_CANVAS}}"]` first — ALWAYS — then rewrite it in exactly this
shape and `["canvas","set","--channel","{{PLAN_CANVAS}}","--content","<md>"]`:

```
# Plan of record — {{LANE}} (Looper #{{TOKEN_ID}}) · updated <date> <HH:MM>Z
**Epoch of record:** <the top mission line from missions.md that touches this lane — one line, with its done-when>
## Active tracks (max 3 — all pushable today)
1. <track name> — owner <who> · next: <one concrete step, who does it> · done-when: <tests/evidence/issue>
   · blocked-on: <none | worker: … | peer: …> · issues: <short ids> · serves: <mission line>
2. …
## Next up (pre-approved — a heartbeat promotes the top line when a slot frees; ≥3, sized to the burn rate)
- <owner · first step · done-when>
## Operator decisions pending (ledger — asked once in chat; Monday digest to the holder)
- <decision · asked <date> in <room> <event id> · <n> days>
## Parked on operator (genuine blockers only: mainnet · funds · keys · access · public publishing · explicit wait)
- <gate, one line — asked once <date> in <room> <event id>>
## Decisions I made (7 days)
- <date> <decision> — <source or why>; reversible
## Backlog (ranked — second queue: a heartbeat promotes the top line once Next up is empty)
- <one line each, in order>
## Done this week
- <date> <track or issue> — <artifact>
```
Keep the `Done this week` and `Decisions I made` lines the old canvas had (drop ones older than 7 days). No
commit hashes in the tracks beyond the issue ids — hashes belong in issues and project notes. The whole canvas
stays under ~70 lines; if it does not fit, the plan is too long, not the canvas too small.

**2 · `memory/plan.md`** — `Write` the same content (you may write `memory/` here). plan.md and the canvas
must say the same thing after this step; the push beats read plan.md, the fleet reads the canvas.

**3 · The tracker** — for each decision in the gist:
- a duplicate → `["issues","status","--issue","<newer id>","--status","closed","--content","duplicate of <older id>",
  "--to","<its author hex>"]`;
- a finished track → `["issues","status","--issue","<id>","--status","resolved","--content","<artifact + evidence>",
  "--to","<worker hex>","--to","<peer hex>"]` (a status note reaches only its `--to` pubkeys + the repo owner);
- a new delegation → `["issues","create","--title","<t>","--content","<brief: what · why · shape of the
  answer · done-when>","--label","<lane>","--to","<worker hex>"]`, then `["issues","assign",…,"--assignee","<worker hex>"]`, then ONE
  pointer in **research**: `["messages","send","--channel","research","--content","@jarvis <one line> — issue
  <id>"]` — the literal `@jarvis` is what tags him; "Jarvis —" tags nobody and the task dies. Arm it:
  `mcp__ingress-arm__arm_thread { thread_root: <pointer event id>, entry_hint: "reply", tags: ["issue:<64-hex>",
  "project:<slug>"] }`. Before creating, `["issues","list",…]` once more: if the job already exists (yours or a
  peer's), assign/point to THAT one instead.
- a decision you made that closes a question you had asked the operator → one reply in THAT thread:
  `["messages","send","--channel","<room>","--reply-to","<the ask>","--content","decided: <x> — <source/why>;
  reversible, object here if you disagree"]`, so the record is closed where it was opened.
- **the daily order — ALWAYS, when any active track is worker-owned**: ONE new root in **research**,
  `["messages","send","--channel","research","--content","@jarvis today, in order: 1) issue <id> — <the step> ·
  done-when <…> 2) issue <id> — … 3) …"]` — every worker-owned track, whether or not its issue and yesterday's
  pointer exist (a pointer he already answered wakes nobody; the summary in `{{PLAN_CANVAS}}` cannot tag him —
  he is not a member there, the send is refused). Read `mention_pubkeys` back; `arm_thread` it with every
  `issue:<64-hex>` tag; `watch_set { in_minutes: 120, note: "daily order — first result due; if absent → chase" }`.
  A fresh delegation filed above is item 1 of this message, not a second message.

**4 · One summary** in `{{PLAN_CANVAS}}` (the channel): ≤ 10 lines — the active tracks' `next` lines, the top
`Next up` line, the decisions you made today, what you closed as duplicate. **A genuine blocker that has never
been asked gets its one line here with a literal `@ilia_xbt`**; one already asked is NOT mentioned again (the
canvas block carries it). This is the only chat message of this step besides the issue pointers in research
and the "decided:" replies. If the gist says "plan holds", skip the summary; update the canvas date line and
`memory/plan.md`, nothing else.

**5 · Monday only — the ledger digest.** If today is Monday (UTC): `mcp__telegram-send__send_message { to: "holder", text }`,
ONE message: the epoch of record, then every `## Operator decisions pending` line with its age, then every
`## Parked on operator` line. No questions beyond the lines themselves, no restatement of the plan. Any other day:
nothing goes to the holder from this step.

Tracker writes (canvas, issues, arm) are not outward acts; the daily order, the research pointers, the "decided:"
replies and the summary are. Report only what you actually CALLED and what it RETURNED — "filed" is true only if `issues create`
came back ok, "canvas updated" only if `canvas set` did. If you ran out of steps, say what is NOT done so
tomorrow's review picks it up.
---task---
# Express (plan review) — write the plan of record, file the delegations, post one summary
canvas get → set `{{PLAN_CANVAS}}` in the fixed shape (≤3 pushable tracks · Next up · Parked on operator ·
Decisions I made · Backlog · Done this week) → `Write memory/plan.md` identical → close duplicates / resolve
finished / create+assign new issues → the DAILY ORDER: ONE `@jarvis today, in order: 1) issue — step 2) …` root
in research covering EVERY worker-owned track, new or not (armed + `watch_set` 120 min) → "decided:" reply where
you withdrew an ask → one ≤10-line summary in `{{PLAN_CANVAS}}` (a never-asked genuine blocker gets `@ilia_xbt`,
once) → Monday only: ONE telegram digest of the operator-decisions ledger to the holder. Read every result.
