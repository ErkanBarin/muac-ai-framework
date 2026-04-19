# AI Planning Scaffold Instructions

## About this repository

This repository is an **AI planning and bootstrap scaffold**. It is not an application codebase. Its purpose is to help plan, scope, and prepare AI-assisted work for other projects — not to ship runtime software from here.

## Primary context source

Approved documents under [`docs/references/`](../docs/references/) are a **primary source of context** for any planning activity in this repo. Treat them as the intended description of the target system.

When reasoning about a task:
- Read the relevant reference documents first.
- Cross-check what the documents describe against the **actual repository state** (files, prompts, agents, skills that genuinely exist today).
- Call out gaps between document intent and repository reality explicitly, rather than assuming the documents are already implemented.

## Workflow: plan first, approval first

The working mode in this repository is:

1. **Plan first.** Produce a clear, reviewable plan before any change.
2. **Wait for approval.** The user must explicitly approve the plan in chat.
3. **Apply the smallest approved step.** Only after approval, and only what was approved.

Prefer the smallest useful recommendation set. Do not propose extra agents, skills, prompts, or tooling unless there is a clear gap supported by the documents and the repository.

### Do not create or modify files without explicit approval

This is the most important rule:

> **Do not create, edit, rename, move, or delete any file in this repository unless the user has explicitly approved that specific change in the current chat.**

Planning, analysis, and suggestions are always allowed. File changes are not, until approval is given in this conversation.

## Entry points

For structured planning sessions, use the existing prompt entry points:

- [`bootstrap-ai-plan-local.prompt.md`](prompts/bootstrap-ai-plan-local.prompt.md) — initial bootstrap planning for a new target.
- [`refine-ai-plan-local.prompt.md`](prompts/refine-ai-plan-local.prompt.md) — refinement of an existing plan.
- [`implement-ai-plan-local.prompt.md`](prompts/implement-ai-plan-local.prompt.md) — applies only explicitly approved AI scaffold items.

Start from these prompts rather than improvising a new planning flow.
