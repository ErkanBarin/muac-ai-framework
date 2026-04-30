# 🎨 Visual Architecture Cheat Sheet

All the diagrams in one place. Each one is a single idea — copy any of them into a slide, a Confluence page, or a kickoff meeting.

---

## 1. Enterprise platform architecture

```mermaid
flowchart TD
    A[Departments<br/>SRS · OSDR · Engineering · QA · Ops · Safety · Infra · Data · Docs] --> B[Approved AI entrypoints]

    B --> C[Claude Code via MS Foundry]
    B --> D[GitHub Copilot Enterprise]
    B --> E[M365 Copilot / Work IQ]

    C --> F[Governed MCP Gateway]
    D --> F
    E --> G[Enterprise Knowledge Layer]

    G --> H[M365 Copilot Connectors]
    G --> I[Department RAG Indexes<br/>Azure AI Search]
    G --> J[Repo-local Memory]

    F --> K[Approved Tool Catalog]

    J --> L[Department Workflows]
    H --> L
    I --> L
    K --> L

    L --> M[Outputs<br/>PRs · Tests · Jira · Confluence · ADRs]
    M --> J
    M --> H
```

---

## 2. Memory hierarchy

```mermaid
flowchart LR
    A[Company memory<br/>Confluence · SharePoint · Teams · Jira] --> Z[Assistant]
    B[Team memory<br/>Azure AI Search · ICDs · Architecture] --> Z
    C[Project memory<br/>docs/memory/* · current truth] --> Z
    D[Live system context<br/>logs · DBs · CI · Azure] --> Z
    Z --> R[Answer with citations]
    R --> WB[Write-back to durable layer]
    WB --> A
    WB --> B
    WB --> C
```

---

## 3. Team RAG architecture

```mermaid
flowchart LR
    A[Team sources<br/>Confluence · SharePoint · Repos · Controlled stores] --> B[Ingestion pipeline]
    B --> C[Chunk · classify · attach metadata · attach ACL]
    C --> D[Azure AI Search team index]
    E[Claude / Copilot / Foundry agent] --> F[Team RAG MCP]
    F --> G[Resolve user identity + groups]
    G --> H[Query Azure AI Search<br/>with ACL filter]
    D --> H
    H --> I[Allowed chunks only]
    I --> J[Answer with citations]
```

---

## 4. Permission / security trimming

```mermaid
flowchart TD
    A[User asks question] --> B[Authenticate via Entra ID]
    B --> C[Resolve user groups]
    C --> D[Run search with ACL filter]
    D --> E{Document ACL allows user?}
    E -->|Yes| F[Return chunk + citation]
    E -->|No| G[Drop chunk silently]
    F --> H[Assistant answers using allowed sources]
    G --> I["No accessible result / restricted"]
```

---

## 5. MCP governance tiers

```mermaid
flowchart LR
    T0[Tier 0<br/>Read public/internal docs] --> T1[Tier 1<br/>Read repo / code]
    T1 --> T2[Tier 2<br/>Edit local branch]
    T2 --> T3[Tier 3<br/>Run local tests]
    T3 --> T4[Tier 4<br/>Write Confluence / Jira]
    T4 --> T5[Tier 5<br/>Run live env tests]
    T5 --> T6[Tier 6<br/>Mutate test systems]
    T6 --> T7[Tier 7<br/>Infra / security changes]
```

---

## 6. SRS example: query routing

```mermaid
flowchart TD
    A[Question about SRS alert testing] --> B[Project repo memory]
    B --> C{Enough project truth?}
    C -->|Yes| D[Plan from repo]
    C -->|No| E[SRS Team RAG]

    E --> F{Sensitive docs needed?}
    F -->|No| G[Azure AI Search team index]
    F -->|Yes| H[Federated / restricted retrieval]

    G --> I[Allowed chunks + citations]
    H --> I

    I --> J{Need live system facts?}
    J -->|No| K[Testability decision]
    J -->|Yes| L[Approved read-only MCP tool]

    L --> K
    K --> M[Write durable finding back]
```

---

## 7. Write-back loop

```mermaid
flowchart LR
    A[Agent / human discovers a durable fact] --> B{Where does it belong?}
    B -->|Active project truth| C[docs/memory/* in this repo]
    B -->|Team domain knowledge| D[Team RAG ingestion source]
    B -->|Formal decision| E[ADR / Jira / Confluence]
    B -->|Sensitive detail| F[Stay in originating restricted store]
    C --> G[Bump 'Last verified' date]
    D --> H[Re-index in Team RAG]
    E --> I[Link from decision-log.md]
```

---

## 8. Team onboarding flow

```mermaid
flowchart TD
    A[1. Source inventory] --> B[2. Classify sensitivity]
    B --> C[3. Pick retrieval mode]
    C --> D[4. Configure ingestion]
    D --> E[5. Define metadata + ACL]
    E --> F[6. Deploy Team RAG MCP]
    F --> G[7. Define golden questions]
    G --> H[8. Run permission tests]
    H --> I[9. Add allowed MCP tools<br/>read-only first]
    I --> J[10. Go live + audit + write-back]
```

---

## 9. Decision: which retrieval layer?

```mermaid
flowchart TD
    Q[Assistant gets a question] --> A{Active project task?}
    A -->|Yes| P[Repo memory]
    A -->|No| B{Team-specific technical?}
    B -->|Yes| T[Team RAG]
    B -->|No| C{Workplace / meeting context?}
    C -->|Yes| W[Work IQ / M365 Copilot]
    C -->|No| D{Live system fact?}
    D -->|Yes| M[Approved MCP tool]
    D -->|No| Esc[Stop and ask user]
```

---

## 10. Approval gate (write / mutation)

```mermaid
flowchart LR
    A[Agent proposes write/mutation] --> B[Show full intent + scope]
    B --> C{User approves?}
    C -->|No| D[Cancel + log]
    C -->|Yes| E{High-risk tier?}
    E -->|No| F[Execute + audit log]
    E -->|Yes| G[Two-person approval]
    G --> F
```
