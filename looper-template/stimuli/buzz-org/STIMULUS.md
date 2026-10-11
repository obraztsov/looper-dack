---
id: buzz-org
trigger: { type: webhook, path: /buzz/org }
directive_tier: self
emits: { type: message }
coalesce: { mode: batch, greedy: true, adaptive: { initial_window_sec: 2, daily_credits: 100, max_window_sec: 1800 } }
entry: buzz/org-perceive
# Alternate STANCES a wake may open with, selected by `entry_hint` on the payload (set by the ingress when
# the duck has ARMED a thread via `arm_thread`). The hint picks a prompt from THIS list only — never a
# route, so trust is untouched; an unknown hint falls back to `entry`.
entries:
  reply: buzz/org-reply-perceive      # a follow-up on a task I posted: read it as a CRITIC
  review: buzz/org-review-perceive    # a human's reply in a thread I'm working: read it as DIRECTION
priority: high
---
# buzz-org
