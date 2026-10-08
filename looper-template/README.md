# looper-template

The **operational layer** of a Looper soul: state prompts, duties (stimuli), and the Buzz skill doc —
forked from the public [dack soul template](https://github.com/obraztsov/dack-soul) and shaped for a
looper (multi-agent org etiquette, split public/org rails, Buzz + Telegram, proactive heartbeat).

`research/compose_soul.py` copies this tree into a composed soul and fills in per-looper values. The
**persona** layer (`SOUL.md`, `memory/soul/*`) is generated from the NFT's on-chain codex instead — that
part is computed, not templated.

## Why this is a fork

The composer used to copy upstream files and then patch them with ~23 `.replace()` / `re.sub()` calls.
Every teaching change meant editing Python that rewrote Markdown, and when a target string drifted the
patch silently did nothing — which is how a bare-vs-fully-qualified tool name and a stale `--reply-to`
default both shipped to production. These are now plain files you edit directly.

## Template syntax

Two constructs, both resolved at compose time:

| form | meaning |
|---|---|
| `{{TOKEN_ID}}` `{{HANDLE}}` `{{AGENT_CLASS}}` `{{BUZZ_RELAY}}` | substituted per looper |
| `{{#if bash_skills}}…{{/if}}` | kept when the flag is on, **removed** when off |

Conditionals are deliberately **opt-out**: the template carries the maximal text and a disabled flag
*removes* its block. A removal marker is visible in the file and can't silently fail the way an
insert-on-match `.replace()` did. It works inline too —
`transitions: [telegram/express{{#if bash_skills}}, settle{{/if}}]`.

Keep one newline *outside* a block that ends a paragraph, so the separator survives removal:

```
…end of prose.
{{#if bash_skills}}
**Extra note only for bash-skills loopers.**
{{/if}}---task---
```

## Layout

```
prompts/            state prompts — perceive/express per rail, digest, reflect, heartbeat
  telegram/         public rail (perceive→express) + org rail (org-perceive→org-express)
  buzz/             same split for Buzz
  heartbeat/        self-driven initiative (multi-baton fan-out)
stimuli/            duties: telegram-op/pub/trusted, buzz-org/pub, heartbeat, social-digest
skills/buzz/        SKILL.md — prose reference for the `buzz-cli` MCP
optional/
  bash_skills/      copied ONLY with --bash-skills (adds prompts/settle.md)
```

## Editing rules

- **Tool names are always fully qualified** (`mcp__telegram__reply_document`, not `reply_document`).
  Tools are *deferred*: the model must `ToolSearch` an exact `mcp__server__tool` name, and orientation
  announces only `mcp__server__*` globs — a bare name gives it nothing to look up.
- **Teach a capability in the state where the PLAN is formed.** Egress tools never appear in Perceive, so
  a Perceive rail must say what the express rail can do or the duck will plan around its absence (it once
  told its operator "send tools aren't loaded" while holding them).
- `mcp:` in frontmatter only *requests* capability — the operator's `tier_policy` admits it. Widening a
  prompt's list does nothing on its own.
- After editing, recompose and diff: `compose_soul.py <id> …` then compare against the previous output.
  A prompt change should move exactly the lines you intended.

## Staying in sync with upstream

This fork tracks `dack-soul` loosely. Files taken from upstream and then diverged: the telegram rails,
`prompts/{perceive,express,reflect}.md`, `prompts/digest/*`, and the `telegram-*` stimuli. When upstream
improves the harness contract (context blocks, baton shape, runlog tools), diff those files and port the
change by hand. `memory/harness/*` is deliberately **not** forked — the composer still takes it from the
engine template, because it documents the runtime rather than the looper.
