---
mode: agent
description: Analyze local reference docs and the target repository, then produce a detailed AI enablement plan
model: GPT-5
tools: ["changes", "codebase", "fetch", "findTestFiles", "githubRepo", "problems", "search", "searchResults", "usages"]
---

Use the `bootstrap-planner` agent.

Goal:
Analyze the approved project reference documents under `docs/references/` and the current repository state.
Do not create or modify files yet.
Produce a detailed AI enablement plan only.

Required behavior:
1. Read all useful documents under `docs/references/`
2. Inspect the current repository structure, codebase, tooling, tests, docs, and conventions
3. Infer:
   - what system this repository supports
   - what stack and tools are being used
   - what engineering maturity level the repo appears to have
   - what gaps or improvement opportunities are visible
4. Recommend the minimum useful AI setup for this repository
5. Prefer a small, practical setup over a large or overengineered one
6. Do not generate files yet
7. End by asking for approval before implementation

Your output must contain these sections:

# Project Understanding
- What the system appears to do
- Which documents were used
- Confidence level
- Unknowns or ambiguities

# Repository Assessment
- Languages and frameworks
- Test tooling
- CI/CD and quality setup
- Existing documentation
- Existing organization and conventions
- Engineering maturity observations

# Recommended AI Assets
For each recommendation, state:
- asset type
- proposed name
- purpose
- why it is needed
- priority: high / medium / low

Possible asset types:
- repository-wide copilot instructions
- path-specific instruction files
- project-specific skills
- project-specific agents
- approved tool or MCP integration guidance
- reusable prompt files

# What Not To Add Yet
State clearly what should be deferred and why.

# Risks and Assumptions
List the main assumptions, risks, and possible incorrect conclusions.

# Approval Options
Offer these choices:
- approve plan only
- approve and implement all
- approve and implement selected items
- revise the plan first
