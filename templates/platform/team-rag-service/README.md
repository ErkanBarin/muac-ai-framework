# 🧬 Team RAG Service — Conceptual API/MCP Skeleton

A **conceptual** template for the platform team's reusable Team RAG service. Each team plugs in its own sources, metadata, and ACLs; the platform team owns the service.

> ⚠️ **This is a design sketch, not implementation code.** It defines the shape of the API/MCP surface the platform team would build once and reuse across teams. Real implementations live elsewhere — Azure AI Search, ingestion workers, MCP server frameworks — and should follow approved platform standards.

---

## What the service is

A **single platform-owned Team RAG service**, exposed as either:

- An **MCP server** to AI assistants (Claude Code via MS Foundry, GitHub Copilot Enterprise, Microsoft 365 Copilot via approved bridges), or
- An **HTTP API** for orchestrators / Foundry agents.

Both surfaces wrap the same logic. The MCP surface is the recommended primary entry point.

---

## What the service is **not**

- ❌ Not a per-team service. The platform team builds it **once** and parameterizes it.
- ❌ Not a write tool. Writes happen by re-ingesting source documents — not by calling this service.
- ❌ Not an ACL bypass. ACL filtering is **always** applied; there is no "admin mode" that disables it.
- ❌ Not a replacement for live MCP tools. Live system facts (DB, logs, CI, Azure) use their own tools, not this service.

---

## Conceptual operations

### `search_team_docs(query, filters)`

> Free-form semantic + keyword search over a team's index, ACL-filtered to the calling user.

**Inputs:**

| Field | Type | Required | Notes |
|---|---|---|---|
| `query` | string | yes | The user's natural-language query |
| `filters.team` | string | yes | The team index to search (e.g. `SRS`) |
| `filters.docType` | string\[\] | no | e.g. `["ICD", "Architecture"]` |
| `filters.classification` | string\[\] | no | e.g. `["L1-team-internal", "L2-restricted"]` |
| `filters.feature` | string | no | Free-form feature label |
| `filters.requirementId` | string | no | Exact match against requirement metadata |
| `filters.interface` | string | no | Exact match against interface metadata |
| `filters.maxResults` | integer | no | Default platform-decided |

**Behavior:**
1. Authenticate caller (Entra ID).
2. Resolve caller's groups.
3. Build the search query against the team's Azure AI Search index with the per-user `allowedGroups` filter.
4. Return only chunks the user is allowed to see, with citations.
5. Audit-log the call (caller, query, filter set, hit count, drop count).

**Returns:**
```text
[
  { chunkId, docTitle, sourceUrl, classification, snippet, score, metadata: { ... } },
  ...
]
```

---

### `get_doc_summary(sourceUrl)`

> A short, ACL-filtered summary of the indexed chunks for a single source document.

**Inputs:**

| Field | Type | Required | Notes |
|---|---|---|---|
| `sourceUrl` | string | yes | The authoritative source URL of the document |

**Behavior:**
1. Authenticate + resolve groups.
2. Retrieve indexed chunks for that document.
3. Apply ACL filter.
4. If no chunks survive: return `"no accessible result"` per platform policy.
5. Otherwise: return a short summary derived from allowed chunks only, with citations.

---

### `find_requirement(requirementId)`

> Locate the indexed chunks tied to a specific requirement ID.

**Inputs:**

| Field | Type | Required |
|---|---|---|
| `requirementId` | string | yes |

**Behavior:**
1. Authenticate + resolve groups.
2. Search by exact `requirementId` metadata match.
3. ACL filter applied.
4. Return matching chunks with citations.

---

### `find_interface(interfaceName)`

> Locate the indexed chunks tied to a specific interface (ICD-style metadata field).

**Inputs:**

| Field | Type | Required |
|---|---|---|
| `interfaceName` | string | yes |

**Behavior:**
1. Authenticate + resolve groups.
2. Search by exact `interface` metadata match.
3. ACL filter applied.
4. Return matching chunks with citations.

---

### `find_testability_notes(feature)`

> Locate the indexed chunks tagged as testability notes for a given feature.

**Inputs:**

| Field | Type | Required |
|---|---|---|
| `feature` | string | yes |

**Behavior:**
1. Authenticate + resolve groups.
2. Search by `feature` metadata match plus `docType` in the testability set (platform-defined).
3. ACL filter applied.
4. Return matching chunks with citations.

---

### `find_owner(systemOrDoc)`

