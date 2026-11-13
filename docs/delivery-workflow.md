# Delivery workflow and divisions of work

Planning revision 0.6 · 2026-11-13 · M0 baseline draft and preservation plan prepared; baseline not yet signed. No project agents, custom skills, CI jobs or application code have been created.

## Smallest useful working arrangement

Use the four responsibilities below as bounded assignments. One agent may perform several sequentially. Parallel work is useful once interfaces and file ownership are settled; it is not a requirement to keep four agents running. The owner retains product decisions and release acceptance.

| Responsibility | Owns | Receives | Returns | Boundary |
|---|---|---|---|---|
| Architect / integrator | Requirement interpretation, architecture decisions, shared contracts, integration and handoff state | Work-item goal, constraints, owner decisions, current revision | Bounded work items; versioned interface/schema contracts; integrated result with acceptance evidence | Raises unresolved architectural tradeoffs to owner; does not silently enlarge scope or revise success criteria. |
| Backend / data engineer | Access and spending behavior, retrieval, ingestion, SQL changes, publication interfaces | Requirement IDs, contract version, owned paths, test conditions | Implementation, behavior tests, migration/recovery notes, remaining limitations | One designated migration owner per work item; cannot publish private source material. |
| Frontend / UI engineer | Public experience, evidence explorer, chat states, accessibility and client performance | Product brief, design constraints, API fixtures/contracts | Working UI, responsive/accessibility evidence, route/bundle measurements | Uses Impeccable contextually; does not invent unsupported résumé content or client-side authorization. |
| Verification / release reviewer | Independent requirement checks, failure exercises, browser review, release/proof validation | Exact candidate commit/build, criteria revision, implementation evidence | Findings tied to requirements, reproducible failures, verified release evidence or precise unmet criteria | Reviews the integrated revision; signer/CI checks supply cryptographic evidence, not the reviewer's prose. |

For complex database or visual work, the relevant role can become a narrower assignment. Do not add a permanent specialist merely because a technology exists in the stack. Broad architectural uncertainty returns to the integrator and owner before a changed contract is implemented.

## Skill policy

- Reuse available **Impeccable** for UI work. Record the selected version; keep the product requirement for restraint explicit.
- Consider **Supabase Postgres Best Practices** for database work, after reviewing and narrowing overly broad instructions for this project. It does not require hosted Supabase.
- Consider **Microsoft Playwright CLI** for browser investigations, traces and test work. Existing browser tools may already cover a task; installing another interface is not itself a deliverable.
- Use a concise architect role definition rather than a generic architecture textbook skill.
- Pin source revision, license and any local changes for adopted skills. Evaluate representative work on the selected GPT-6 model before claiming benefit. Do not treat a “GPT-6” label as demonstrated quality.

Possible custom skills, once the concrete workflows are established:

| Skill | Precise trigger | Useful project-specific content |
|---|---|---|
| `cjp-work-item` | Begin/resume/hand off a scoped implementation item | Requirement links, owner boundaries, contract locations and concise handoff shape. Omit generic coding instructions. |
| `cjp-content-publish` | Import/review/publish career evidence | Private/public boundary, evidence classes, approval records, publication command and recovery rules. |
| `cjp-milestone` | Prepare, verify or publish an agreed milestone | Record schema, expected signer policy, acceptance linkage, standard signing commands and pending-proof handling. |

