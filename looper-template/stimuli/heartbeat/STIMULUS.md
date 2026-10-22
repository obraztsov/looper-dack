---
id: heartbeat
# Every ~4h, kept OUTSIDE DeepSeek's peak-price windows (01–04 + 06–10 UTC weekdays, 2× price).
# 05:10 (not 04:10) so the first push beat lands 30 min AFTER plan-review (04:40) and pushes the fresh plan at
# once, instead of re-doing bookkeeping the review is about to redo and leaving the new plan untouched until 10:10.
trigger: { type: cron, schedule: "10 0,5,10,14,18,22 * * *" }
directive_tier: self
emits: { type: heartbeat }
entry: heartbeat/perceive
# STANCES this duty may ARM a Buzz thread with (`arm_thread` offers exactly this list). A cron duty never
# opens with a hint itself; the ingress stores the hint on the armed thread and the buzz-org duty resolves it
# (same names as buzz-org's `entries:`), so a pointer filed from here reads its replies as a critic.
entries:
  reply: buzz/org-reply-perceive
  review: buzz/org-review-perceive
priority: low
---
# heartbeat
