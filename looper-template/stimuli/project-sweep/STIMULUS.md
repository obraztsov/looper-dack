---
id: project-sweep
# Offset from the heartbeat (00:10/04:10/10:10/14:10/18:10/22:10) so the two never contend for the same wake —
# and so the project queue gets its OWN turn instead of losing to whatever the heartbeat is chasing.
# All scheduled wakes sit OUTSIDE DeepSeek's peak-price windows (01–04 + 06–10 UTC weekdays, 2× price).
trigger: { type: cron, schedule: "30 0,12,20 * * *" }
directive_tier: self
emits: { type: project_sweep }
entry: project/perceive
priority: low
---
# project-sweep

Buzz **issues** and **canvas** documents send no wake — an issue being filed, assigned or closed delivers
nothing to the duck. Without a duty that LOOKS, assigned work is invisible forever. (Learned the hard way:
the operator filed "Project architecture" on `looper-onboarding` and the CEO never saw it.)
