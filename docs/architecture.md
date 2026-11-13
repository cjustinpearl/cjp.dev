# Architecture

Planning revision 0.3 · 2026-10-19 · implementation defaults accepted; deployment unverified · [decision register](decisions.md).

## Boundaries and intent

Use one SvelteKit web application, one PostgreSQL service, and a Python content toolchain with RabbitMQ/Celery. Chat is synchronous/streaming HTTP and does not wait on the ingestion queue. The owner CLI is the initial Celery producer; TypeScript does not implement Celery's task protocol. Public GraphQL reads only published evidence. Approved content and publication artifacts bridge private preparation and public delivery.

Diagrams are skeletons, not deployed-state claims. Labels `PROPOSED`, `TBD`, and `LATER` identify unresolved placement or deferred components. Arrows represent permitted logical flows, not unrestricted network access. Standalone `.mmd` files are the diagram editing source; embedded copies below must remain identical.

## System level

People, deployments, external services, and trust boundaries. Broker and database ports are private. Public artifacts, their independent mirror, and private encrypted backups have separate destinations and credentials.

```mermaid
flowchart TB
  V["Recruiter / public visitor"]
  O["Owner / private CLI"]
  E["PROPOSED: edge proxy / DDoS provider<br/>provider and plan TBD"]
  subgraph HOST["Selected deployment: existing Hetzner CPX31, us-west<br/>4 vCPU / 8 GB RAM / 160 GB disk / Ubuntu 24.04.4 LTS"]
    C["Caddy / TLS ingress"]
    W["SvelteKit / Node<br/>public pages, GraphQL, access, chat"]
    P[("PostgreSQL<br/>separate roles and schemas")]
    R["RabbitMQ<br/>private task broker"]
    PY["Python / Celery worker<br/>bounded ingestion jobs"]
    F[("Private input / candidate files<br/>never public web assets")]
    T["OpenTelemetry export<br/>destination / retention TBD"]
    C --> W
    W --> P
    R --> PY
    PY --> P
    PY --> F
    W -. "sanitized telemetry" .-> T
    PY -. "sanitized telemetry" .-> T
  end
  G["Public Git repository: cjustinpearl/cjp.dev<br/>approved content and code only"]
  CI["GitHub Actions<br/>checks, corpus build, release signing"]
  A[("GitHub immutable release assets<br/>public artifacts and proof bundles")]
  BACK[("Private encrypted backups<br/>off-host destination TBD")]
  MIRROR[("Public artifact mirror<br/>independent destination TBD")]
  SIG["Sigstore public services<br/>Fulcio / transparency log"]
  OTS["OpenTimestamps calendars<br/>eventual Bitcoin anchor"]
  MAIL["Transactional email<br/>provider TBD"]
  LLM["OpenAI Responses API<br/>model TBD"]
  K["LATER: Kafka and replay consumers<br/>placement TBD"]
  V -->|HTTPS| E
  E -->|protected origin HTTPS| C
  O -->|private network / tunnel| R
  O -->|private input transfer| F
  O -->|reviewed publication| G
  G --> CI
  CI -->|signed outputs| A
  CI --> SIG
  CI --> OTS
  A -->|verify before deployment / import| W
  P -. "encrypted backups" .-> BACK
  A -. "independent copy" .-> MIRROR
  W -->|HTTPS| MAIL
  W -->|bounded HTTPS streaming| LLM
  W -. "later: transactional outbox relay" .-> K
  V -->|independent download and verify| A
```

[Standalone system diagram](diagrams/system.mmd)

The existing Hetzner host was selected for launch on 2026-10-19, subject to live capacity and ingress verification. On 2026-10-12, the owner supplied the inventory and confirmed nothing substantial is currently running; the host has not been inspected. The existing hosting bill is outside the US$20–50 additional monthly project budget. A single host is not highly available. If ingestion disrupts chat, move or run ingestion on demand before increasing architecture complexity. Any co-tenants must have separate credentials, volumes and resource budgets. The origin address belongs in deployment configuration; it is unnecessary in public architecture diagrams.

### Initial VPS resource envelope (proposed)

