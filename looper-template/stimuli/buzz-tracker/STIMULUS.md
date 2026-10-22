---
id: buzz-tracker
# The TRACKER lane. Buzz has no issue-comment verb: a "comment" on an issue is a kind:1630 status event
# carrying markdown (every worker's "status note"), rooted on the issue, addressed by `p` tags. It lives on
# no channel, so the room polls never saw it — the ingress reads it off the relay's activity feed
# (`feed get --types mentions,needs_action`, config `tracker_path`) and POSTs it here: issues filed or
# assigned to me, status notes `--to` me, resolves on issues I coordinate, PRs, workflow approvals, reminders.
# Keyed PER ISSUE by the ingress (`dedup_key: buzz:issue:<root>`): a worker's burst of notes on one issue
# folds into one wake. Trust comes from the path (org) — the content is still untrusted world text.
trigger: { type: webhook, path: /buzz/tracker }
directive_tier: self
emits: { type: message }
coalesce: { mode: batch, window_sec: 120, greedy: true }
entry: tracker/perceive
# STANCES this duty may ARM a Buzz thread with (a pointer filed from here reads its replies as a critic).
entries:
  reply: buzz/org-reply-perceive
  review: buzz/org-review-perceive
priority: normal
---
# buzz-tracker

Issue, PR and status-note events addressed to me, read off the relay feed. The record of a task lives
on its issue; this duty keeps me reading that record instead of reconstructing it from chat and memory.
