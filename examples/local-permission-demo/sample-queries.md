# Sample Queries — Local Permission-Trimming Demo

The same query, asked by four different users, against the same conceptual index ([`sample-index.json`](sample-index.json)).

---

## The query

> *"How does the SRS subsystem send acknowledgement messages to its peer system?"*

---

## The four users

| User | Entra groups | Sensitivity ceiling |
|---|---|---|
| **U1** General developer | `ALL-MUAC` | L0 only |
| **U2** SRS QA engineer | `ALL-MUAC`, `SRS-Engineering`, `SRS-QA` | L1 / L2 (where their groups are listed) |
| **U3** SRS architect | `ALL-MUAC`, `SRS-Engineering`, `SRS-Architecture` | L1 / L2 (where their groups are listed) |
| **U4** Out-of-team developer | `ALL-MUAC`, `OTHER-TEAM` | L0 only |

None of the four users belong to `SRS-Restricted-Access` in this demo, so none of them sees the L3 chunk. This is intentional: the demo shows that even an SRS architect has a clear access wall around L3 / sensitive content unless they're also in the restricted group.

---

## The conceptual ACL filter

In a real Team RAG MCP, the filter is an Azure AI Search query restricted by `allowedGroups`. For this demo, the filter is the following pseudocode:

```text
def visible_chunks(index, user_groups):
    return [
        chunk for chunk in index.chunks
        if any(group in chunk.allowedGroups for group in user_groups)
    ]
```

A chunk is returned **only if** the user's group set intersects the chunk's `allowedGroups`. Otherwise the chunk is silently dropped — the model never sees it.

L4 content is not in the index at all, so no filter logic applies — it simply doesn't exist in the retrieval path.

---

## Why "silently dropped"

The drop is **silent on purpose**:

- The model can't paraphrase what it never received.
- The user can't infer the existence of restricted content from a "denied for chunk-003" message (unless the platform's policy explicitly allows that signal).
- The audit log still records the drop for governance purposes.

If the policy allows surfacing the existence of restricted content (e.g. "5 additional results are restricted — request access via `<group>`"), that's a deliberate platform-level decision, not a default. See [`docs/permission-model.md`](../../docs/permission-model.md).

---

## What happens if no chunks survive

The assistant **does not**:

- Retry without the filter.
- Answer from training data as if it had retrieved.
- Summarize a restricted chunk it never received.

The assistant **does**:

- Return *"no accessible result"* (or *"no result"*, per platform policy).
- Surface the access-request path: *"If you believe you should have access, request membership in `<group>` via the standard IAM workflow."*

For the expected per-user results, see [`expected-results.md`](expected-results.md).
