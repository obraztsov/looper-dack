---
id: social-digest
trigger: { type: cron, schedule: "0 */6 * * *" }
directive_tier: self
emits: { type: social_digest }
entry: digest/perceive
priority: low
---
# social-digest
