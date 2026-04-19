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
4. Re-check the user's stated objective from the current chat context and ensure the plan remains aligned with it
5. Tighten the plan by removing weakly justified recommendations, reducing unnecessary complexity, and improving clarity
6. Remove or defer any item that shows **any** of the following:
   - weak objective fit
   - weak repo evidence
   - weak document evidence
   - high maintenance cost without strong value
7. Remove recommendations that do not clearly support the stated objective
8. Keep the smallest useful setup for that specific purpose
9. Preserve anything that is strongly justified by the documents, the repo evidence, or the stated objective
10. End by asking for approval before implementation

Your output must contain these sections:

# Refined Project Understanding
- Updated understanding of the system
- Any changed assumptions
- Confidence level

# Refined Repository Assessment
- Any important repo observations affecting the plan
- Any corrections to the earlier assessment

# Candidate Asset Evaluation
Re-evaluate the surviving or debated items from the prior plan. For each, state:
- asset type
- proposed name
- objective fit
- repo evidence
- doc evidence
- maintenance cost: low / medium / high
- decision: keep / defer / reject
- rationale

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
