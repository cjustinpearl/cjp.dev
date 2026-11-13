# Software requirements specification

Planning revision 0.3 · 2026-10-19 · release requirements approved; derived from the [PRD](prd.md) and [decisions](decisions.md).

“Shall” describes accepted release criteria. Explicit operational policy examples remain proposals until U07 is resolved; release evaluation and nonfunctional targets are accepted as specified below. NFR-03 requires measurement and reporting, not achievement of its latency objective. “Later” requirements do not block initial release. Verification is behavioral; no requirement is satisfied solely by adding a library, configuration file or passing a mocked happy path.

## Public product and evidence

| ID | Requirement | Verification |
|---|---|---|
| PUB-01 | The site shall expose career overview, one approved résumé, two approved case studies and contact without authentication or AI. Résumé download, core text and links shall work without client JavaScript. | Approved content inventory; browser checks with JavaScript disabled and model endpoint unavailable. |
| PUB-02 | Each published contribution shall identify project context, personal role, supported outcomes, relevant skills and evidence qualifications. Company background shall not establish personal participation. | Owner review of every initial contribution and a deliberately ambiguous attribution fixture. |
| PUB-03 | Public GraphQL shall expose only published evidence with bounded pagination and operation complexity. Launch operations shall be explicitly allowlisted; variable limits still apply. | Reject unknown operations, excessive page size, aliases/batches and private-field requests; verify bounded resolver query counts. |
| PUB-04 | Each source reference shall identify the exact published version and a page, section or chunk locator. Corrections shall create a new version. | Old citations resolve after publication of a replacement; changed bytes fail manifest validation. |
| PUB-05 | UI shall provide keyboard navigation, visible focus, readable contrast, reduced-motion behavior and understandable access/error states. | Automated accessibility scan plus manual keyboard, screen-reader spot check and mobile review against WCAG 2.2 AA scope. |

## Identity, authorization and spending

| ID | Requirement | Verification |
|---|---|---|
| AUTH-01 | Automatic eligibility shall require an exact approved email domain and proven mailbox control. Subdomains require explicit policy; substring matches are forbidden. Invitations shall be individually scoped. | Approved, unapproved, deceptive-suffix, IDN and invitation cases. |
| AUTH-02 | Login tokens shall be random, short-lived, hashed at rest and atomically single-use. A passive GET/prefetch shall not consume a token; deliberate confirmation shall perform redemption. | Concurrent redemption yields one successful session; expired/reused token fails; ordinary link fetch leaves it usable. Advanced scanners are not guaranteed distinguishable from users. |
| AUTH-03 | Sessions shall use secure HttpOnly cookies, appropriate SameSite policy, constrained redirects and CSRF/origin controls for state changes. | Session fixation/redirect/origin tests; cookie inspection; logout invalidates session. |
| AUTH-04 | Email delivery shall be rate-limited by normalized address, trustworthy client-network signal and global budget, with a generic public response. Raw tokens and complete magic-link URLs shall not be logged or sent to analytics. | Abuse simulation with stub mail transport; inspect logs; spoofed forwarding header cannot bypass limits. |
| AUTH-05 | An expiring, revocable grant shall control AI allowance independently of sessions. Re-login or requesting another link shall not reset an existing grant's usage. | Re-login, multiple sessions, expiry, revocation and explicit renewal tests. |
| AUTH-06 | Before each provider call, the application shall atomically reserve the maximum permitted request cost and allowance against grant and global limits. Ingestion/model costs shall also count toward the relevant global budget. | Concurrent requests cannot exceed configured reserved allowance/cost; no provider call on failed reservation. |
| AUTH-07 | Request reservations shall have unique IDs and states reserved, finalized, released or uncertain. Confirmed non-execution may release a reservation; ambiguous provider failure shall retain conservative accounting until resolved. | Timeout-before-send, timeout-after-send, cancellation, duplicate finalization and process-restart cases. |
| AUTH-08 | The system shall limit request size, history, evidence context, output tokens, concurrent requests, request duration and retries. Operator shall be able to disable paid AI without disabling public content. | Oversized/concurrent requests rejected; kill switch demonstrated; provider stub records bounded calls. |

