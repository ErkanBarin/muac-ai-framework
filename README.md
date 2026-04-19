# MUAC AI Framework

This repository provides a bootstrap AI planning flow for engineering teams.

## Current mode
- Teams place approved project documents under `docs/references/`
- The bootstrap planner analyzes those documents and the target project repository
- The agent produces a detailed AI enablement plan
- The plan can then be reviewed, challenged, approved, and optionally implemented

## Quick Start
1. Place approved project documents under `docs/references/`.
2. Run the `/bootstrap-ai-plan-local` prompt to generate a draft AI enablement plan.
3. (Optional) Run `/refine-ai-plan-local` to tighten the plan further.
4. Review the plan. Approve implementation only if you are satisfied — nothing is created or modified without explicit approval.

> **Note:** This framework is best used against a *separate target repository* containing real application code. Pointing it at this scaffold itself will mostly describe the scaffold.

## What runs under the hood
The `/bootstrap-ai-plan-local` and `/refine-ai-plan-local` prompts both delegate to the `bootstrap-planner` agent, which internally orchestrates three subagents:
- `docs-reader` — summarizes approved documents under `docs/references/`
- `repo-inspector` — inspects the actual repository state
- `challenger-reviewer` — critiques the draft plan for weak assumptions and overengineering before it is shown for approval

You do not invoke these subagents directly; they run as part of the planning flow.

## Future mode
When WorkIQ is available, the same framework can use approved enterprise sources such as Confluence or Microsoft 365 content instead of or in addition to local reference documents.

## Principles
- Plan first
- Do not modify project repositories automatically
- Ask for approval before any implementation