Skills guide decisions. Scripts perform repeatable mechanics. CI enforces checks. Keep shared conventions in a short root `AGENTS.md` and read detailed docs only when relevant. Exact agent configuration and skill files are implementation deliverables after review, not an installed orchestration framework in this draft. [GPT-6 skill guidance](https://developers.openai.com/blog/rethinking-skills-and-prompts-for-gpt-6-astra)

## Repository and session continuity

Proposed layout after implementation begins:

```text
site/                         public repository root
  AGENTS.md                   concise working conventions
  docs/                       requirements, decisions, diagrams, handoffs
  apps/web/                   SvelteKit app
  packages/contracts/         public data/API/event schemas as needed
  pipeline/                   Python CLI and Celery tasks
  db/migrations/              one authoritative migration sequence
  content/                    reviewed, public-approved sources and claims
  tests/                      cross-component and end-to-end fixtures
  evals/                      versioned career-Q&A cases
  ops/                        deployment, backup and recovery commands
  .github/workflows/          GitHub Actions
```

This is a suggested arrangement, not a request to create empty directories. Private original PDFs, exports, candidate drafts, application materials, credentials and runtime data stay outside the public repository. Stage explicit paths; review every initial public commit for source permissions and sensitive data.

Use short-lived branches and isolated worktrees for parallel assignments. Agree ownership of files and migrations. Merge small complete slices; run integration verification after merging, not only inside each agent's branch. Do not rely on conversation history as the only record of a decision.

Each work item records: objective; requirement IDs; accepted/proposed decisions; dependencies; owned paths; interface version; success evidence; exclusions; current commit; status; next action. A handoff includes what changed, checks actually run and outcomes, known failures and unresolved decisions. Never label unrun checks as passed.

## Repeatable work loop

1. **Define:** choose a small vertical slice and observable success criteria. Settle cross-component interfaces before splitting work.
2. **Specify behavior:** write meaningful failing tests for access, accounting, pipeline state or other consequential behavior. A documentation change or trivial reversible UI edit does not require ceremonial TDD.
3. **Implement:** make the smallest change satisfying the agreed behavior, using official references and existing libraries where appropriate.
4. **Verify:** run affected checks; broaden only for integration boundaries, new failures or unresolved concerns. Use local provider fakes routinely and bounded live integration checks deliberately.
5. **Review:** independent reviewer examines the actual candidate revision and requirement evidence; implementer resolves actionable findings.
6. **Integrate:** merge, run relevant integrated checks, update contracts/decisions and record the revision.
7. **Witness when due:** freeze milestone evidence, sign and timestamp, verify, publish copies, then append owner acceptance for the same candidate.
8. **Hand off:** persist a concise status and next step so a new session can resume without reconstructing intent.

## Delivery chunks

| Milestone | Work | Exit evidence / dependencies |
|---|---|---|
| M0 — baseline | Establish public repo; commit approved planning state and prior-work inventory; manually witness baseline with existing tools | PROOF-01 and disclosed prior work. Sigstore proof verified and OTS submission retained before implementation; anchoring may be pending. No custom app/proof automation has been built yet. |
| M1 — working skeleton | Minimal SvelteKit page, local Postgres/Compose, CI, staging deployment, smoke check and first automated milestone flow | Repeatable clean build and deploy; baseline preserved; selected host capacity/ingress verified. Tests and source/artifact revisions visible. |
| M2 — evidence pipeline | Python CLI, RabbitMQ/Celery ingestion, private candidate review/export, corpus builder, signed publication, public evidence pages and GraphQL | PUB-02–04, KB-01–08, PROOF-05; crash/duplicate tests; approved initial content. Frontend may use agreed fixtures while pipeline is built. |
| M3 — protected Q&A | Mailbox verification, sessions/grants, atomic cost accounting, retrieval, bounded streaming, citations and observability | AUTH and AI requirements, metadata-only retention checks, relevant OBS checks and evaluated model. Budget/policy must be fixed. |
| M4 — first public release | Accessibility/performance review, security failure cases, restore/rollback, independent proof verification, owner acceptance | PRD-01–07 and launch SRS satisfied; Sigstore verified and OTS submitted. Pending anchoring is visible and does not block acceptance. All exceptions are explicit scope amendments. |
| M5 — Kafka | Sanitized versioned events, transactional outbox, consumers, replay/deduplication and bounded failure behavior | FUT-01; demonstrate before/after replay state; label single-broker lab limits. |
| M6 — transparent matching, optional | Public rubric, gap-aware evidence mapping, inspectable exports; decide whether to sign each result | FUT-02 and, only if selected, FUT-03. Product usefulness and trust claims reviewed before implementation. |

Checks-passed and owner-accepted records exist independently for each important milestone. If a milestone is split, change the versioned milestone plan and append a criteria amendment; keep the earlier scope visible.

Before M1 deployment, verify the existing CPX31's free RAM/disk and ports/ingress ownership, preserving any existing services. The owner reports no substantial workload and funds the existing Hetzner bill outside the additional US$20–50/month budget. Validate the [proposed resource envelope](architecture.md#initial-vps-resource-envelope-proposed). Keep production builds and test suites off the host, and check actual billing and pricing consequences before any VPS rescale. Read-only host discovery may happen during planning; implementation, including deployment automation and resource configuration, follows the witnessed M0 baseline.

Natural parallelism after M1: backend builds pipeline/data contracts while frontend builds public evidence pages using those fixtures; reviewer develops acceptance cases without duplicating implementation. Authentication/accounting work should have one owner at a time. Integration, migration sequencing and final release acceptance remain explicit responsibilities.

### M0 preparation and verification

The owner approved baseline policies on 2026-10-19. This approval date is not the measured implementation start. Complete preparation before witnessing the start, so account setup does not unnecessarily consume the measured build interval.

1. Verify access to `cjustinpearl/cjp.dev` and the personal signing flow. Install and record versions of the standard Cosign and OpenTimestamps clients. See the readiness record below for completed installation checks. Do not publish a trial project-start record.
2. Review the explicit `site/` public inventory, add the scoped MIT license/notices, initialize Git there and establish the selected public remote. Enable and verify immutable releases. Keep private parent-workspace inputs outside the repository. Pin the personal verifier identity/issuer; the exact CI workflow policy is needed before the first CI-signed milestone.
3. Prepare the concrete baseline record and referenced planning snapshot: stable project ID, commit/tree and file digests, approved requirements, disclosed prior planning/content/reuse and AI assistance. Identify the independent artifact copy destination and baseline digest publication location. Verify local implementation prerequisites before starting the clock; VPS discovery is read-only at this stage.
4. Freeze the reviewed bytes immediately before implementation. Sign with the selected Google identity, independently verify Cosign/Sigstore evidence, submit OpenTimestamps and retain its proof, then publish the immutable artifacts and independent copy/digest. Personal Google authentication requires the owner's interactive sign-in.
5. Verify the published downloads against the original digests and signer policy. Save the successful verifier output and proof status. M0 is complete when these checks pass; OTS anchoring may still be pending. Continue directly into M1 and preserve the original baseline unchanged.

Repository preparation is complete as recorded below. The [baseline draft and preservation plan](baseline/README.md) now bind a reviewed snapshot and disclose prior work; final freeze, signing and proof publication remain outstanding. Remaining access-policy, provider, content and retention choices stay attached to their implementation milestones; they do not require reopening accepted architecture decisions.

### M0 readiness record — 2026-11-13

| Item | Observed status |
|---|---|
| Cosign | Installed through Homebrew, version `v3.1.4`, macOS ARM64. `cosign version` and `cosign sign-blob --help` succeeded. |
| OpenTimestamps | Official PyPI package `opentimestamps-client==0.8.0`, installed in an isolated `uv tool` environment. `ots --version`, `ots --help`, and stamp/verify command help succeeded. |
| GitHub | Browser sign-in completed; authenticated `GET /user` returned `cjustinpearl` (account ID `124394950`). Created `cjustinpearl/cjp.dev`; GitHub reports `PUBLIC` visibility and `ADMIN` permission. |
| Local repository | Git root is `site/`, branch `main`, remote `https://github.com/cjustinpearl/cjp.dev.git`. The parent workspace has no Git metadata. Author identity and GitHub CLI credential helper are configured only in this repository; global Git settings were preserved. |
| Public inventory and licensing | Reviewed 15 regular text files: planning documents, four Mermaid diagrams, `.gitignore`, `LICENSE`, and `LICENSE-SCOPE.md`. No private career inputs, symlinks or unexpected files. Credential/private-path scan passed. MIT covers original software and associated technical documentation; career content, personal assets and third-party material are excluded. |
| Immutable releases | Enabled through GitHub's repository API; a subsequent read returned `enabled: true`. This is configuration verification, not a completed release or signed-proof test. |
| Baseline and proofs | An unsigned [baseline draft](baseline/baseline.draft.json) now identifies the project, original 15-file planning snapshot and SHA-256 inventory. Its start time is null; final freeze must include subsequent preparation. No signature, OpenTimestamps submission, tag or release created. |
| Independent preservation | Public B2 archive and 365-day compliance retention selected. API authentication succeeded, but creation returned `no_payment_history`; no project bucket was created or existing bucket changed. Sigstore is the selected external signed-digest publication service; no log entry exists yet. |
| Development readiness | Node `v26.7.0`, pnpm `11.25.0`, uv `0.12.5`, Python `3.14.7` are on PATH. Docker/Compose are not. These observations do not pin application versions or establish dependency compatibility. |

Installations used `brew install cosign` and `uv tool install opentimestamps-client`. Unrelated Homebrew upgrades/cleanup were disabled. Both commands are available on PATH. These are executable/readiness checks, not an end-to-end signing or Bitcoin-anchor verification. Personal Google authentication is deferred until the reviewed baseline is ready to sign. Sources: [Cosign installation](https://docs.sigstore.dev/cosign/system_config/installation/) and [OpenTimestamps client](https://github.com/opentimestamps/opentimestamps-client).

## Automation candidates

| Process | Automation | Evidence retained |
|---|---|---|
| Local development | One documented command for app/DB/broker/worker; disposable fixtures | Tool versions and readiness result. |
| Contract integrity | Validate API/data schemas and migration compatibility across TS/Python | Contract test result bound to commit. |
| Content release | Validate references, permissions metadata, digests, corpus build and evaluations | Manifest and approved source revisions. |
| Build/review | Formatting, types, relevant unit/integration/browser checks; dependency/secret checks | Machine-readable reports and build digest. |
| Deploy | Verify artifacts, reviewed migrations, readiness, compatible rollback | Deployment revision and health evidence. |
| Recovery | Encrypted off-host backup and scheduled disposable restore exercise | Restore outcome and measured recovery time. |
| Provenance | Assemble immutable record; sign; stamp; verify; copy; upgrade pending proof | Exact bytes, bundle, `.ots`, verifier output. |
| Session handoff | Produce concise status from current work item and actual results | Commit, decisions, unmet criteria and next step. |

Schedules and CI triggers will be configured during implementation; this document creates no recurring automation or externally published record.
