# 🔍 Local Permission-Trimming Demo

A **conceptual** local demonstration of how ACL trimming works in a Team RAG setup.

> ⚠️ **This is a markdown/JSON walkthrough, not a real RAG service.** It uses tiny placeholder content to make the permission model visible. Nothing here is a deployment, an ingestion pipeline, or an Azure AI Search query.

---

## What this demo shows

The same query — *"How does the SRS subsystem send acknowledgement messages to its peer system?"* — is "asked" by four users in four different Entra group sets. The demo shows:

1. The **same** index ([`sample-index.json`](sample-index.json)) is searched.
2. The **same** query is used ([`sample-queries.md`](sample-queries.md)).
3. **Different** allowed chunks are returned per user ([`expected-results.md`](expected-results.md)).
4. **L4 / secret** content is never returned to anyone — by design, it isn't even in the index.

---

## Files

| File | Purpose |
|---|---|
| [`sample-index.json`](sample-index.json) | Tiny placeholder Team RAG index. Each chunk has a sensitivity level and `allowedGroups`. |
| [`sample-queries.md`](sample-queries.md) | The query, the four user profiles, and how the ACL filter is applied conceptually. |
| [`expected-results.md`](expected-results.md) | What each user **does** and **does not** see. The teaching artifact. |

---

## How to "run" the demo

Read the files in this order:

1. **`sample-index.json`** — see the four placeholder chunks and their `allowedGroups`.
2. **`sample-queries.md`** — see the four user profiles and the ACL filter pseudocode.
3. **`expected-results.md`** — see exactly which chunks each user receives.

That's the whole demo. There is no runtime.

---

## The point of the demo

In a real Team RAG setup:

- Chunks live in **Azure AI Search**.
- ACL filtering happens **before** chunks reach the model.
- The user's Entra groups are resolved at retrieval time.
- The model **never** sees chunks the user is not allowed to see — so it cannot leak them by paraphrase.

This local demo is a **stand-in** for that flow, expressed in JSON and markdown so a reviewer can see the trimming behavior without spinning up infrastructure.

---

## Demo groups

| Group | Stands for |
|---|---|
| `ALL-MUAC` | Everyone in the org |
| `SRS-Engineering` | SRS engineering staff |
| `SRS-QA` | SRS QA engineers |
| `SRS-Architecture` | SRS architecture / interface owners |
| `OTHER-TEAM` | A developer from a different team |

In a real deployment these would be Entra ID groups managed via the standard IAM workflow.

---

## What this demo is **not**

- ❌ Not a runnable RAG service.
- ❌ Not a real Azure AI Search index.
- ❌ Not a substitute for permission tests against a real index.
- ❌ Not an approved data structure for production indexes.
- ❌ Not real SRS content — every chunk is a placeholder.

For the real Team RAG build pattern, see [`docs/team-rag-framework.md`](../../docs/team-rag-framework.md). For the SRS worked example, see [`examples/srs-team-rag/`](../srs-team-rag/).

---

## Related pages

- [Permission model](../../docs/permission-model.md)
- [Team RAG framework](../../docs/team-rag-framework.md)
- [Connectors & RAG options](../../docs/connectors-and-rag-options.md)
- [SRS worked example](../srs-team-rag/)
