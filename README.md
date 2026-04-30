# MUAC AI Framework

> 📖 **Showcase entry point: [OVERVIEW.md](OVERVIEW.md)** · 🚀 **Practical on-ramp: [QUICKSTART.md](QUICKSTART.md)**

A reusable, governed pattern for AI-assisted engineering, QA, operations, safety, infrastructure, data, and documentation work. This repo is a **proposal-grade boilerplate** teams can copy and adapt — it is not a deployment, and nothing here implies any tool or vendor has been formally approved.

---

## 🗺️ Where to go next

| If you want… | Open this |
|---|---|
| The one-page architectural picture | [OVERVIEW.md](OVERVIEW.md) |
| A practical onboarding guide for a team / repo / platform admin | [QUICKSTART.md](QUICKSTART.md) |
| Connectors, Work IQ, Atlassian MCP, Team RAG, repo memory, live MCP — when to use what | [docs/connectors-and-rag-options.md](docs/connectors-and-rag-options.md) |
| How users from other teams are blocked from restricted documents | [docs/permission-model.md](docs/permission-model.md) |
| Phase-by-phase implementation plan from scratch | [docs/implementation-roadmap.md](docs/implementation-roadmap.md) |
| The repo's external-knowledge map (where each kind of question is answered) | [docs/external-knowledge.md](docs/external-knowledge.md) |
| Reusable per-repo tool policy | [docs/tool-policy.md](docs/tool-policy.md) |
| 10-minute demo script for the exploration team | [docs/demo-script.md](docs/demo-script.md) |
| The team RAG implementation pattern | [docs/team-rag-framework.md](docs/team-rag-framework.md) |
| The recommended MCP catalog and governance tiers | [docs/mcp-catalog.md](docs/mcp-catalog.md) |
| The per-repo project memory pattern | [docs/project-memory-pattern.md](docs/project-memory-pattern.md) |
| How agents, skills, prompts, MCPs, and RAG fit together | [docs/agent-skill-model.md](docs/agent-skill-model.md) |
| All the diagrams in one place | [docs/visual-architecture.md](docs/visual-architecture.md) |
| Templates a new team can copy | [templates/team-onboarding/](templates/team-onboarding/) |
| Conceptual platform skeleton (Team RAG service shape) | [templates/platform/team-rag-service/](templates/platform/team-rag-service/) |
| A worked example using SRS as the team | [examples/srs-team-rag/](examples/srs-team-rag/) |
| Conceptual local permission-trimming demo | [examples/local-permission-demo/](examples/local-permission-demo/) |

---

## 🧭 The framework eats its own dogfood

This repo also ships a working **plan-first, approval-first bootstrap workflow**. Run it inside any real target repo to derive a tailored set of agents, skills, prompts, and instructions for your objective.

The pattern is `/<prompt-name> <objective>`:

```text
/bootstrap-ai-plan-local I want UI test automation for this repo
/refine-ai-plan-local Focus the plan only on UI test automation for role-based workflows
/implement-ai-plan-local Approve and implement only the recommended repo instructions and UI testing skill
```

How it works in two sentences: the bootstrap planner reads your `docs/references/`, inspects the actual repo, and treats your stated objective as a primary input. Every candidate AI asset is evaluated against objective fit, repo evidence, document evidence, and maintenance cost — only `recommend now` items reach the final shortlist, and nothing is created without explicit approval.

Three subagents run automatically inside the planning prompts:

- `docs-reader` — summarizes `docs/references/`
- `repo-inspector` — inspects the actual repo state
- `challenger-reviewer` — critiques the draft for weak assumptions and overengineering

For the full workflow narrative — including the candidate-evaluation rubric and implementation mode — see [OVERVIEW.md](OVERVIEW.md) and [docs/agent-skill-model.md](docs/agent-skill-model.md).

---

## 🧱 Core principles

1. **Plan first.** Always produce a reviewable plan before any change.
2. **Approval first.** Nothing is created without explicit user approval in chat.
3. **Read-only first.** Tools start read-only; mutation is a separate, gated step.
4. **Permission-aware retrieval.** ACL filtering happens before the model sees chunks.
5. **Right memory in the right place.** Company → connectors. Team → RAG. Project → repo. Live → MCP.
6. **Write-back discipline.** Durable findings flow back to the right layer.
7. **Vendor-aware, not vendor-locked.** The pattern works for Claude, GitHub Copilot, M365 Copilot, and others.
8. **Evaluate every candidate AI asset against the stated objective, repo evidence, and document evidence before recommending it.**
