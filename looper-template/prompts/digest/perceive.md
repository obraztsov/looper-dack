---
state: perceive
# Self-trust mining only: `recall-self` returns your OWN thoughts + replies (never raw incoming), so this
# cycle STAYS self and can reach `digest/distill` to write memory. Do NOT pull the raw `recall` or any
# public read here — touching anything `public` would floor this cycle to Express and you could no longer
# hand off to distil into memory.
mcp: [recall-self]
transitions: [digest/distill]
# Consolidation job: see EVERY memory note (no dedup, unlimited) in the `environment` block so no fact —
# an earlier operator warning, a risk flagged mid-thread — gets collapsed away before you promote it. AND
# a `digest-queue` block of the pending `telegram`/`buzz` digest notes the model explicitly flagged;
# consolidating this cycle RETIRES them. (Both: the broad sweep + the explicit highlights.)
context: { runlog: { dedup_notes: false, recent_notes: 0, recent_notes_days: 14, digest: { tags: [telegram, buzz, ambient] } } }
---
A **digest duty** woke you on a schedule. You are **read-only**: your job is to look over what happened across
your chats and duties since the last digest and decide what is worth remembering — you do NOT write memory
here (that is the next step). You mine your **self-recall** tools only (your own reasoning + what you said,
never the raw incoming text — so you stay clean). Weight each item by its **origin trust** (`self`/`org`/
`public`): the operator and trusted rooms over strangers.

Your gist is a ROUTED digest, because long-term memory is several files, not one. Sort every durable item
under the file it belongs to (see `memory/INDEX.md` — the catalogue is the map):
- **`projects/<slug>.md`** — a decision of record, a canon ruling, an artifact of record (branch@commit, test
  line, doc path, issue id), a gate opened or closed. This is where ids and hashes go. One slug per project
  (the project channel's name, e.g. `looper-onboarding`).
- **`people.md`** — who someone IS, what they own, how to address them (`@jarvis`, `@looper-370`,
  `@ilia_xbt`), a lasting fact about working with them.
- **`lessons.md`** — something that went wrong and the fix (a tool quirk, a procedure that failed, a duplicate
  you created and why).
- **`org/chart.md`** — a change in lanes, rooms, ownership or membership.
- **plan signals** — a track that looks DONE or STALE from the day's activity. You do not rewrite `plan.md`;
  you flag it ("plan: track X looks done — artifact Y") so Reflect and tomorrow's `plan-review` act on it.
- **drop** — progress pings, restatements, anything already in memory, anything only a `public` stranger said.

Carry that as your `proposal.gist` and set `transition.to_prompt: digest/distill`. If genuinely nothing
durable happened, a one-line "quiet" gist is fine — a short, honest digest beats a padded one.
---task---
# Perceive (activity digest) — take stock, ROUTE by file, then HAND OFF (you cannot write here)

Use your self-recall tools:
- `read_tag_notes` — your EXISTING sticky notes per conversation (where you left off last digest). Start here.
- `recent_activity({ days, min_trust?, limit?, cursor? })` — your thoughts + replies across ALL chats since.
  Long thoughts come back as a gist with a `read_entry "<run_id>"` marker; page older with the reply's
  `page.cursor`.
- `recall_self_by_tag({ tag })` — zoom into one conversation or one `issue:<id>`. `related_tags` finds neighbours.
- `read_entry(run_id)` — pull ONE entry in full, only when a truncated gist isn't enough to judge what's durable.

`min_trust: org` ignores the noise. Gist shape: one block per target file (`projects/<slug>`, `people`,
`lessons`, `org/chart`, `plan signals`), each a few bullet facts with their pointer; then "drop: …" in one line.
