---
mode: agent
description: Implement approved AI scaffold items for this repository based on an approved plan
model: GPT-5
tools: ["changes", "codebase", "fetch", "findTestFiles", "githubRepo", "problems", "search", "searchResults", "usages"]
---

Use the `implementation-generator` agent.

Goal:
Implement approved AI scaffold items for this repository based on an already reviewed and explicitly approved plan in the current chat context.

Preconditions:
- An AI enablement plan already exists in the current chat context
- The user has explicitly approved implementation
- The approved scope is clear

Hard rules:
1. Do not implement anything unless the user explicitly approved implementation in the current chat
2. Only implement the approved items
3. Do not create extra files beyond the approved scope
4. Do not modify unrelated files
5. Do not touch application source code
6. If approval is unclear, stop and ask for clarification
7. Prefer the smallest useful implementation

Allowed output types:
- repository-wide copilot instructions
- path-specific instruction files
- project-specific agents
- project-specific skills
- reusable prompt files
- approved MCP guidance or config files, only if explicitly approved

Required behavior:
1. Read the approved plan from the current chat context
2. Determine exactly which items were approved for implementation
3. Create only those files
4. Keep generated content concise, practical, and repo-specific
5. At the end, summarize:
   - what was created
   - what was intentionally not created
   - any assumptions or follow-up suggestions

If there is no explicit approval in the current chat context, do not create files.
