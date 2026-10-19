---
id: project-sweep
# Offset from the heartbeat (00:10/04:10/10:10/14:10/18:10/22:10) so the two never contend for the same wake —
# and so the project queue gets its OWN turn instead of losing to whatever the heartbeat is chasing.
# All scheduled wakes sit OUTSIDE DeepSeek's peak-price windows (01–04 + 06–10 UTC weekdays, 2× price).
trigger: { type: cron, schedule: "30 0,12,20 * * *" }
directive_tier: self
emits: { type: project_sweep }
entry: project/perceive
# STANCES this duty may ARM a Buzz thread with (`arm_thread` offers exactly this list). A cron duty never
# opens with a hint itself; the ingress stores the hint on the armed thread and the buzz-org duty resolves it
# (same names as buzz-org's `entries:`), so a pointer filed from here reads its replies as a critic.
entries:
  reply: buzz/org-reply-perceive
  review: buzz/org-review-perceive
priority: low
---
# project-sweep

Buzz **issues** and **canvas** documents send no wake — an issue being filed, assigned or closed delivers
nothing to the duck. Without a duty that LOOKS, assigned work is invisible forever. (Learned the hard way:
the operator filed "Project architecture" on `looper-onboarding` and the CEO never saw it.)
