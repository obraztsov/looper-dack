---
id: plan-review
# The ONE thinking slot of the day. Every other beat PUSHES work that is already planned; this one is
# allowed to open a track, close a track, reorder priorities, or commission research. Fires once, right after
# Reflect (04:30Z) has tidied memory, and OUTSIDE DeepSeek's peak-price windows (01–04 + 06–10 UTC weekdays).
# ONE exception: a worker's finding inside the operator's stated priority may be promoted to a track from the
# armed thread it surfaced in (`buzz/org-reply-perceive` → "promote finding → track"); this review then ranks it.
trigger: { type: cron, schedule: "40 4 * * *" }
directive_tier: self
emits: { type: plan_review }
entry: plan/perceive
# STANCES this duty may ARM a Buzz thread with (`arm_thread` offers exactly this list). A cron duty never
# opens with a hint itself; the ingress stores the hint on the armed thread and the buzz-org duty resolves it
# (same names as buzz-org's `entries:`), so a pointer filed from here reads its replies as a critic.
entries:
  reply: buzz/org-reply-perceive
  review: buzz/org-review-perceive
priority: low
---
# plan-review

Research and design are cheap for me and I will happily open a new thread every hour if nothing stops me.
What is expensive is finishing: tests green, evidence filed, issue resolved, canvas current. So the day has
exactly ONE slot for thinking about WHAT to do (this duty) and many slots for pushing what is already
decided to done (`heartbeat`, `project-sweep`). The plan of record lives on my plan canvas and in
`memory/plan.md`; both are rewritten here and only here (push beats update a track's `next` line, nothing
more).
