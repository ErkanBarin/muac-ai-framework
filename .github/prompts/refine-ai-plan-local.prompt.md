---
mode: agent
description: Refine an existing AI enablement plan using local docs and current repository reality
model: GPT-5
tools: ["changes", "codebase", "fetch", "findTestFiles", "githubRepo", "problems", "search", "searchResults", "usages"]
---

Use the `bootstrap-planner` agent.

Goal:
Refine an existing AI enablement plan for this repository.
Do not create or modify files yet.
Do not implement anything.
Produce a revised plan only.

Instructions:
1. Read the current approved documents under `docs/references/`
2. Reinspect the current repository state as needed
3. Review the existing draft AI enablement plan provided in the current chat context
4. Tighten the plan by removing weakly justified recommendations, reducing unnecessary complexity, and improving clarity
5. Keep the smallest useful setup
6. Preserve anything that is strongly justified by either documents or repo evidence
7. End by asking for approval before implementation

Your output must contain these sections:

# Refined Project Understanding
- Updated understanding of the system
- Any changed assumptions
- Confidence level

# Refined Repository Assessment
- Any important repo observations affecting the plan
- Any corrections to the earlier assessment

# Refined Recommended AI Assets
For each recommendation, state:
- asset type
- proposed name
- purpose
- why it is still needed
- priority: high / medium / low

# Removed or Deferred Items
- What was removed, simplified, or postponed
- Why

# Risks and Assumptions
- Remaining uncertainties
- Risks that still need human review

# Approval Options
Offer these choices:
- approve refined plan only
- approve and implement all
- approve and implement selected items
- revise again
