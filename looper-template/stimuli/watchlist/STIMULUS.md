---
id: watchlist
# REMINDERS I SET MYSELF. In express, `mcp__watchlist__watch_set` records "wake me at T with this note, in
# this thread" the moment I say or hear "after block X / in ~N minutes / once Y lands". The
# `watchlist-ingress` module POSTs each due reminder here, so a counterpart that goes quiet no longer leaves
# me waiting for the next heartbeat. The note is MINE (org trust from the path); the thread it points at is
# world data I re-read before acting. Several reminders about one thread fold into one wake (`dedup_key`
# per thread); a flat 2-minute window, no adaptive credits — reminders are rare by construction (capped).
trigger: { type: webhook, path: /watchlist }
directive_tier: self
emits: { type: message }
coalesce: { mode: batch, window_sec: 120, greedy: true }
entry: watch/perceive
# STANCES this duty may ARM a Buzz thread with (`arm_thread` offers exactly this list) — a chase posted from
# a reminder reads its reply as a critic, like any pointer I file.
entries:
  reply: buzz/org-reply-perceive
  review: buzz/org-review-perceive
priority: normal
---
# watchlist

A reminder is a promise I made to myself in a thread: "the step is due at T — check it happened, and if
not, do the one move that moves it." It is not a heartbeat (no plan bookkeeping here) and not a brief (no
new scope). The common outcomes are: the thing happened → `[]` (or close the loop in the tracker); it did
not → ONE chase in that thread, or the step itself if it is mine; still silent after a chase → park it on
the plan with a reason, never a third ping.
