---
id: buzz-pub
trigger: { type: webhook, path: /buzz/public }
directive_tier: self
emits: { type: message }
coalesce: { mode: batch, greedy: true, adaptive: { initial_window_sec: 2, daily_credits: 100, max_window_sec: 1800 } }
entry: buzz/perceive
priority: low
---
# buzz-pub
