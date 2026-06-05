# [Introduction to agent skills](https://anthropic.skilljar.com/introduction-to-agent-skills)

## Skill Descriptions

Every skill's `description` field should answer:
- What does the skill do?
- When should Claude use it?

Example:
> Summarizes uncommitted changes. Use when the user asks what changed or wants a commit message.

## Progressive Disclosure

- Supporting files (e.g., `references/`) are only loaded when needed — not on every invocation.
- Scripts in `scripts/` are executed but never read into context — only their output is injected.

Keep `SKILL.md` lean and offload the rest.

## Subagents and Skills

Subagents start with isolated context and don't automatically have access to your skills. A subagent **can** discover and invoke them at runtime via the Skill tool.

The following prompt **will** work:
> Run a subagent to `/dripify:backend-review` these changes.

To preload a skill into the subagent's context from the start, declare it in the subagent's frontmatter:

```yaml
---
name: backend-reviewer
skills:
  - dripify:backend-review
---
```
