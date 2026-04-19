# MUAC AI Framework

This repository provides a bootstrap AI planning flow for engineering teams.

Current mode:
- Teams place approved project documents under `docs/references/`
- A bootstrap planner agent reads those documents and the target project repository
- The agent produces a detailed AI enablement plan
- The plan can then be reviewed, challenged, approved, and optionally implemented

Future mode:
- When WorkIQ is available, the same framework can use approved enterprise sources such as Confluence or Microsoft 365 content instead of or in addition to local reference documents

Initial goal:
- Plan first
- Do not modify project repositories automatically
- Ask for approval before any implementation