Proposed initial policy for review: magic-link TTL 10 minutes; grant 20 questions over 7 days; one concurrent model request per grant; three globally; no automatic provider retry following an ambiguous charge; global spend cap TBD. Input/output/token limits depend on the selected model and evaluated answer length. These are configuration, not hardcoded business logic. Application reservations supplement provider controls; they cannot guarantee an exact invoice ceiling where provider billing or cancellation is uncertain.

## Content ingestion and publication

| ID | Requirement | Verification |
|---|---|---|
| KB-01 | Owner CLI shall accept Markdown, UTF-8 text, text-bearing PDFs and selected web URLs. It shall reject or explicitly mark encrypted, scanned/OCR-required, oversized or unsupported inputs. | Representative formats plus malformed and scanned PDF fixtures. OCR is outside initial scope. |
| KB-02 | Fetching/parsing shall have size, duration, redirect, content-type and resource limits. Web fetches shall reject loopback, private/link-local and cloud metadata addresses, including redirects and resolved addresses. Parsers shall not execute embedded code. | Local-network/redirect abuse fixtures, oversized content and constrained parser execution. No authenticated crawling by default. |
| KB-03 | Each job shall record input digest, pipeline version, options, state and bounded attempts. Identical processing shall not create duplicate accepted output. Worker-loss and acknowledgement behavior shall be intentionally configured. | Kill a worker around processing/ack boundaries, redeliver the message, restart broker, and verify one durable result with no silent loss. |
| KB-04 | Failed jobs shall reach a visible terminal state or quarantine after bounded retries. Lost/uncertain dispatch shall be recoverable by job ID; queued work shall be reconcilable with stored job state. | Poison job, network interruption during publish and recovery tests; no infinite requeue loop. |
| KB-05 | Drafts and raw inputs shall remain private until the owner approves factual accuracy, personal attribution and publication permission. Workers shall not activate public content. | Role/permission tests and approval-history inspection. |
| KB-06 | Publication shall build a versioned manifest covering approved content, source/chunk digests, approved summaries, skill links and transformation configuration. Validate all references and required evaluation results. | Missing source, stale digest, broken skill link and changed summary reject the publication. |
| KB-07 | Publication shall verify expected signer and artifact hashes before staging and atomically activating a corpus. Each query shall use one corpus version throughout. | Failure during import preserves old version; concurrent queries/publication never mix versions. |
| KB-08 | Initial retrieval shall use PostgreSQL full-text and metadata search. Semantic retrieval shall be added only after a versioned evaluation demonstrates benefit. | Record baseline retrieval results; compare any embedding implementation on identical cases and cost. |

## Career Q&A and transparency

| ID | Requirement | Verification |
|---|---|---|
| AI-01 | The model shall receive only bounded, approved evidence and career-Q&A instructions. It shall have no arbitrary SQL, authorization, publishing, browsing or shell tools. | Inspect request construction; injection fixtures cannot invoke privileged actions or read candidates. |
| AI-02 | Answers shall distinguish supported personal claims, owner assertions, contextual background and missing evidence. Unsupported questions shall produce a clear limitation rather than invented experience. | Owner-reviewed evaluation set covering factual, ambiguous, absent, misleading and adversarial questions. |
| AI-03 | Final citations shall resolve to retrieved, versioned source IDs and exact public passages. The server shall reject unknown citation IDs and flag incomplete/invalid output. Streaming text shall remain visibly provisional until completion. | Fabricated citation, truncated stream, corpus replacement and cross-version citation tests. Citation existence does not establish semantic entailment. |
| AI-04 | “Inspect answer” shall expose corpus version, cited passages, evidence class, applicable skill links and measured stage timings. It shall distinguish integrity verification from factual assessment. | Compare displayed timings with recorded stage spans; verify no “truth verified” badge. |
| AI-05 | No raw question, answer, prompt or conversation shall be retained by the application by default. In-memory context shall be bounded; model-provider data handling shall be disclosed separately. | Inspect DB, traces, logs, error reports, analytics and backup samples after test conversations. |
| AI-06 | Evaluation cases, prompts/configuration and results shall be versioned. Release evidence shall include the tested model identifier and corpus revision. | Re-run a fixed suite and inspect evidence manifest; a model/configuration change triggers the relevant evaluation. |