> Resolve the owning group for a system or document.

**Inputs:**

| Field | Type | Required |
|---|---|---|
| `systemOrDoc` | string | yes |

**Behavior:**
1. Authenticate + resolve groups.
2. Resolve owner from index metadata (`ownerGroup`) or from a platform owner-registry.
3. Return owner-group label and a contact path; **never** return private contact details from a directory the user can't access.

---

## Authentication concept

- The service requires a valid **Entra ID** identity for every call.
- Tokens are passed through from the calling AI assistant (or its MCP gateway).
- The service **does not** accept anonymous or service-principal calls for retrieval — every retrieval is bound to a user identity, so ACL trimming has someone to trim against.
- Service-principal access is used **only** for ingestion workers and admin operations, not for retrieval.

---

## Entra group resolution concept

- On every call, the service resolves the caller's group set (cached per session, refreshed at a platform-defined interval).
- Group set is used to build the Azure AI Search ACL filter.
- Group changes propagate at the next refresh — there is no "stale group set" longer than the refresh window.
- The platform team owns the group-resolution component and its caching policy.

---

## Azure AI Search dependency

- One **index per team** (or one shared index with `team` as a hard filter — platform team's choice).
- Required fields: `team`, `system`, `sourceSystem`, `sourceUrl`, `docTitle`, `docType`, `classification`, `ownerGroup`, `allowedGroups`, `lastIndexed`, plus the team's optional metadata (`requirementId`, `interface`, `feature`, `testLane`, `safetyImpact`, …).
- Embeddings: platform-defined model. The Team RAG service is **vendor-aware, not vendor-locked** at the embedding layer.
- Reindex strategy: nightly incremental + full reindex on schema/ACL change.

---

## ACL filtering

- **Filter applied at query time, before chunks reach the model.**
- Implementation: `allowedGroups/any(g: search.in(g, '<user-group-1>,<user-group-2>...'))` — or the platform's preferred Azure AI Search filter form.
- The service **never** retrieves un-filtered and trims afterwards.
- The service **never** supports a "bypass ACL" mode.

For the conceptual demo of this behavior, see [`examples/local-permission-demo/`](../../../examples/local-permission-demo/).

---

## Citations

- Every returned chunk **must** carry: `sourceUrl`, `docTitle`, `classification`, `chunkId`.
- The calling assistant **must** present citations to the user. Answers without citations are an anti-pattern.
- Citations are how the user verifies the assistant didn't fabricate.

---

## No write by default

- This service exposes **only** retrieval operations.
- There is **no** `update_doc` or `delete_doc` operation in the user-facing surface.
- Document changes happen at the **source** (Confluence, SharePoint, the team's Bitbucket repo) and propagate via the next ingestion run.
- This means: write-back from the assistant happens by writing to the source system (with the user's own permissions, via separate approved write tools), not by writing to this service.

---

## Audit

For every call, log:

- caller identity, resolved group set
- operation name and inputs (with sensitive fields redacted per platform policy)
- chunks returned (IDs only by default, content only if policy allows)
- chunks dropped by ACL (count, optionally IDs per policy)
- timestamp, latency

Audit logs follow the platform retention policy.

---

## What the platform team builds vs what each team configures

| Area | Platform team (once) | Each team (per onboarding) |
|---|---|---|
| MCP/API surface (this skeleton) | ✅ | — |
| Azure AI Search index template | ✅ | — |
| Ingestion pipeline (chunk · classify · stamp ACL) | ✅ | — |
| Group resolution + caching | ✅ | — |
| ACL filter logic | ✅ | — |
| Audit logging | ✅ | — |
| Sources to ingest | — | ✅ |
| Metadata values | — | ✅ |
| `allowedGroups` per source | — | ✅ |
| Golden questions | — | ✅ |

Templates a team uses for the per-team configuration are in [`templates/team-onboarding/`](../../team-onboarding/).

---

## Related pages

- [Team RAG framework](../../../docs/team-rag-framework.md) — the build pattern this service implements
- [Permission model](../../../docs/permission-model.md) — how ACL trimming works end-to-end
- [Connectors & RAG options](../../../docs/connectors-and-rag-options.md) — how Team RAG differs from Atlassian MCP, M365 connectors, Work IQ
- [MCP catalog](../../../docs/mcp-catalog.md) — where this service fits in the MCP allowlist
- [Local permission demo](../../../examples/local-permission-demo/) — conceptual demo of the ACL filter
