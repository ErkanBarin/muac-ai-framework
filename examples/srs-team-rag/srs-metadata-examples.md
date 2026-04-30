# SRS — Metadata Examples

Three realistic example chunks showing how the metadata schema applies to typical SRS documents. All values are illustrative.

---

## Example 1 — SRS Interface Control Document (L2)

```json
{
  "team": "SRS",
  "system": "SRS",
  "sourceSystem": "Confluence",
  "sourceUrl": "https://confluence.example/srs/icd/srs-fdps",
  "docTitle": "SRS Interface Control Document — SRS to FDPS",
  "docType": "ICD",
  "classification": "L2",
  "ownerGroup": "SRS-Architecture",
  "allowedGroups": [
    "SRS-Engineering",
    "SRS-Architecture",
    "SRS-Safety",
    "FDPS-Engineering"
  ],
  "lastIndexed": "2026-04-30",
  "interface": "SRS-FDPS",
  "feature": "alert acknowledgement",
  "requirementId": "SRS_ALERT_042",
  "release": "2026.2"
}
```

**Reasoning**

- `classification: L2` — the document contains restricted technical detail.
- `allowedGroups` includes `FDPS-Engineering` because the interface is jointly used.
- `requirementId` and `interface` make the chunk findable via the dedicated MCP tools (`find_requirement`, `find_interface`).

---

## Example 2 — SRS Testability Notes (L1)

```json
{
  "team": "SRS",
  "system": "SRS",
  "sourceSystem": "Confluence",
  "sourceUrl": "https://confluence.example/srs/testability/alert-ack",
  "docTitle": "SRS Testability — Alert Acknowledgement Flow",
  "docType": "TestabilityNote",
  "classification": "L1",
  "ownerGroup": "SRS-QA",
  "allowedGroups": [
    "SRS-Engineering",
    "SRS-QA",
    "SRS-Architecture"
  ],
  "lastIndexed": "2026-04-30",
  "feature": "alert acknowledgement",
  "testLane": "UI",
  "safetyImpact": "low"
}
```

**Reasoning**

- `classification: L1` — internal team material, not interface-restricted.
- `testLane: UI` and `feature` make this discoverable via `find_testability_notes(feature="alert acknowledgement")`.

---

## Example 3 — SRS Security Risk Analysis (L3, federated)

```json
{
  "team": "SRS",
  "system": "SRS",
  "sourceSystem": "RestrictedStore",
  "sourceUrl": "<restricted — federated retrieval>",
  "docTitle": "SRS Security Risk Analysis — Alert Subsystem",
  "docType": "SecurityRiskAnalysis",
  "classification": "L3",
  "ownerGroup": "SRS-Safety",
  "allowedGroups": [
    "SRS-Safety"
  ],
  "lastIndexed": "n/a (federated, fetched live)",
  "safetyImpact": "high"
}
```

**Reasoning**

- `federated: true` — content is *not* indexed; the Team RAG MCP fetches it live and only for users in `SRS-Safety`.
- The `sourceUrl` is a placeholder because federated chunks don't expose source URLs in lower-classification responses.
- `lastIndexed` shows `n/a` because nothing is stored in Azure AI Search.

---

## What this demonstrates

- A single team can mix L0–L3 content with appropriate retrieval modes.
- Same metadata schema; different `classification` and `federated` settings.
- The optional fields (`requirementId`, `interface`, `testLane`, `safetyImpact`) light up specialized retrieval tools.
- Nothing in the metadata reveals secrets or live operational data.
