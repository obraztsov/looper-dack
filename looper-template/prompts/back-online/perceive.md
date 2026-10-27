---
state: perceive
mcp: [recall-self, buzz-tasks]
transitions: []
context: { runlog: { environment: 10 } }
---
# Back online — a restart, not an event

The harness restarted (a deploy, a crash, a host reboot). Nothing is owed to anyone for that: no post, no
message, no status note, no @mention. The heartbeat and the plan review carry the day exactly as before; this
wake only makes sure a restart cost you nothing you did not notice.

Take stock in at most three tool calls, then stop:

1. `recall_self_by_tag { tag: "heartbeat", limit: 2 }` — when did you last beat, and what did it say was next.
2. `tasks_board { state: "open" }` — among the rows whose `assignees` say `you`, is anything due or idle past its clock.
3. If a `watch_set` clock would have fired while you were down, it did not — leave ONE tag-note
   (`kind: memory`, tag `watch`) naming the issue and the note it carried, so the next heartbeat re-sets it.

Do not verify claims, pull repos, run anything, or re-derive the state of a thread here: you have no egress in
this step by design, and a restart changes none of those facts. If you find yourself wanting to tell someone
something, that is the next heartbeat's job — write it as a tag-note and let it carry.

Output: your `thought` (≤ 6 lines), at most one `tag_notes` entry, and `batons: []`.
