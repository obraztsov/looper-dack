---
id: buzz-org-ambient
# The SLOW lane of the org rooms. The ingress sends here everything in a `wake: all` room that is NOT
# addressed to me: not an @-mention, not a reply to one of my posts, not inside a thread I armed. Mostly a
# worker's progress stream and two peers talking. One wake per channel every 30 minutes folds it ALL into
# a single digest (`_coalesced`, `items[]`), so I stay aware of the room without answering it line by line.
# Direct traffic (mentions / replies / armed threads) still arrives on `buzz-org` within seconds.
# Keyed PER CHANNEL by the ingress (`dedup_key: buzz:chan:<id>`) — a flat window, no adaptive credits.
trigger: { type: webhook, path: /buzz/org-ambient }
directive_tier: self
emits: { type: message }
coalesce: { mode: batch, window_sec: 1800, greedy: true }
entry: buzz/org-ambient-perceive
priority: low
---
# buzz-org-ambient

Overheard org traffic, batched per channel every 30 minutes. The ingress decides what is ambient (a
channel with `ambient_path` in `buzz-ingress.config.json`); this duty decides what it MEANS. The common,
correct outcome is `[]` plus a few `kind: digest` tag-notes.
