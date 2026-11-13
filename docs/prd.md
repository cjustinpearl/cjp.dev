# Project requirements document / PRD

Planning revision 0.3 · 2026-10-19 · release contract approved; operational policy details remain open as listed in [decisions](decisions.md).

## Product purpose

cjp.dev should let a recruiter quickly understand Justin's experience, personal contributions, and engineering judgment. The site itself should supply inspectable examples of product ownership, architecture, security, performance, observability, testing, and delivery. AI provides optional follow-up grounded in reviewed evidence.

The engineering story must distinguish an implemented capability from a lab, an inferred skill from a documented contribution, and a signed record from proof that its contents are true.

## Audiences and outcomes

| Audience | Need | Intended outcome |
|---|---|---|
| Recruiter | Establish relevance quickly | Find role history, selected outcomes, résumé and contact without registration. |
| Hiring manager / interviewer | Examine depth and judgment | Follow contribution → project → source, inspect tradeoffs, ask focused questions. |
| Technical reviewer | Verify engineering claims | Inspect public source, tests, architecture, pipeline demonstrations and verifiable delivery records. |
| Owner | Maintain evidence with little friction | Import source material through CLI, review it, and publish through Git. |

## First public release

Launch content includes one publication-approved résumé and two approved case studies. Their exact text and source permissions must be reviewed before publication.

| Product requirement | Capability | Acceptance evidence |
|---|---|---|
| PRD-01 | Fast, accessible public résumé and selected work | Mobile/desktop review, keyboard checks, content review, measured page performance. |
| PRD-02 | Evidence explorer connecting skills, contributions, projects and sources | Read-only GraphQL behavior; stable citations; source type and uncertainty visible. |
| PRD-03 | Email-gated career Q&A with bounded cost | Allowlist verification, expiring grant, concurrency/cost tests, clear unavailable states. |
| PRD-04 | Reviewed ingestion from Markdown, text, text PDFs and web pages | RabbitMQ/Celery job lifecycle, retries, duplicate delivery, failed-document handling, explicit publication review. |
| PRD-05 | Inspectable answer provenance | Corpus revision, cited passages, skill links, measured processing stages, limitations. |
| PRD-06 | Verifiable project history and signed knowledge publications | Baseline and milestone bundles, separate checks/acceptance records, Sigstore verification, OpenTimestamps proofs and pending states. |
| PRD-07 | Repeatable, recoverable delivery | CI, repeatable builds, database migration checks, backup/restore drill, rollback evidence and handoffs. |

Suggested information architecture: Experience, Selected work, Résumé, How this site works, Contact. Suggested questions can have reviewed public answers so visitors obtain immediate value before deciding whether to request AI access.

Keep observability details behind an optional “Inspect answer” disclosure. Show sources, stages, timing and versions; do not expose hidden model reasoning, secrets, raw prompts, email addresses, or infrastructure internals. Use restrained typography, spacing and motion with Impeccable; design work follows the product brief.

## Principal-level skills to demonstrate

| Skill | Observable evidence |
|---|---|
| Product judgment | Public information works without chat; scope and hosting tradeoffs are explicit. |
| Architecture and ownership | Clear state ownership, integration contracts, failure behavior, and migration/deployment process. |
| Messaging and pipelines | Idempotent tasks, bounded retries, recovery from worker failure; later Kafka replay. |
| Security | Authentication separated from entitlement; constrained ingestion; cost limits; least-privilege data access. |
| Data and AI quality | Reviewed claims, source lineage, unsupported-question behavior, regression evaluation. |
| Observability and performance | Real stage timing, correlated private traces, measured budgets and defined test conditions. |
| Delivery and TDD | Behavior-first tests for meaningful requirements; reproducible test evidence bound to release revision. |
| Communication and leadership | Case studies identify personal role, team context, decisions and limits without inflating credit. |
| Integrity | Independent verification of record bytes and signing identity; honest explanation of what remains an assertion. |

## Explicitly later

- Kafka event stream, consumers, replay/deduplication and recovery demonstration.
- Job-description-to-experience matching with public rubric and inspectable evidence coverage. No opaque “fit percentage” at launch.
- Optional Wazuh / Shuffle security labs, isolated from the public site; no dependency on security-operations deployment.
- Semantic retrieval if evaluations show meaningful benefit over full-text search.
- Per-result cryptographic signatures if their value, privacy model and verification audience are agreed.

No content administration UI, model training on the résumé, live web browsing during recruiter chat, multi-tenant product, autonomous external actions, or high-availability cluster is required for launch.

## User journeys

1. **Anonymous visitor:** open résumé → scan selected outcomes → inspect a project/source → download résumé or contact Justin.
2. **Authorized visitor:** submit company email → receive short-lived link → deliberately redeem → see allowance → ask question → read cited answer → inspect evidence/timing.
3. **Unsupported question:** receive a concise explanation of missing evidence and relevant known information; no invented experience.
4. **Owner publishing:** submit private source inputs via CLI → inspect candidate output and failures → approve public text/excerpts → commit approved content → validate/sign corpus → publish verified version.
5. **Independent verification:** download public manifest, artifacts and proof bundles → verify expected signer and hashes with published instructions → distinguish cryptographic success, timestamp status and content assertions.

## Success and release rule

Proposed usability check: a reviewer can identify Justin's current positioning, two relevant contributions, and a contact route within one minute on a phone, without chat. This is a product test, not a measured result yet.

Release requires PRD-01 through PRD-07 and their mapped launch SRS requirements to pass or receive an explicit, recorded scope amendment before acceptance. The model first-token p95 ≤3-second objective must be measured and reported but does not independently block release. Kafka and matching are separate milestones. The public timeline identifies exact accepted criteria and revisions, allowing changed scope to be inspected.

Required records must have verified Sigstore evidence and submitted OpenTimestamps proofs. Deployment and owner acceptance may proceed with Bitcoin anchoring pending, visibly labeled; fully verified proof status requires successful anchoring. Preserve checks-passed, deployment and owner-acceptance events separately.

Availability and quality take priority over a favorable speed claim. Record elapsed time between externally witnessed baseline and release acceptance; do not present this as proven labor hours, proof of no earlier work, or proof of prior unfamiliarity.

## Dependencies and remaining constraints

The owner supplies publication permission and policy choices. The budget target is approximately US$20–50/month in additional spending, excluding the existing Hetzner bill. An existing CPX31 (4 vCPU, 8 GB RAM, 160 GB disk, us-west, Ubuntu 24.04.4 LTS) is selected for launch, with no substantial current workload reported. Verify live host headroom before deployment. Third-party email/model/edge/signing services can fail. Public résumé content remains useful when AI, ingestion, or timestamp services are unavailable. Detailed acceptance checks and remaining policy proposals are in the [SRS](srs.md); implementation sequence is in the [delivery workflow](delivery-workflow.md).
