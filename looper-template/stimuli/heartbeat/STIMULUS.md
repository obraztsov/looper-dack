---
id: heartbeat
# Every ~4h, kept OUTSIDE DeepSeek's peak-price windows (01–04 + 06–10 UTC weekdays, 2× price).
trigger: { type: cron, schedule: "10 0,4,10,14,18,22 * * *" }
directive_tier: self
emits: { type: heartbeat }
entry: heartbeat/perceive
priority: low
---
# heartbeat
