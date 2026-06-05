# [Spec-Driven Development with Coding Agents](https://www.deeplearning.ai/courses/spec-driven-development-with-coding-agents)

## Overview

- SDD is to coding agents what compilers are to programming languages: it helps agents turn Markdown requirements into source code.
- Specs preserve the non-negotiables across sessions, preventing context decay as the codebase grows.

## Project Constitution

The foundation of an SDD project — let the agent interview you to generate it.

- **Mission** — the project's vision, target audience, and scope (the *why*)
- **Tech stack** — for the engineering team
- **Roadmap** — a living product document; specifies *what* to build, not *how*

The constitution may be revised between feature phases, but never during one.

## Feature Implementation

Each feature is taken from the roadmap. Before implementation, ask the agent to prepare:

- `plan.md` — implementation plan
- `requirements.md` — feature requirements
- `validation.md` — how to validate the implementation

Then let the agent implement and verify the result. If changes are needed, ask the agent to update the plan first, then implement the corresponding code changes.

> **Open questions:**
> - Is the spec reviewed by the team before implementation begins? If so, how is a late-phase spec update handled?
> - Risk: specs may end up as detailed as source code, making them hard to maintain.

## Keeping Specs in Sync

- Manually refactoring code without updating specs leads to drift.
- **Replanning** (done *outside* feature phases) is needed when a structural change affects multiple specs simultaneously (e.g., a new tech stack dependency, or introducing responsive design).
- Goal: specs capture decisions, not just code. Keep both in sync.

## Brownfield Projects

For existing codebases, ask the agent to infer the constitution from the codebase as a starting point.

## SDD Frameworks

SDD frameworks automate repeated steps (requirement gathering, spec refinement, implementation planning, validation) using skills.

Conventions:
- Store task specs as files (`plan.md`, `requirements.md`, `validation.md`)
- Store research findings in `backlog/` (a convention from this course); link to them from the roadmap when scheduling future work

## Workflow Enhancements

- **CLI + Skills > MCP** is gaining traction as an approach to workflow customization — less setup, lower context usage.
- **ACP (Agent Client Protocol)** — an open standard (analogous to LSP) that connects code editors with coding agents, decoupling them so both can evolve independently.
