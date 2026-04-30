# Expected Results — Local Permission-Trimming Demo

For the query *"How does the SRS subsystem send acknowledgement messages to its peer system?"* run against [`sample-index.json`](sample-index.json), here is what each user **does** and **does not** see.

---

## Index recap

| Chunk | Title | Classification | `allowedGroups` |
|---|---|---|---|
| `chunk-001` | SRS Overview | L0 general | `ALL-MUAC` |
| `chunk-002` | SRS Team-Internal Flow | L1 team-internal | `SRS-Engineering`, `SRS-QA`, `SRS-Architecture` |
| `chunk-003` | SRS Restricted Interface (ICD) | L2 restricted | `SRS-Architecture`, `SRS-Engineering` |
| `chunk-004` | SRS Security-Sensitive Note | L3 sensitive | `SRS-Restricted-Access` |
| *(L4 secret content)* | *(not in index)* | L4 | *(no AI access by default)* |

---

## U1 — General developer (`ALL-MUAC`)

**Allowed chunks:** `chunk-001` (overview)

**Filtered out:**
- `chunk-002` — user not in any SRS team group
- `chunk-003` — user not in any SRS team group
- `chunk-004` — user not in `SRS-Restricted-Access`

**Assistant answer (conceptual):**
> *Based on the public SRS overview, the SRS subsystem produces acknowledgement messages to a peer system as part of its normal operation. (citation: SRS Overview)*
>
> *I don't have access to the team-internal flow notes or the interface contract. If you need those details, please request membership in `SRS-Engineering` via the standard IAM workflow.*

---

## U2 — SRS QA engineer (`ALL-MUAC`, `SRS-Engineering`, `SRS-QA`)

**Allowed chunks:** `chunk-001`, `chunk-002`, `chunk-003`

**Filtered out:**
- `chunk-004` — user not in `SRS-Restricted-Access`

**Assistant answer (conceptual):**
> *The SRS subsystem sends acknowledgement messages over the SRS-PEER-ACK interface (citation: SRS Restricted ICD). Team-internal testability notes describe the integration lane that verifies ack behavior (citation: SRS Team-Internal Flow). For a high-level summary, see the SRS overview (citation: SRS Overview).*
>
> *I don't have access to the security-sensitive analysis for this flow. If your work requires it, please request membership in `SRS-Restricted-Access`.*

This is the typical "SRS QA can retrieve testability notes" path the framework is designed for.

---

## U3 — SRS architect (`ALL-MUAC`, `SRS-Engineering`, `SRS-Architecture`)

**Allowed chunks:** `chunk-001`, `chunk-002`, `chunk-003`

**Filtered out:**
- `chunk-004` — user not in `SRS-Restricted-Access`

**Assistant answer (conceptual):**
> *The SRS subsystem sends acknowledgement messages over the SRS-PEER-ACK interface (citation: SRS Restricted ICD), with the message contract defined in the ICD. Team-internal flow notes describe how the ack is produced and verified (citation: SRS Team-Internal Flow). High-level: see the SRS overview (citation: SRS Overview).*

This is the typical "SRS architect can retrieve restricted interface docs" path. Note: even an SRS architect doesn't see L3 content unless they're explicitly in the restricted group — having `SRS-Architecture` is **not** the same as having `SRS-Restricted-Access`.

---

## U4 — Out-of-team developer (`ALL-MUAC`, `OTHER-TEAM`)

**Allowed chunks:** `chunk-001` (overview)

**Filtered out:**
- `chunk-002` — user not in any SRS team group
- `chunk-003` — user not in any SRS team group
- `chunk-004` — user not in `SRS-Restricted-Access`

**Assistant answer (conceptual):**
> *Based on the public SRS overview, the SRS subsystem produces acknowledgement messages to a peer system as part of its normal operation. (citation: SRS Overview)*
>
> *I don't have access to SRS team-internal documents or interface contracts. If you need those details for cross-team work, please request membership in the relevant SRS group via the standard IAM workflow.*

This is the typical "non-SRS developer only gets general docs or no result" path.

---

## What happens with L4 content

L4 content is **not in the index**. There is no filter to apply, no chunk to drop, no metadata to leak. The assistant simply has nothing to retrieve, and any L4-shaped question gets routed to the approved secure process — not to the AI assistant.

This is the meaning of *"no AI access by default"* for L4.

---

## Summary table

| User | `chunk-001` (L0) | `chunk-002` (L1) | `chunk-003` (L2) | `chunk-004` (L3) | L4 |
|---|---|---|---|---|---|
| **U1** General developer | ✅ | ❌ | ❌ | ❌ | not in index |
| **U2** SRS-QA | ✅ | ✅ | ✅ | ❌ | not in index |
| **U3** SRS-Architecture | ✅ | ✅ | ✅ | ❌ | not in index |
| **U4** OTHER-TEAM | ✅ | ❌ | ❌ | ❌ | not in index |

Three points to draw from this:

1. **Same query, different users, different allowed results.** That's the whole point.
2. **The model never sees the dropped chunks.** It cannot leak by paraphrase what it never received.
3. **L4 / secret content is not protected by ACL filtering — it's protected by not being in the index at all.**

---

## What this means in production

- A real Azure AI Search query uses an `allowedGroups/any(g: search.in(g, '<user-group-1>,<user-group-2>...'))` style filter applied **before** retrieval.
- The chunk-level `allowedGroups` is set at ingestion time from the source ACL.
- An ACL refresh job propagates Entra group membership changes to the next retrieval window.
- Audit logs record: query, user, group set, chunks considered, chunks returned, drops by ACL.

For the build pattern, see [`docs/team-rag-framework.md`](../../docs/team-rag-framework.md). For the central permission model, see [`docs/permission-model.md`](../../docs/permission-model.md).
