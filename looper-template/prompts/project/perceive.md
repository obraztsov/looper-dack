---
state: perceive
# The `buzz` tool lives in the EXPRESS step, so the queries happen there — this step decides what to look
# at and what it means. (`recall-self` only: reading public chat here would floor the cycle and cost the
# express step its org-tier reach.)
mcp: [recall-self]
transitions: [project/express]
# A sweep that produces nothing at all is suspicious — it means even the LOOK didn't happen.
notify_on_silent: true
context: { runlog: { environment: 10 } }
---
This is your **project sweep** — the duty that exists because Buzz's work tracker is silent. Issues and
canvas documents generate NO wake: nothing is delivered when a task is filed, assigned, or closed. If you
only ever react to messages, assigned work sits untouched indefinitely.

So: go and LOOK, then decide what to do about what you find.

Emit ONE baton to `project/express` whose gist says to sweep the projects and what you care about. The
express step holds the `buzz` tool and will run, in order:
1. `["projects","list","--owner","<operator-hex>"]` — the org's projects. **`--owner` is required**: a bare
   `projects list`, or `projects get <slug>`, only sees projects YOU own, which is almost none of them.
   Need the hex? `["channels","members","--channel","<id>"]` → the member with `role: "owner"`. Each
   project's `a` tag (`30617:<owner>:<repo-id>`) gives the repo coords; `buzz-channel` gives the channel.
2. `["issues","list","--repo-owner","<hex>","--repo-id","<repo>"]` — the open queue.
3. `["canvas","get","--channel","<project-channel>"]` — the current scope/spec.

Then ONE real move on what it finds — in the SAME express step, since you now know the state:
- an open issue in your lane → do the work, or say concretely when you will, and set its status;
- a scope that drifted from reality → update the canvas (`get` → edit → `set`; `set` REPLACES, so never
  skip the `get`);
- work that needs Hermes → file/assign an issue and post a one-line pointer in the channel;
- genuinely nothing open and the canvas is current → `[]` is a fine answer. Say so in your thought so the
  next sweep knows you looked.

**Don't re-report what you already reported.** Check your recent activity (`recall-self`) first: a sweep
that re-escalates the same stalled issue every 8 hours is noise. If nothing changed since your last sweep,
stay quiet rather than restate it.
---task---
# Project sweep — look at the tracker, then act on it
ONE baton to `project/express`: sweep projects → issues → canvas, then make the single highest-value move
on what's actually there. Quiet if nothing changed. `skills/buzz/SKILL.md` has the full command reference.
