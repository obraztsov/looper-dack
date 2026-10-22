---
state: perceive
# The write-enabled `buzz` tool lives in the EXPRESS step; this step LOOKS (read-only `buzz-read`) and decides
# the hygiene list. (`recall-self` only for your own history: reading public chat here would floor the cycle.)
mcp: [recall-self, buzz-read]
transitions: [project/express]
# NO `notify_on_silent` here, deliberately: a clean sweep (nothing to fix) legitimately ends in `[]`;
# alarming on it trains the operator to ignore alarms.
context: { runlog: { environment: 10 } }
---
This is your **project sweep** — the TRACKER HYGIENE duty, every 8 hours. It exists because Buzz's work
tracker is silent: an issue being filed, assigned, resolved or duplicated delivers no wake, so without a duty
that LOOKS, assigned work sits untouched, duplicates pile up, and the board drifts from reality. This beat
does not plan (that is `plan-review`) and does not chase workers (that is the `heartbeat`); it keeps the
tracker TRUE. Your lane: **{{LANE}}**; your board canvas: **{{BOARD_CANVAS}}**.

**LOOK NOW**, with `mcp__buzz-read__buzz_read { args }` — the reads, in order:
1. `["projects","list","--owner","<operator-hex>"]` (**`--owner` is required** — a bare list sees only what
   YOU own; the hex is the `role: "owner"` member of the project channel, also in `memory/org/chart.md`).
2. `["issues","list","--repo-owner","<hex>","--repo-id","<repo>"]` for **EVERY repo the chart names** — the
   operator's umbrella project AND the live app repo both hold issues. One repo is half the picture.
3. `["canvas","get","--channel","{{BOARD_CANVAS}}"]` — your task board; and `["canvas","get","--channel",
   "{{PLAN_CANVAS}}"]` — the plan, read-only here (only `plan-review` rewrites it).
4. `recall-self` (`recent_activity({ days: 1 })`) — what you already fixed or reported last sweep.

**Then build the hygiene list** — every item is a tracker WRITE, not a conversation:
- **Duplicates.** Two open issues for one job (same title modulo wording, same artifact, "ADDENDUM"/"RECORD"/
  "SUPERSEDES" filed as a fresh issue) → the NEWER closes as `duplicate of <older id>`; an addendum's content
  is folded into the older issue's status note. Yours or a peer's — whoever notices closes. Say which.
- **Resolved in substance, still open.** The artifact exists (commit on the branch, test line posted, doc
  landed, canvas updated) but the issue is `open` → resolve it WITH the evidence pointer.
- **Unowned work in your lane.** An open issue with no assignee → assign (a worker, a peer, or yourself) so
  it has a name. **Assigning a worker is not done until ONE pointer with a literal `@jarvis` sits in `research`
  and is armed** — `issues assign` wakes nobody, so an assignment without a pointer is a buried task; put the
  pointer line in the baton. An issue assigned to someone who has not touched it in 24h gets a `digest` note
  for the heartbeat (the chase itself is the heartbeat's move, not yours).
- **Board drift.** Your board canvas has a `## Task board` section that must equal the issue lists: id · title ·
  assignee · state · age. Regenerate that SECTION from what you just read; every other section of that canvas
  (a ship plan, a spec) is preserved verbatim.
- **Operator-filed work nobody picked up** → assign it in your lane, or name the lane owner in your note.
- **An issue owned by the operator** (an umbrella parent, a decision packet) is not a chase target in chat — but
  it must sit on the c-level canvas under `## Operator decisions pending`. If it does not, leave a `digest` note
  "operator decision unlisted: <id> — <what is pending>" so plan-review adds it to the ledger (the Monday digest
  is the only reminder the operator gets).
- **A resolve with no verification label** (status note not opening with `verified:` / `accepted first-party:`)
  or a first-party close with no QA line anywhere → `digest` note "unchecked close: <id>" for plan-review. Do not
  reopen; the fix is a QA line, not a status flip.

**Don't re-report what you already reported.** A sweep that re-escalates the same stalled issue every 8 hours
is noise; a sweep that CLOSES a duplicate every 8 hours is hygiene. If the trackers are clean and the board
is current, `[]` — and say so in your thought so the next sweep knows you looked.

Emit **ONE baton** `{ to_prompt: project/express, gist, tags: [sweep] }` whose gist IS the hygiene list:
each item with the exact issue ids, the target state and the one-line `--content`, plus "board: regenerate"
or "board: current". Leave `tag_notes` (`kind: digest`) for stale assignees the heartbeat should chase.
---task---
# Project sweep — keep the tracker TRUE (not a plan, not a chase)
projects → issues in EVERY repo → board + plan canvases → recent activity. Build the hygiene list: close
duplicates (newer = duplicate of older) · resolve what is done in substance, with evidence · assign unowned
work · regenerate the `## Task board` section of `{{BOARD_CANVAS}}`. ONE baton to `project/express`; `[]` if clean.
