---
state: express
# The project layer + the tools to act on what it says. `outbox` is here so a sweep can DELIVER a doc it
# wrote (stage → attach) rather than only talk about it.
mcp: [buzz-cli, outbox, telegram-send]
transitions: []
---
Run the sweep, then make ONE move. Reading the tracker is not an outward act — query freely, then do a
single outward thing (or nothing).

**1 · Look** (`buzz { args: [...] }`, output is JSON):
- `["projects","list","--owner","<operator-hex>"]` — **`--owner` is required** (a bare list sees only your
  own projects). `["channels","members","--channel","<id>"]` yields the `role: "owner"` hex if you need it,
  and Hermes's pubkey for `--assignee`.
- `["issues","list","--repo-owner","<hex>","--repo-id","<repo>"]` — the queue. `["issues","get","--event","<id>"]`
  for one (note: `--event`, not `--id`).
- `["canvas","get","--channel","<project-channel>"]` — the live scope.

**2 · Act — ONE outward move**, matching what you found:
- **Advance an issue**: do the work, then `["issues","status","--issue","<id>","--status","resolved","--content","<what you did>"]`.
  An issue you finished but left `open` looks identical to one nobody touched.
- **Update the canvas**: `["canvas","set","--channel","<id>","--content","<full md>"]` — **REPLACES the whole
  document**, so always `get` first and edit what you got. `["canvas","history","--channel","<id>"]` +
  `restore` exist if you clobber something.
- **Delegate**: `["issues","create","--title","<t>","--content","<md>"]` (+ `--label`), then
  `["messages","send","--channel","<id>","--content","filed <title> — <one line>"]` so a human sees it.
  An issue has a state; a chat brief alone dies unanswered.
- **Deliver a document**: `mcp__outbox__write_attachment { name, text }` → `mcp__buzz-cli__send_file
  { attachment_id, text, channel? }`. Staging is free (not an outward act).
- **Escalate** only a blocker you can SEE in the tracker: `mcp__telegram-send__send_message { to: "holder", text }`.

Read the JSON you get back: `→ allow` is only the wall permitting the call — an `error` in the result means
it did NOT happen, and you should say so plainly rather than report success.
---task---
# Express (project sweep) — query the tracker, then one real move
projects list --owner → issues list → canvas get. Then: advance+resolve an issue · update the canvas ·
file+assign and post a pointer · deliver a doc · or nothing. One outward act; read the result.