These are starting container memory ceilings to validate, not measured consumption or guarantees. Keep one Celery task executing at a time initially and bound ingestion CPU to approximately one vCPU. Build images and run browser suites in CI or development, outside the production host.

| Component | Proposed memory ceiling | Placement / constraint |
|---|---|---|
| SvelteKit / Node | 768 MiB | One web process; hosted model calls, no local model. |
| PostgreSQL | 1,536 MiB | Small bounded connection pools; tune query memory within this envelope. |
| RabbitMQ | 1,024 MiB | Small task-reference messages; bounded queue backlog. |
| Celery worker | 1,024 MiB | One concurrent task; text-bearing PDFs with input/time limits. |
| Caddy | 256 MiB | Coordinate ports and routing with any existing ingress. |
| Telemetry exporter / optional collector | 256 MiB | Bounded buffers; no full local observability cluster. |

The proposed ceilings total 4.75 GiB, leaving roughly 2–3 GiB of nominal host capacity for OS/cache, deployment and backup peaks. No substantial competing workload is currently reported. Measure actual usable RAM before choosing limits and review the allocation if another project is deployed later. Confirm recovery from a worker memory-limit termination and test chat/retrieval with ingestion active.

For RabbitMQ, initially evaluate an absolute 512 MiB memory alarm below its 1 GiB container ceiling. The alarm throttles publishers; it is not a hard memory cap, and transient overhead needs headroom ([RabbitMQ guidance](https://www.rabbitmq.com/docs/memory)).

Use separate persistent volumes, bounded log/image/candidate retention and disk-pressure alerts. Provisionally keep at least 30 GB disk free for database, upgrade and recovery headroom; validate against actual disk usage. Store encrypted database backups off-host and test restoration. Add Kafka or security labs only after reviewing their additional resource needs. Hosting and variable-service allocations are tracked in the [budget decision](decisions.md#budget-and-capacity-update--2026-10-12).

## Application level

Runtime modules and the independent publication path. Every AI request passes eligibility, session, grant, and atomic cost checks before the provider call. Chat receives only publication-approved evidence; ingestion cannot activate a corpus.

```mermaid
flowchart LR
  B["Browser"]
  subgraph APP["SvelteKit application — one deployable"]
    P["Prerendered résumé / projects<br/>static fallback"]
    G["PROPOSED: GraphQL Yoga<br/>bounded public evidence queries"]
    AUTH["Authentication + mailbox eligibility"]
    GRANT["Access grant + revocation<br/>atomic usage / spend reservation"]
    CHAT["Chat orchestrator<br/>stream, deadline, finalize usage"]
    RET["Retrieval<br/>pin one published corpus version"]
    CITE["Citation resolver / source inspector<br/>signature status separate from truth"]
    META["Operational metadata / telemetry<br/>no transcript retention"]
  end
  DB[("Postgres: auth / grants / usage")]
  KB[("Postgres: published knowledge<br/>full-text first; pgvector conditional")]
  MODEL["Hosted LLM"]
  CLI["Owner Python CLI"]
  MQ["RabbitMQ"]
  WORK["Celery ingestion<br/>extract, normalize, candidate checks"]
  REVIEW["Owner review then Git commit"]
  PUB["CI publication builder<br/>signed corpus + approved summaries"]
  B --> P
  B --> G
  B --> AUTH
  AUTH --> DB
  B --> CHAT
  CHAT --> GRANT
  GRANT --> DB
  CHAT --> RET
  RET --> KB
  G --> KB
  CHAT --> MODEL
  CHAT --> CITE
  CITE --> KB
  CHAT --> META
  CLI --> MQ
  MQ --> WORK
  WORK --> REVIEW
  REVIEW --> PUB
  PUB -->|verified release import| KB
  PUB -->|build public pages| P
```

[Standalone application diagram](diagrams/application.mmd)

### Publication and retrieval

1. CLI submits a reference to bounded private input. Do not put whole PDFs or sensitive text into broker messages.
2. Worker extracts and normalizes candidate text with lineage, page/section locators and pipeline version. It writes only private candidate state.
3. Owner reviews accuracy, attribution, source permissions and public suitability. Approved Markdown and permitted excerpts enter public Git through a deliberate export.
4. CI builds a corpus snapshot using the same versioned Python transformation code via a deterministic CLI entry point. This need not run through RabbitMQ. Optional model-authored summaries must be reviewed and frozen before signing.
5. CI checks corpus references and evaluations, signs the manifest, starts OpenTimestamps, and publishes immutable artifacts. A privileged publication command verifies signatures and checksums, stages the version, and changes the active corpus pointer atomically.
6. A chat request pins one corpus digest for retrieval and citation resolution. A deployment pins matching public pages, GraphQL views and corpus; health checks detect revision mismatch. Old versions remain available for existing citations.

Start retrieval with PostgreSQL full-text search and explicit metadata filters. Evaluate paraphrases and ambiguous questions; introduce `pgvector` only if it closes a measured gap. Embedding configuration and corpus version then become part of the publication manifest. Do not treat company background as evidence of Justin's personal contribution.

### Interfaces

| Boundary | Proposed contract | Important constraint |
|---|---|---|
| Browser → public pages | Prerendered HTML / static assets | Core résumé and contact useful without JavaScript or chat. |
| Browser → GraphQL | Read-only evidence queries, bounded pagination, approved operation set | No private candidate, identity, usage or telemetry schema exposure. |
| Browser → authentication | Magic-link request and deliberate confirmation/redeem | Plain link tokens never enter logs; requests have generic responses and layered rate limits. |
| Browser → chat | Authenticated POST, streamed progress/text, completion with source IDs and corpus digest | Browser submits text, not privileged model tools; server bounds history and cost. |
| Owner CLI → broker | Native Celery protocol, JSON arguments containing job IDs and private input references | Reject unsafe serializers; private authenticated transport. |
| Worker → candidate store | Idempotent result keyed by input digest, transformation version and options | An extraction retry cannot publish or duplicate a corpus. |
| CI → deployment | Versioned release manifest, build and corpus digests, proof bundles | Verify expected identity and immutable bytes before activation. |
| Future producer → Kafka | Versioned sanitized event envelope | Introduce outbox with the Kafka slice; never rely on events for live authorization or spending. |

Exact routes and schema field names are implementation contracts to approve in the relevant slice, not frozen API promises in this draft.

## Data level

Conceptual entities and relationships, not migration-ready tables. Authentication package tables will be adapted rather than duplicated. An ingestion job can produce multiple candidate documents; publication assembles immutable source and claim versions. Signed records may refer to a corpus, release or milestone; the corpus relationship applies only to corpus-related records.

```mermaid
erDiagram
  PRINCIPAL ||--o{ ACCESS_GRANT : receives
  PRINCIPAL ||--o{ SESSION : authenticates
  PRINCIPAL ||--o{ LOGIN_TOKEN : redeems
  ACCESS_GRANT ||--o{ USAGE_RESERVATION : authorizes
  CORPUS_RELEASE }o--o{ SOURCE_VERSION : includes
  CORPUS_RELEASE }o--o{ CLAIM_VERSION : includes
  SOURCE_VERSION ||--o{ CHUNK : yields
  CLAIM_VERSION ||--o{ CLAIM_EVIDENCE : supported_by
  CHUNK ||--o{ CLAIM_EVIDENCE : cited_by
  PROJECT ||--o{ CLAIM_VERSION : contextualizes
  CLAIM_VERSION ||--o{ CLAIM_SKILL : demonstrates
  SKILL ||--o{ CLAIM_SKILL : labels
  INGESTION_JOB ||--o{ CANDIDATE_DOCUMENT : produces_private
  SOURCE_VERSION o|--o{ CANDIDATE_DOCUMENT : approved_into
  CORPUS_RELEASE o|--o{ SIGNED_RECORD : attested_by
  SIGNED_RECORD ||--o{ PROOF_ARTIFACT : has
  SIGNED_RECORD o|--o{ SIGNED_RECORD : references_previous
  CORPUS_RELEASE o|--o{ QUERY_METADATA : used_by
  PRINCIPAL {
    uuid id PK
    text verified_email
  }
  ACCESS_GRANT {
    uuid id PK
    uuid principal_id FK
    timestamp expires_at
    int allowance
    timestamp revoked_at
  }
  LOGIN_TOKEN {
    text token_hash PK
    timestamp expires_at
    timestamp consumed_at
  }
  SESSION {
    uuid principal_id FK
    timestamp expires_at
  }
  USAGE_RESERVATION {
    uuid request_id PK
    uuid grant_id FK
    text state
    numeric reserved_cost
    numeric reconciled_cost
  }
  CORPUS_RELEASE {
    text digest PK
    text git_revision
    text manifest_digest
    timestamp activated_at
  }
  SOURCE_VERSION {
    text digest PK
    text source_kind
    text public_locator
    text publisher_attribution
  }
  CHUNK {
    text id PK
    text source_digest FK
    text locator
    text exact_excerpt
  }
  CLAIM_VERSION {
    text id PK
    text statement
    text evidence_class
    text qualifications
  }
  CLAIM_EVIDENCE {
    text claim_id FK
    text chunk_id FK
    text relationship
  }
  CLAIM_SKILL {
    text claim_id FK
    text skill_id FK
  }
  PROJECT {
    text id PK
    text title
  }
  SKILL {
    text id PK
    text label
  }
  INGESTION_JOB {
    uuid id PK
    text input_digest
    text pipeline_version
    text state
    int attempts
  }
  CANDIDATE_DOCUMENT {
    uuid job_id FK
    text private_locator
    text review_state
  }
  SIGNED_RECORD {
    text digest PK
    text kind
    text subject_digest
    text previous_digest
    text criteria_digest
    timestamp asserted_event_time
  }
  PROOF_ARTIFACT {
    text record_digest FK
    text kind
    text verification_state
    text artifact_locator
  }
  QUERY_METADATA {
    uuid request_id PK
    text corpus_digest FK
    text outcome
    int input_tokens
    int output_tokens
    int duration_ms
  }
```

[Standalone data diagram](diagrams/data.mmd)

### Ownership and storage

| State | Owner / permitted readers | Retention proposal |
|---|---|---|
| Public approved Markdown, claims, permitted excerpts | Git author/reviewer; public | Versioned history; corrections and withdrawals explicit. |
| Original private inputs and candidate files | Owner CLI / ingestion worker only | Delete after approval plus 30 days unless deliberately retained outside site infrastructure. |
| Published corpus / full-text index | Publication command writes; app and worker have only required reads | Preserve versions referenced by published records; indexes are rebuildable. |
| Email, sessions, token hashes, access grants | Auth/access modules only | Tokens expire promptly; inactive account metadata purged after a proposed 90 days, subject to agreed abuse/account policy. |
| Usage reservations and reconciled totals | Chat/access module | Metadata 30 days proposed; retain non-identifying aggregate budget totals as needed. |
| Traces and logs | Operator only | Seven days traces / 30 days logs proposed; redact text, tokens and identifiers at source. |
| Signed records and proof files | Publication process appends; anyone verifies public versions | Retained in multiple locations; corrections never rewrite signed record bytes. |
| Query metadata | Operator; only sanitized per-query timing shown to visitor | No question, answer, raw prompt, job description, or chain-of-thought fields. |

Retention values are P-level proposals (U07). Deletion also needs a documented backup-expiration window. The browser may hold in-session conversation state in memory; the server validates and truncates any resubmitted history. Operational logs must not accidentally defeat the no-transcript policy. Model-provider retention is a separate contract to disclose and verify.

One migration sequence owns shared schema changes. Python and TypeScript consume the same field-level contracts. The worker's DB role cannot write auth, grants, usage, published knowledge or active-version pointers. The web role cannot read private candidates or operate schema migrations. The publication identity has narrowly scoped activation rights.

## Integration level

Request, task, publication and proof lifecycles. Store represents distinct public artifacts, private candidates and scoped database state; the actual implementation must preserve those boundaries.

```mermaid
sequenceDiagram
  autonumber
  actor Owner
  participant CLI as Python CLI
  participant Queue as RabbitMQ / Celery
  participant Git as Public Git / CI
  participant Proof as Sigstore / OpenTimestamps
  participant Store as Artifacts / Postgres
  actor Visitor
  participant App as SvelteKit
  participant External as Email / Model providers
  Owner->>CLI: Submit private input and requested transform
  CLI->>Queue: Job ID, input reference, pipeline version
  Queue->>Store: Candidate output and bounded job status
  Note over Queue,Store: Retry transient errors, deduplicate outputs, terminal failure visible
  Owner->>CLI: Review candidate and approve public material
  CLI-->>Owner: Approved Markdown / permitted excerpts
  Owner->>Git: Commit reviewed publication
  Git->>Proof: Sign corpus manifest and submit digest for timestamp
  Proof-->>Git: Sigstore bundle, OTS proof may be pending
  Git->>Store: Verify, import immutable version, activate atomically
  Note over Git,Store: Old corpus remains active on failure, pin version for each query
  Visitor->>App: Request access with company email
  App->>External: Rate-limited single-use link delivery
  Visitor->>App: Deliberate redemption then chat request
  App->>Store: Verify grant and reserve allowance / cost atomically
  alt Denied or budget unavailable
    App-->>Visitor: Explain access / allowance state
  else Authorized and reserved
    App->>Store: Retrieve from one signed public corpus version
    App->>External: Bounded model request with selected evidence
    External-->>App: Stream / completion / failure
    App-->>Visitor: Provisional stream then final sources and safe timings
    App->>Store: Reconcile usage, store metadata only
  end
  Note over Git,Proof: Baseline uses existing tools before implementation, later CI automates records
  Owner->>Git: Accept checks-passed milestone by digest
  Git->>Proof: Append owner-accepted record and proofs
  Git->>Store: Publish verified record and downloadable bundle
  Visitor->>Store: Download record, sources and proof bundles
  Note over Visitor,Store: Independent verification checks identity and bytes, not factual truth
```

[Standalone integration diagram](diagrams/integration.mmd)

### Failure behavior

| Failure | Required behavior |
|---|---|
| Email delivery error / duplicate request | Bound retries and sending spend; preserve generic public response and useful private diagnostic. No grant before redemption. |
| Provider timeout after potential model execution | Mark usage uncertain and retain conservative reservation until reconciliation; do not automatically retry an ambiguous charge. |
| Browser disconnect | Attempt upstream cancellation; bound execution even if cancellation is not honored; finalize/retain reservation appropriately. |
| Worker crash / duplicate delivery | Retry only idempotent work; test ack/requeue configuration, use unique result key and bounded poison-job handling. |
| Broker downtime | Public pages and chat stay usable; CLI reports that ingestion was not accepted or has uncertain dispatch status. |
| Database outage | Serve built public résumé; authenticated chat fails closed. Never call paid model without successful authorization/reservation. |
| Bad corpus / partial import | Keep prior active version. Verify artifact before use; do not mix corpus versions during a query. |
| Sigstore unavailable | New publication remains pending; existing verified corpus and site continue. No fabricated “verified” status. |
| OpenTimestamps pending | Preserve exact signed record; show pending anchor and publish proof upgrades separately. Baseline requires verified Sigstore evidence and OTS submission before implementation. Deployment and acceptance may proceed while anchoring is pending; fully verified status must wait. |
| Host loss | Restore database/config/artifacts from independent backups; reproduce indexes and containers; verify restored public artifact digests. |
| Kafka unavailable later | Bound outbox growth and alert; chat does not wait for Kafka. Explicitly define retention/overflow before enabling events. |

## Later extension contracts

Kafka can consume `content.published`, `query.completed`, and `milestone.attested` events containing IDs, versions, durations and cost counters. Exclude emails, tokens and conversational text. At-least-once delivery is acceptable with consumer deduplication; demonstrate replay producing the same projection. A single-broker lab must be labeled as such.

Future job matching can consume the signed source/claim graph and add rubric version, requirements-to-evidence mappings, gaps and output digest. Signed approved summaries are supported at launch; runtime signed assessments remain a later design decision. See [provenance](provenance.md) for why a valid signature is not proof of fairness or truth.
