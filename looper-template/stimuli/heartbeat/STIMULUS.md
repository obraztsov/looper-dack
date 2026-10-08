---
id: heartbeat
trigger: { type: cron, schedule: "0 */4 * * *" }
directive_tier: self
emits: { type: heartbeat }
entry: heartbeat/perceive
priority: low
---
# heartbeat
