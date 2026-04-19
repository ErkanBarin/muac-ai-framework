---
mode: agent
description: Analyze local reference docs and the target repository, then produce a detailed AI enablement plan
model: GPT-5
tools: ["changes", "codebase", "fetch", "findTestFiles", "githubRepo", "problems", "search", "searchResults", "usages"]
---

Use the `bootstrap-planner` agent.

Goal:
Analyze the approved project reference documents under `docs/references/` and the current repository state, in light of the user's stated objective.
Do not create or modify files yet.
Produce a detailed AI enablement plan only.

User objective:
Before planning, the user must state what they want this framework to help with, in the current chat context. The planner must treat this objective as a primary input alongside the documents and the repository state.

Examples of objectives you can specify:
- UI test automation
- backend/API testing
- feature implementation support
- repository hygiene and conventions
- pull request review
- debugging flaky tests
- documentation analysis

If no objective is stated, ask for one before producing the plan.

Required behavior:
1. Read all useful documents under `docs/references/`
2. Inspect the current repository structure, codebase, tooling, tests, docs, and conventions
3. Consider the user's stated objective from the current chat context
4. Infer:
   - what system this repository supports
   - what stack and tools are being used
   - what engineering maturity level the repo appears to have
   - what gaps or improvement opportunities are visible that are relevant to the stated objective
5. Evaluate every candidate AI asset (instructions, skills, agents, prompts, approved MCP guidance) against the stated objective, repo evidence, and document evidence before recommending any
6. Recommend only the strongest low-noise items as the final shortlist that supports the stated objective
7. Prefer a small, practical setup over a large or overengineered one
8. Do not generate files yet
9. End by asking for approval before implementation

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

# Candidate Asset Evaluation
Evaluate every candidate instruction, skill, agent, prompt, and approved MCP guidance/config item **before** committing to a final recommendation. For each candidate, state:
- asset type
- proposed name
- objective fit
- repo evidence
- doc evidence
- maintenance cost: low / medium / high
- decision: recommend now / defer / reject
- rationale

# Recommended AI Assets
This section is the **final shortlist** — include only candidates whose decision above was `recommend now`. Do not reintroduce items that were deferred or rejected. For each recommendation, state:
- asset type
- proposed name
- purpose
- how it supports the stated objective
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
