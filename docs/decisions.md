# Decisions and open questions

Planning revision 0.5 · 2026-11-13 · public planning repository established; no start record signed. Approval of a technology is not evidence that it is implemented.

## Accepted decisions

| ID | Decision | Consequence |
|---|---|---|
| D01 | Public living résumé; recruiter and interviewer audience | Useful career information remains available without AI or sign-in. |
| D02 | SvelteKit / TypeScript application; Python / Celery processing | Two language runtimes are an intentional learning choice. |
| D03 | PostgreSQL | One relational service; avoid a separate vector database initially. |
| D04 | RabbitMQ / Celery and GraphQL in the first release; Kafka follows | Content processing demonstrates work queues; a later milestone demonstrates event replay. |
| D05 | Git and CLI content maintenance | No owner-only content administration UI. Markdown, plain text, text PDFs, and web pages are initial input types. |
| D06 | A verified mailbox on an approved company domain is sufficient | Verify mailbox control, not recruiter status or employment. Individual exceptions can use explicit invitations. |
| D07 | AI gate controls consumption; source material must be approved for public disclosure | Authentication does not turn unpublished career material into permissible AI context. |
| D08 | Operational metadata only by default | Do not persist recruiter questions, answer text, or raw conversations in application logs, database, telemetry, or backups. |
| D09 | Career Q&A first; job-description matching later | Preserve evidence and version identifiers that make later matching inspectable. |
| D10 | Cosign with Sigstore's public transparency log plus OpenTimestamps | Use independently verifiable records; distinguish signed assertions from factual truth. |
| D11 | Witness the baseline immediately before implementation | Planning and existing content are disclosed as prior work. No retroactive start claim. |
| D12 | Record checks-passed and owner-accepted milestones separately | Acceptance can occur later without rewriting the earlier record. |
| D13 | Earliest release meeting agreed criteria; no fixed deadline | Release scope and requirements are versioned; scope changes remain visible. |
| D14 | Public repository; Markdown documents, embedded Mermaid and `.mmd` sources | Start the public repository inside `site/`; keep private parent-workspace material outside it. |
| D15 | Messaging and pipelines are the learning priority; security operations are lower priority | Kafka follows launch; Wazuh/Shuffle are optional subsequent labs. |
| D16 | Hosting is flexible; existing Hetzner CPX31: 4 vCPU, 8 GB RAM, 160 GB local disk, us-west, Ubuntu 24.04.4 LTS | Owner reports nothing substantial currently running, as of 2026-10-12. Verify live resource availability before deployment. |
| D17 | Monthly budget target approximately US$20–50 in additional spending, excluding the existing Hetzner bill | Accounting boundary confirmed. Exact service allocations and enforced AI cap remain proposed. |
| D18 | GitHub owner `cjustinpearl`; repository name `cjp.dev` | Selected on 2026-10-14; public repository created on 2026-11-13 with administrator access verified. Git root is `site/`; the private parent workspace remains outside Git. |
| D19 | Personal signing account: Google identity `c.justin.pearl@gmail.com` | Use for the baseline and personal acceptance records. Identity metadata is public in signing evidence. The exact verifier policy is documented in [provenance](provenance.md#selected-identities). |
| D20 | Release contract approved on 2026-10-19: PRD-01–07 and mapped launch SRS checks, one résumé and two approved case studies | Retain reviewed Q&A, security, recovery and public/retrieval performance checks. Model first-token p95 ≤3 seconds is a measured objective, not a release gate. |
| D21 | MIT for original application code and associated technical documentation | Added [MIT license](../LICENSE) and [content exclusions](../LICENSE-SCOPE.md) on 2026-11-13. Personal résumé material, personal assets and third-party content retain separate rights/notices. |
| D22 | GitHub immutable release assets plus an independent off-host copy; independently publish the baseline digest | Immutable releases enabled and verified through GitHub's API on 2026-11-13. No release published yet. Independent copy/digest publication destinations remain to be established. Preserve exact record bytes and separate versioned proof upgrades. |
| D23 | Verified Sigstore evidence and OpenTimestamps submission required before implementation | Deployment and owner acceptance may proceed while Bitcoin anchoring is pending. Display that state; fully verified status requires successful anchoring. |

## Accepted implementation defaults

P01–P10 were approved on 2026-10-19. Their original IDs are retained for traceability. Explicitly deferred provider, version, policy and capacity details remain unresolved.

| ID | Accepted default | Rationale / remaining detail |
|---|---|---|
| P01 | Initially deploy containers to the existing CPX31 | Reported capacity and light current usage fit the proposed launch workload with one ingestion worker. Existing hosting is funded separately; verify live headroom before deployment. Managed containers remain an alternative. |
| P02 | Caddy + Docker Compose; optional Cloudflare edge proxy | Simple deployment and TLS. Upstream DDoS mitigation needs a provider; software on a saturated VPS cannot supply upstream bandwidth. Edge provider and plan remain TBD. |
| P03 | GraphQL Yoga within SvelteKit, read-only public evidence API | Avoid another application service. Use bounded, approved frontend operations at launch. |
| P04 | Better Auth magic links and sessions; separate application access grants | Reuse authentication plumbing while keeping eligibility, revocation, and spending policy explicit. Validate scanner-safe redemption and hashed-token configuration against the selected version. |
| P05 | Hosted OpenAI model via the Responses API; managed transactional email | Evaluate model and provider choices against correctness, latency, cost, and data handling before launch. No credentials or provider model are selected here. |
| P06 | PostgreSQL full-text retrieval first; pgvector only if retrieval evaluation justifies it | Small corpus and exact career terminology favor a simple measurable baseline. Embeddings may improve paraphrase recall but add ingestion cost and versioning. |
| P07 | Versioned SQL migrations with one migration owner/runner; Drizzle for TypeScript access and psycopg for Python | Both languages share explicit database contracts. Never let independent ORMs manage the same tables. Verify compatible versions during implementation. |
| P08 | GitHub Actions for checks, builds, and milestone automation in `cjustinpearl/cjp.dev` | GitHub owner/repository are selected (D18). CI OIDC is suitable for signing; exact workflow/ref policy and automation remain to be configured. |
| P09 | OpenTelemetry instrumentation; local collector and one trace viewer for development | Preserve portable instrumentation. Production trace storage/provider and retention depend on budget. Start with structured logs and essential metrics, not a large monitoring cluster. |
| P10 | Signed public corpus manifests and reviewed summaries at publication time | Gives answers stable, verifiable evidence without putting live signing in the chat latency path. Corpus publication requires verified Sigstore evidence; OpenTimestamps anchoring may finish asynchronously. |

## Resolved questions

| ID | Resolution |
|---|---|
| U01 | On 2026-10-12, owner confirmed that US$20–50/month covers additional project spending outside the existing Hetzner bill. The current base hosting charge is not needed to allocate this budget; check it before proposing any hosting change. |

## Decisions still needed

| ID | Unknown | Resolve by | Impact |
|---|---|---|---|
| U02 | Live available RAM/disk/CPU and existing ingress configuration | Before deployment | Owner reports no substantial current workload; verify the proposed resource envelope and preserve any existing services. |
| U03 | Domain allowlist, invitation policy, grant allowance and duration | Before access feature acceptance | These are operational policy, not identity guarantees. |
| U04 | Verify the selected personal sign-in flow and exact issuer policy; specify the later CI workflow/ref allowlist | Personal policy before baseline publication; CI policy before its first signed milestone | Repository and Google account are selected (D18–D19). Verify against explicit expected identities, not identities supplied by an untrusted bundle. |
| U05 | Publication-approved résumé and initial case studies | Before corpus release | Existing private briefs are inputs, not authorization to publish. |
| U06 | Exact model, email provider, edge provider, telemetry destination, backup destination | Before integration / release acceptance | Document data handling, costs, secrets, and failure behavior. |
| U07 | Operational numeric limits, retention, service caps and performance measurement profiles | Before their implementation acceptance | Release quality/performance objectives are accepted under D20. Explicit policy examples and resource ceilings remain proposals. |
| U08 | Whether runtime job-match results need signatures, and who independently verifies them | Before the later matching milestone | Launch signs public knowledge; per-result signing needs a separate privacy and signer design. |

The owner has resolved the baseline policy decisions, budget boundary and reported workload questions. U02 is a deployment verification task. Remaining implementation details can be resolved with concrete options during the relevant delivery slice; they do not require another broad architecture review before M0.

## Baseline readiness — 2026-10-19

On 2026-10-19, the owner approved the remaining recommendations below. Repository owner/name and the personal signing account were already accepted. Approval establishes the planning contract; it is not the externally witnessed implementation start.

| Decision | Approved baseline policy |
|---|---|
| Release contract | Adopt PRD-01–07 and their SRS checks; launch with one résumé and two approved case studies. Keep the reviewed Q&A evaluation and security/recovery checks. Treat the three-second model first-token target as a measured objective rather than a release gate. |
| Implementation defaults | P01–P10 accepted, with the repository choice already settled under D18. Exact provider models, service credentials and live host verification can follow in their implementation slices. |
| Licensing | MIT for original application code; explicitly exclude personal résumé material, personal assets and third-party content from that grant. |
| Evidence publication | Use GitHub immutable release assets plus an independent off-host copy; publish the baseline digest independently of the mutable site. Later proof upgrades are additional artifacts. |
| Proof completion | Require verified Sigstore evidence and OpenTimestamps submission before implementation. Permit deployment and owner acceptance while Bitcoin anchoring is pending, display that state, and mark a proof fully verified only after successful anchoring. This replaces the earlier proposed rule requiring completed anchoring for release acceptance. |

Accepted choices have been reconciled into the PRD, SRS, architecture and provenance documents. Public repository preparation and GitHub account verification are complete as recorded in [M0 readiness](delivery-workflow.md#m0-readiness-record--2026-11-13). Next: prepare the exact baseline record and independent preservation destinations, then exercise and verify the selected signing flow when ready to begin implementation. No measured start event has been recorded. Provider choices, access policy details, content approvals and retention proposals remain scheduled for their relevant implementation slices.

## Budget and capacity update — 2026-10-12

Recommend the existing CPX31 for launch, with live capacity verification under U02. Keep web, Postgres, RabbitMQ and one Celery worker together; build artifacts in CI. Use hosted model inference. Run Kafka and security labs later, with separate resource review. See the [proposed resource envelope](architecture.md#initial-vps-resource-envelope-proposed).

The current [Hetzner US price table](https://docs.hetzner.com/general/infrastructure-and-availability/price-adjustment/) lists CPX31 at US$73.49/month excluding IPv4 and tax, compared with the previous US$24.99. This is not the owner's confirmed bill. Existing servers can retain legacy prices; [rescaling and some other actions can change that](https://docs.hetzner.com/cloud/billing/faq/#which-customer-actions-cause-servers-with-legacy-prices-to-switch-to-the-current-pricing). Check actual billing before recommending a resize or replacement.

The US$20–50 monthly target covers additional project spending; the existing Hetzner bill is outside it. Propose starting with US$10 for AI, up to US$5 for email/off-host backups/other services, and US$5 reserve. This targets the lower end of the range and leaves the remaining US$30 unallocated until actual need is demonstrated. These are spending allocations, not provider quotes or approved service caps. Newly enabled paid services count toward the additional budget. Report incremental project spend separately from full operating cost when the latter is known.

Include bounded live evaluations and ingestion model calls in the AI allowance, not only recruiter chat. Keep ordinary CI tests on provider fakes. Enforce the eventual AI cap in the application; provider billing alerts alone are not a hard spending limit. Paid email/telemetry plans are not assumed necessary until provider selection. Off-host, restorable backups remain required regardless of optional VPS snapshots. Hetzner's optional automatic backups cost 20% of the server price; include that cost only if selected ([billing reference](https://docs.hetzner.com/cloud/billing/faq/)).

## Hosting tradeoff

| Option | Useful value | Cost / responsibility | Fit for this build |
|---|---|---|---|
| Existing Hetzner VPS | CPX31 inventory and reported light usage fit the proposed launch envelope; Node, Python, RabbitMQ and Postgres run together | Existing bill outside additional project budget; owner patches host, protects ingress, backs up and restores data; one host is a shared failure domain | Recommended; verify live headroom before deployment (U02). |
| Managed containers + managed Postgres | Provider handles more deployment and database operations; web and worker can be sized independently | Multiple paid services; still need RabbitMQ placement, backups policy, and operational checks | Best alternative if reducing host administration outweighs incremental spend. Render background workers illustrate the service model. |
| Functions / edge runtime for web and chat | Managed HTTP ingress, elasticity, and potential scale-to-zero savings at light traffic | Runtime/lifecycle constraints; remote database connections; Python Celery worker and RabbitMQ still need compatible hosting or an architectural change | Useful for web delivery, but not a drop-in replacement for the whole chosen stack. |
| Hybrid edge/static delivery + container services | Fast cached public content while retaining the selected worker model | More deployment boundaries and configuration | Add where measurement or operational value warrants it. |

SvelteKit supports Node and Cloudflare adapters. Switching adapters does not guarantee all Node libraries or network integrations work unchanged. Managed workers support persistent queue consumers; function runtimes have different lifecycles and limits. These conclusions are architectural judgments based on the [primary hosting references](sources.md#hosting), not a priced procurement recommendation.

Do not add Kubernetes, a service mesh, Redis, or a standalone vector service merely to demonstrate tools. RabbitMQ is a task broker; Kafka's later purpose is replayable events, not a replacement for task execution or spending authorization.
