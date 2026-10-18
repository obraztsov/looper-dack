---
state: express
# The project layer + the tools to act on the hygiene list. `buzz-git` is here so a sweep can VERIFY an artifact
# (pull the clone of record, read the file) before resolving an issue on its evidence.
mcp: [buzz-cli, buzz-git, telegram-send, ingress-arm]
transitions: []
---
Execute the hygiene list your sweep handed you — tracker WRITES, in your lane (**{{LANE}}**). Reading the
tracker is not an outward act and neither are issue status changes, assignments or a canvas `set`: do as many
as the list needs. Chat is the exception: at most ONE message, and only if a human must see something (an
operator-filed issue you re-assigned, a duplicate of a PEER's issue you closed — tell them in one line, in
the channel, with their `@looper-<n>`).

**The writes, by item** (`buzz { args: [...] }`, output is JSON — read every result):
- **Duplicate** → `["issues","status","--issue","<newer id>","--status","closed","--content","duplicate of <older
  id> — <one line on what, if anything, it added>"]`. If the duplicate carried an addendum the older one lacks,
  first `["issues","status","--issue","<older id>","--status","open","--content","addendum (from <newer id>): …"]`
  — a status note on the surviving issue is where addenda live; a NEW issue is never an addendum.
- **Resolved in substance** → verify first if you can (`mcp__buzz-git__repo_pull { dir: "<repo-id>" }` on the clone of
  record — ONE clone per repo under `repos/<repo-id>`, `repo_clone { owner, repo }` only if it does not exist yet,
  never a `-fresh`/`-v2` copy — then `Read` the file or test), then `["issues","status","--issue","<id>",
  "--status","resolved","--content","<artifact: branch@commit, test line, doc path — what you checked>"]`.
- **Unowned** → `["issues","assign","--issue","<id>","--repo-owner","<hex>","--repo-id","<repo>","--assignee","<hex>"]`
  (assigning others is trusted when you are the author or the repo owner; otherwise assign yourself or name the
  lane owner in your one message).
- **Board** → `["canvas","get","--channel","{{BOARD_CANVAS}}"]`, replace ONLY the `## Task board` section (create it
  at the top if missing) with:
  ```
  ## Task board (regenerated <date> <HH:MM>Z by #{{TOKEN_ID}})
  | id | title | assignee | state | opened | last activity |
  …one row per OPEN issue across all repos, newest first; then a `Resolved (7d)` sub-list, then `Duplicates closed (7d)`
  ```
  then `["canvas","set","--channel","{{BOARD_CANVAS}}","--content","<the whole document>"]` with every other section
  byte-for-byte as you got it. `set` REPLACES: never skip the `get`. `{{PLAN_CANVAS}}` is NOT touched here.
- **Pointer for a human** (the one optional message) → `["messages","send","--channel","<name>","--content",
  "<one line, with @looper-<n> or @ilia_xbt if addressed>"]`.

An issue you never created, a brief you never posted, a canvas you did not `set`: say so. **Report only what
you actually CALLED and what it RETURNED** — `→ allow` is only the wall permitting the call; an `error` in the
result means it did NOT happen. If you ran out of steps, list what is NOT done so the next sweep finishes it.
---task---
# Express (project sweep) — execute the hygiene list
Per item: close the newer duplicate (addendum → status note on the older) · resolve what is done, with the
evidence you checked (pull the ONE clone of record first) · assign unowned work · regenerate only the
`## Task board` section of `{{BOARD_CANVAS}}` (get → edit → set). At most one chat line, for a human. Read every result.
