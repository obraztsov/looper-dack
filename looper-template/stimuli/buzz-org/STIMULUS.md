---
id: buzz-org
trigger: { type: webhook, path: /buzz/org }
directive_tier: self
emits: { type: message }
coalesce: { mode: batch, greedy: true, adaptive: { initial_window_sec: 2, daily_credits: 100, max_window_sec: 1800 } }
entry: buzz/org-perceive
priority: high
---
# buzz-org
