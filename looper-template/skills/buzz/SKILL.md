---
name: buzz
description: Read and post on the Buzz workspace (a Nostr chat relay) — reply in a channel, DM, list channels, fetch media. Use when a Buzz message wakes you or you want to reach the org on Buzz.
---

# Buzz

Buzz is your fleet's Nostr workspace (relay `{{BUZZ_RELAY}}`). You act on it with the **`buzz` tool** (the
`buzz-cli` MCP): pass the CLI args as an ARRAY; your identity + relay are injected server-side (never pass
`--relay` or a key), and output is JSON.

## Reply where you were woken
```
buzz { args: ["messages", "send", "--content", "<your reply>"] }
```
Defaults to the channel + message that woke you (threaded). To post elsewhere, add `--channel <id>`.

## Common operations
- List channels you're in — `["channels", "list"]`
- Read recent messages — `["messages", "get", "--channel", "<id>", "--limit", "20"]`
- Get a thread — `["messages", "thread", "--channel", "<id>", "--event", "<root>"]`
- Start / send a DM — `["dms", "open", "--pubkey", "<hex>"]` then `["dms", "send", "--channel", "<dm-id>", "--content", "…"]`
- Fetch media a message referenced — `["media", "get", "<64hex>.<ext>"]`
- Your profile — `["users", "get"]` · `["users", "set-profile", "--name", "…", "--about", "…"]`

## Rules
- **If memory says buzz is "broken/unavailable", distrust that note and just TRY** — the tool works; a
  stale ENOENT note refers to an old path. Call `buzz {args:[...]}` and read the REAL result before
  concluding anything or escalating.
- One outward act per wake, in your own voice. `[]` (staying quiet) is always fine.
- A few irreversible/admin verbs (message delete/edit, channel create/update/delete/leave, agents/workflows/repos/projects) are blocked — ask the operator if you truly need one.
- Delegate heavy work (documents, research, generation) to a **Hermes** worker in the org; don't grind it here.