Accepted release evaluation: at least 25 owner-reviewed questions covering accurate retrieval, personal attribution, unknown experience, adversarial instructions and citation integrity. Require zero fabricated personal achievements in the reviewed release set and 100% resolvable final citation IDs. Record failures and reviewer judgments; passing a finite set is not a guarantee about all future answers. External LLM output need not be bit-for-bit reproducible.

## Observability, integrity and delivery

| ID | Requirement | Verification |
|---|---|---|
| OBS-01 | The system shall correlate web requests, retrieval, provider calls and ingestion jobs with request/job IDs and OpenTelemetry spans where applicable. It shall omit sensitive payloads and high-cardinality personal labels. | Follow one request and one job across stages; verify redaction and bounded metric dimensions. |
| OBS-02 | Operator shall observe errors, latency, usage/reservations, queue age/failures, active corpus and host resource pressure. Telemetry failure shall not block public pages or create unbounded queues. | Inject exporter failure and a failed job; inspect alert/diagnostic behavior. |
| PROOF-01 | Before implementation, an externally witnessed baseline shall identify project ID, criteria/docs digest, existing files/content, exclusions and declared reuse. No baseline may be backdated. | Independent verification of signed baseline and Sigstore proof, plus OpenTimestamps submission and retained proof, before first implementation change. |
| PROOF-02 | Checks-passed and owner-accepted milestones shall be separate immutable records referencing the exact tested revision, criteria version and relevant artifact digests. The latter shall reference the accepted checks record. Deployment shall record the activated build as a separate event. | Acceptance of a different revision fails; delay deployment or acceptance without editing the checks record. |
| PROOF-03 | Records shall include prior-record linkage; corrections and scope changes shall append new records. UI shall display asserted event times, external signing/timestamp evidence and pending proof status separately. | Changed date, predecessor, criteria or artifact bytes fail verification; correction preserves original record. |
| PROOF-04 | Every required record shall have a Cosign bundle verified against pinned signer/issuer policy and public Sigstore evidence, plus a submitted OpenTimestamps proof. Deployment and acceptance may proceed with anchoring pending; fully verified status requires a verified anchor. Proof upgrades shall be separate artifacts preserving signed bytes. | Valid, altered, unexpected-signer, missing-proof and pending-anchor fixtures; pending proofs cannot display fully verified status. |
| PROOF-05 | Public corpus manifests and approved summary bytes shall be signable using the same artifact mechanism. Signature claims shall be limited to identity/integrity/provenance. | Verify a public summary via manifest membership; modified summary fails; owner-signed claim remains labeled owner-supplied. |
| PROOF-06 | Verifiers shall work independently of the site using downloaded bytes, proof bundles, documented trust policy and standard tools. Publish required records as GitHub immutable release assets and maintain an independent off-host copy; publish the baseline digest independently of the site. | Verify release immutability, independent copies and baseline digest publication. Verify on a clean environment with the website unavailable; document trust-root/Bitcoin-data network or local prerequisites. |
| OPS-01 | CI shall run relevant formatting/type, unit, contract, integration, browser and evaluation checks against a specified revision. Builds shall use lockfiles and pinned release tooling. | Reproduce from clean checkout; bind results/build hashes to that revision. Live model evaluations are bounded and separate from routine mocked provider tests. |
| OPS-02 | Production shall expose only intended ingress. Services shall use scoped credentials and private broker/DB networks. Untrusted PR jobs shall receive no production/signing secrets or release authority. | Network and role checks; inspect CI permissions and release trigger boundaries. |
| OPS-03 | Backups shall be encrypted, off-host and periodically restored into a disposable environment. Public artifacts and signed records shall have independent copies. | Restore database and referenced artifacts, rebuild indexes and verify hashes. A backup command succeeding alone is insufficient. |
| OPS-04 | Deployment shall verify artifacts, apply reviewed migrations, check readiness and permit rollback to a known compatible build/corpus. Irreversible schema changes require an explicit migration/recovery decision. | Rehearsed failed deployment and compatible rollback, plus migration upgrade checks. |
| OPS-05 | Configuration shall enforce the accepted budget, retention and resource limits. Deletion policy shall state backup expiry and distinguish irreversible public log metadata. | Retention job and config validation tests; public launch blocked on missing spend cap. |

