---
state: perceive
# The write-enabled `buzz` tool lives in the EXPRESS step; this step LOOKS (read-only `buzz-read`) and decides
# the hygiene list. (`recall-self` only for your own history: reading public chat here would floor the cycle.)
mcp: [recall-self, buzz-read, buzz-tasks]
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

**LOOK NOW** — the reads, in order:
1. `mcp__buzz-tasks__tasks_board { state: "open" }` — EVERY open issue across every repo the relay shows you,
   with its state of record (the newest status event rooted on it, whoever it was addressed to), owner,
   author, hours idle, note count and last note; `open_by_owner` names the unowned pile. Then `tasks_board {
   state: "resolved", since_hours: 168 }` and `{ state: "closed", since_hours: 168 }` for the week's closes and
   duplicates. The board is regenerated from THESE rows, never from memory and never from `issues list` (which
   knows no state) or the mentions feed (which shows only notes that name you).
2. `["canvas","get","--channel","{{BOARD_CANVAS}}"]` (`mcp__buzz-read__buzz_read`) — your task board; and
   `["canvas","get","--channel","{{PLAN_CANVAS}}"]` — the plan, read-only here (only `plan-review` rewrites it).
3. For every open title that looks like a twin of another (same artifact, "ADDENDUM"/"RECORD"/"SUPERSEDES",
   a successor of a resolved issue): `mcp__buzz-tasks__tasks_find { text: "<title>" }` — a hit ≥ 0.35 is a
   duplicate candidate; `tasks_issue { id }` on both decides which survives (the older, unless the newer holds
   the work and the older is empty — then the OLDER closes as "superseded by").
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
  what it waits for must be a line under `## Open consults` on the c-level canvas, with the default you are
  working on. If it is not, leave a `digest` note "consult unlisted: <id> — <the question + your default>" so
  plan-review adds it and restates it; an operator-owned issue idle 7 days with no consult line is a planning
  failure, not the operator's.
- **Twin issues for one fact** (`tasks_find` on an active title shows another issue whose done-when the first
  already met — a fix verified on one, "open defect" on the other) → the met one resolves with "met by <id>".
- **A resolve with no verification label** (status note not opening with `verified:` / `accepted first-party:`):
  if the artifact is a DOCUMENT (docs-only commit, runbook, spec, canvas), `repo_show { dir, path, rev }` it at the
  named commit — it reads as claimed → it is checked, nothing to report (the board's `last note` may say
  `verified by sweep`). If it is a PIN (bytes gating a publish, deploy, mainnet call or funds) accepted first-party
  with no QA line anywhere → `digest` note "unchecked pin close: <id>" for plan-review. Do not reopen; the fix is a
  QA line, not a status flip — and a hash typo in a document is never a reopen either.

**Don't re-report what you already reported.** A sweep that re-escalates the same stalled issue every 8 hours
is noise; a sweep that CLOSES a duplicate every 8 hours is hygiene. If the trackers are clean and the board
is current, `[]` — and say so in your thought so the next sweep knows you looked.

Emit **ONE baton** `{ to_prompt: project/express, gist, tags: [sweep] }` whose gist IS the hygiene list:
each item with the exact issue ids, the target state and the one-line `--content`, plus "board: regenerate"
or "board: current". Leave `tag_notes` (`kind: digest`) for stale assignees the heartbeat should chase.
---task---
# Project sweep — keep the tracker TRUE (not a plan, not a chase)
`tasks_board` open + resolved/closed 168h (the state of record) → board + plan canvases → `tasks_find` on twin-looking
titles → recent activity. Build the hygiene list: close duplicates (newer = duplicate of older; "met by" for a
twin whose fact is already verified) · resolve what is done in substance, with evidence · assign unowned work ·
unlisted operator-owned waits → "consult unlisted" digest note · regenerate the `## Task board` section of
`{{BOARD_CANVAS}}` from the board rows. ONE baton to `project/express`; `[]` if clean.
