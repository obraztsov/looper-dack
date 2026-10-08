---
id: project-sweep
# Offset from the heartbeat (which runs at :00 every 4h) so the two never contend for the same wake —
# and so the project queue gets its OWN turn instead of losing to whatever the heartbeat is chasing.
trigger: { type: cron, schedule: "30 1,9,17 * * *" }
directive_tier: self
emits: { type: project_sweep }
entry: project/perceive
priority: low
---
# project-sweep

Buzz **issues** and **canvas** documents send no wake — an issue being filed, assigned or closed delivers
nothing to the duck. Without a duty that LOOKS, assigned work is invisible forever. (Learned the hard way:
the operator filed "Project architecture" on `looper-onboarding` and the CEO never saw it.)
