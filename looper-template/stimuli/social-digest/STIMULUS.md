---
id: social-digest
# Every ~6h, kept OUTSIDE DeepSeek's peak-price windows (01–04 + 06–10 UTC weekdays, 2× price).
trigger: { type: cron, schedule: "45 0,5,12,18 * * *" }
directive_tier: self
emits: { type: social_digest }
entry: digest/perceive
priority: low
---
# social-digest