Sizing context for OPS-05: the owner supplied a US$20–50/month additional-spending target, excluding the existing Hetzner bill, and an existing CPX31 with 4 vCPU, 8 GB RAM, 160 GB disk, us-west, Ubuntu 24.04.4 LTS. No substantial current workload is reported. The [resource envelope](architecture.md#initial-vps-resource-envelope-proposed) and US$10/month AI allocation are proposals. Before deployment, record usable capacity, any competing service demands and accepted service caps. Validate effective container limits, bounded disk growth, worker termination/recovery, and latency with ingestion active. Provider billing alerts are supplementary to application-enforced AI limits; they do not cap the entire infrastructure bill.

## Nonfunctional acceptance targets and measured objectives

| ID | Target | Measurement / acceptance condition |
|---|---|---|
| NFR-01 | Public-page LCP ≤1.5 seconds in the agreed lab profile | Median of five cold-cache runs; document device/network profile, region, build and page. Revisit before calling it an SLA. |
| NFR-02 | Retrieval p95 ≤100 ms | At least 100 representative queries over agreed launch corpus on chosen host, with ingestion active and cache conditions recorded. |
| NFR-03 | Model first-token p95 ≤3 seconds — measured objective, not a release gate | Measure at least 30 bounded requests on selected model/region within the agreed cost budget; report provider variability, failures and conditions. Reporting is required; exceeding three seconds alone does not block release. |
| NFR-04 | Initial visitor JS ≤100 KiB compressed, excluding optional chat loaded on demand | Measure actual production route bundles; assess any justified exception before release acceptance. |
| NFR-05 | RPO ≤24 hours; RTO ≤4 hours | Demonstrate backup coverage and restore; record actual elapsed time. No high-availability claim. |
| NFR-06 | Accessible essential journeys | PUB-05 evidence; automated scores alone do not establish WCAG conformance. |
| NFR-07 | Provider / broker / telemetry failure isolation | Failure exercises described above; public portfolio remains useful independently of paid AI and ingestion. |

The owner can amend targets based on real deployment measurements. Record revisions before acceptance rather than silently weakening checks after a failure.

## Later requirements

| ID | Capability | Acceptance outline |
|---|---|---|
| FUT-01 | Kafka sanitized event stream, transactional outbox and replay consumers | Duplicate/reordered delivery handled; restart/replay reconstructs projection; bounded backlog; no dependency for real-time spending authorization. |
| FUT-02 | Job-description matching against signed evidence using a published versioned rubric | Requirement-by-requirement support, gaps, unknowns and transferability; exact evidence links; no unexplained fit percentage. |
| FUT-03 | Optional signed assessment receipt | Bind input commitment, corpus, rubric, software/config/model metadata and exact output bytes; independently verify signature and distinguish this from trusted execution or truth. Signer/privacy design must be approved first. |

## Traceability

| Product requirement | SRS coverage |
|---|---|
| PRD-01 | PUB-01, PUB-05, NFR-01, NFR-04, NFR-06 |
| PRD-02 | PUB-02, PUB-03, PUB-04, KB-06, KB-07 |
| PRD-03 | AUTH-01 through AUTH-08, AI-01, AI-02, AI-05 |
| PRD-04 | KB-01 through KB-08 |
| PRD-05 | AI-03, AI-04, AI-06, OBS-01, OBS-02, NFR-02, NFR-03 |
| PRD-06 | PROOF-01 through PROOF-06 |
| PRD-07 | OPS-01 through OPS-05, NFR-05, NFR-07 |

Each implementation work item shall cite the specific requirement IDs it accepts. A requirement can have multiple tests; test names and CI job paths will be assigned during implementation.
