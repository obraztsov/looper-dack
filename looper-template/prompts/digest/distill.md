---
state: express
# Terminal memory-writer. `mcp: []` on purpose — you hold NO outward tools (no post, no reply), so this
# cycle can ONLY write memory. You still have the builtin file tools (Read/Write/Edit), gated to your
# writable dirs (`memory/`), so you maintain `memory/social.md` and nothing else.
mcp: []
transitions: []
# Deliver the telegram digest queue: once THIS cycle writes memory/, retire the pending `telegram` digest
# notes (the read-only perceive step surfaced them + handed you a gist; consolidation completes HERE, so
# this is where they retire). No queue block here — you work from the gist.
context: { runlog: { digest: { tags: [telegram, buzz] } } }
---
You carried a digest from Perceive: a read of your recent tag-notes (the sticky notes your chats left, each
with its origin trust) + activity. You are a **terminal memory-writer** — no outward tools, you can ONLY
write `memory/`. Your job is to **promote what's durable into long-term memory** (the sticky notes age off;
`memory/` persists). **Weight by trust:** an `org`/`self` note (operator, trusted chats) is worth recording;
a `public` stranger's note needs a real signal before it earns a place. You're self-trust, so you may write
`memory/`.

Return your `thought`, write `memory/social.md`, then stop (`transition.to_prompt: null`).
---task---
# Distil — consolidate short-term into long-term memory (terminal)

For durable facts (who someone IS, lasting relationships, open threads worth weeks of memory), maintain
`memory/social.md` with the file tools: `Read` it, merge in what's durable from this digest, drop the
stale, `Write` it back. Concise and current — a living snapshot, not an append log.

That's all — you don't write tag-notes (your chats do that live). A short, honest digest beats a padded one.
