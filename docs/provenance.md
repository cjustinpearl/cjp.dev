# Evidence, timestamps and future transparent matching

Planning revision 0.5 · 2026-11-13 · baseline draft prepared; public repository and immutable-release setting verified; no baseline signed. B2 archive setup was verified on 2026-11-23. The exact CI workflow/ref and signed baseline remain outstanding.

## What the design can establish

Cosign supports signing ordinary files, including a JSON corpus manifest or approved summary. Verification can establish that the exact bytes match a signature from an expected identity under an accepted issuer/trust policy. Public Sigstore evidence adds an externally recorded signing event. OpenTimestamps adds an independently verifiable existence-before anchor. [Cosign blobs](https://docs.sigstore.dev/cosign/signing/signing_with_blobs/) · [Sigstore security model](https://docs.sigstore.dev/about/security/) · [OpenTimestamps](https://opentimestamps.org/)

These mechanisms do not establish that a résumé claim is true, that an employer endorsed it, that a test suite is adequate, that a rubric is fair, or that the owner could not have started earlier. A host can sign an inflated score. Stronger evidence comes from inspectable original sources, honest attribution, a versioned rubric, independent execution or review, and explicit gaps.

| Displayed claim | Required evidence | Remaining limitation |
|---|---|---|
| “Signature verified” | Artifact digest, valid signature, expected signer/issuer, accepted trust roots and public-log proof | Identifies signer and bytes; not factual truth. |
| “Source captured / published” | URL or source identity, exact excerpt/snapshot digest, locator and capture/publication metadata | A site-signed snapshot is not an employer-signed document. |
| “Supported by cited passage” | Exact versioned passage and an inspectable mapping from claim to evidence | Entailment, context and source credibility still require judgment. |
| “Checks passed” | Test report digest, tested code/build/config and criteria revisions, signing workflow identity | Workflow and tests can be flawed or controlled by the owner. |
| “Owner accepted” | New acceptance record referencing checks record and owner approval event | Acceptance is a decision, not an independent quality certification. |
| “Externally witnessed by time T” | Verified external log/timestamp evidence with stated time semantics | Does not prove the object's exact creation time or no prior work. |

Use precise UI labels. Never collapse these into “truth verified” or “unbiased match certified.”

## Baseline and milestone records

Immediately before implementation, create a baseline with existing tools: off-the-shelf Cosign and OpenTimestamps clients. Do not implement custom project proof automation first and then call the later event the start of all implementation. Automating that workflow belongs to the measured build.

Baseline contents: stable project ID; approved scope/criteria digest; baseline Git commit/tree and artifact inventory; planning dates as owner assertions; existing content and reused tools; explicit exclusions; declared AI assistance. No private source documents or their raw text are submitted to a public log.

The [M0 preparation record](baseline/README.md) contains the concrete draft and storage setup state. Its review snapshot must be refreshed to the final committed preparation before signing. Final record bytes are an external release asset, so they do not attempt to contain their own Git commit hash.

Bind the owner-approved [prototype profile](srs.md#production-prototype-acceptance) and its criteria to the M0 snapshot. Prototype evidence links the exact evaluation report, running build, corpus, policy and relevant integration revisions. Each checks-passed, deployment and owner-acceptance record names the profile it covers. The timeline projects actual records and evidence status; a prototype milestone makes no claim of full-release acceptance.

A record is a versioned JSON file signed as **its exact saved bytes**. Hash referenced artifacts with SHA-256. Preserve those bytes; verification must not parse and reserialize before checking the signature. There is no need to invent a new signature or canonicalization scheme.

Illustrative contract, not an implemented schema:

```json
{
  "schema_version": 1,
  "project_id": "TBD-STABLE-ID",
  "kind": "checks-passed",
  "milestone_id": "M3",
  "previous_record_digest": "sha256:...",
  "criteria_digest": "sha256:...",
  "git_commit": "...",
  "build_digest": "sha256:...",
  "corpus_digest": "sha256:...",
  "artifacts": [{"name": "test-results.json", "sha256": "..."}],
  "asserted_event_time_utc": "...",
  "workflow_revision": "..."
}
```

Separate record types cover baseline, criteria amendment, checks-passed, deployed, owner-accepted, correction, corpus-published and release. Fields apply by type; do not invent a build digest for a planning baseline. Deployment records the actual activated build separately from checks and owner acceptance. Owner acceptance references the checks record and records the approval identity/event. It cannot silently accept a different build.

Signing sequence:

1. Produce and freeze record bytes plus referenced artifacts.
2. Sign using Cosign; capture bundle and verify expected identity/issuer and transparency evidence.
3. Submit the record digest through OpenTimestamps and retain the `.ots` proof. Initially it may be pending.
4. Copy record, artifacts, bundle and proof to independently accessible storage.
5. Upgrade/verify the OpenTimestamps proof as anchoring completes. Version proof sidecars; never rewrite signed record bytes to insert later timestamps.
6. Publish a read-only timeline projection with separate asserted event, Sigstore evidence, and OpenTimestamps anchor status.

Use the verified time evidence actually returned by the selected Cosign version. Rekor v1's integrated time and Rekor v2's separate timestamp-authority evidence have different semantics; public-log inclusion alone must not be presented as an independently validated wall-clock timestamp. Preserve the bundle and identify its timestamp source. [Sigstore timestamps](https://docs.sigstore.dev/cosign/verifying/timestamps/)

Accepted policy (2026-10-19): the baseline must have externally verified Sigstore evidence and an OpenTimestamps submission with retained proof before implementation starts. Deployment and owner acceptance may proceed while Bitcoin anchoring is pending. Display that state separately from checks-passed, deployment and acceptance times; label a proof fully verified only after its anchor verifies. Upgrade pending proofs later as separate versioned artifacts, preserving the original signed bytes.

## Trust and availability

### Selected identities

On 2026-10-14, the owner selected GitHub repository `cjustinpearl/cjp.dev` and Google account `c.justin.pearl@gmail.com` for personal signing. The public repository was created on 2026-11-13; administrator access and the immutable-release setting were verified. The personal signing flow remains unexercised. Use the Google account for the baseline and personal owner-acceptance records; the later CI signer has a separate policy.

For the standard Cosign browser flow through Sigstore's federated login, select Google and expect certificate identity `c.justin.pearl@gmail.com` with issuer `https://oauth2.sigstore.dev/auth`. Google login through that broker does not imply an issuer of `https://accounts.google.com`; direct Google OIDC is a distinct supported path. Pin the expected issuer for the deliberately selected flow before signing and verify the resulting bundle against it. A mismatch requires investigation, not automatic trust in the returned certificate. [Sigstore OIDC identities and issuers](https://docs.sigstore.dev/certificate_authority/oidc-in-fulcio/)

Selected automated signer: GitHub Actions in `cjustinpearl/cjp.dev`, issuer `https://token.actions.githubusercontent.com`, with an exact workflow path and approved ref pinned before use. Do not accept every workflow in the account. The workflow/ref are still TBD. [Cosign verification](https://docs.sigstore.dev/cosign/verifying/verify/)

### Verification and preservation policy

- Pin the expected baseline identity and later CI workflow identity/issuer in documented verifier policy. Never trust whatever signer an artifact supplies.
- Restrict release signing to the approved branch/workflow and owner acceptance path. Record workflow revision. Prefer short-lived CI OIDC identity over a shared long-lived signing secret for CI publications.
- Publish the stable project ID and baseline digest outside the mutable site. Predecessor links expose changes to a known chain, but alone cannot reveal a hidden fork, deleted tail or replaced baseline. Independent copies and observation of new records strengthen detection.
- Publish record/artifact bytes and proof bundles as GitHub immutable release assets, with immutability enabled and verified before publication. Keep an independent off-host copy. Release-page text and ordinary Git tags alone are insufficient; preservation still needs independent copies because a release can be deleted. Publish later proof upgrades as additional immutable artifacts rather than replacing locked assets. [GitHub immutable releases](https://docs.github.com/en/code-security/concepts/supply-chain-security/immutable-releases)
- Transparency services do not guarantee that every referenced file will remain downloadable. Record the independent copy destination and baseline digest publication location before closing M0.
- Selected independent artifact provider: a dedicated public Backblaze B2 bucket with 365-day compliance retention, separate from private operational backups. Preserve exact object-version IDs and digests. Provisioning, public-download verification and actual retain-until dates are tracked in the [preservation plan](baseline/README.md#preservation-locations). A retention window is not a promise of permanent availability.
- Corrections append a record referring to the superseded digest. Public log metadata is intended to persist; the owner has selected the personal identity above. Verify the configured sign-in flow before first submission.
- Standard verification still depends on trust roots and, for OpenTimestamps, suitable Bitcoin verification data. Supply documented tooling/prerequisites; do not promise arbitrary offline verification with only an HTML page.

The honest speed claim is “this disclosed baseline was externally witnessed, and this release met these recorded criteria by this later witnessed event.” Display elapsed calendar time between those records, disclose earlier planning/reuse, and distinguish any self-reported human effort. Prior ignorance is not a cryptographic property; a dated learning self-assessment and later demonstrations can document progress without claiming proof of it.

## Signed knowledge and approved summaries at launch

Each corpus release manifest binds source versions, permitted excerpts, structured claims, skill links, reviewed summaries and transformation configuration. One signed manifest can authenticate all included artifacts by digest. There is no need for a public-log submission per sentence or per chat token.

Use evidence classes such as:

- **Owner statement:** personal role or outcome supplied by Justin, with qualifications.
- **External corroboration:** a third-party source supports a specified claim; distinguish source publisher from the party signing the site's copy.
- **Company/project context:** background that does not itself prove personal involvement.
- **Derived summary:** reviewed synthesis linked to supporting passages and its limitations.

A signed public summary remains an owner-published synthesis. The answer inspector links the exact summary and passages to the corpus manifest and verification bundle. The application validates the corpus on import; visitors can independently verify downloaded artifacts. Launch chat responses may be unsigned while citing signed evidence, and must be labeled accordingly.

## Later job-description matching

Prefer a requirements-to-evidence table over a single score. For each job requirement, expose direct evidence, adjacent/transferable evidence, missing or conflicting evidence, and unknowns. Do not convert “unknown” into a favorable match. If an aggregate score is later introduced, publish its weights, exclusions and formula and allow independent recalculation.

Publish a rubric and code/configuration version before evaluating a submitted description. Bind the exact input, corpus and output to those versions. Signed provenance makes post-hoc changes detectable; it cannot prevent the owner from designing a favorable rubric in advance or selecting favorable evidence. Include negative test cases, gap reporting and independent review to address that risk.

Reserve these fields in the later assessment export contract:

| Field | Purpose |
|---|---|
| Input digest / private commitment | Bind the exact submitted job description without publishing its text. A bare hash does not ensure confidentiality for guessable inputs. |
| Corpus and source/claim digests | Identify the evidence available for this assessment. |
| Rubric, retrieval, prompt/config and application revisions | Make the method inspectable and changes visible. |
| Model identifier and relevant parameters | Record declared inference configuration; do not imply deterministic regeneration or provider attestation. |
| Per-requirement evidence/gaps and exact output digest | Inspect reasoning summaries and verify the returned bytes. No private chain-of-thought is required. |
| Signature bundle and signer policy | Authenticate the exported receipt if result signing is selected. |

A runtime assessment signer, private-result retention and public-log handling remain U08. Do not send job descriptions, recruiter identity, conversation text or private receipts to a public log or public CI job. The default extension direction is public signing of reusable corpus/rubric releases and private, visitor-held assessment exports. Decide per-result signing separately, including key management and whether independent execution is required.

An independent verifier can validate signatures, source membership and deterministic scoring calculations. It cannot establish that a compromised host actually used the declared model/configuration without stronger execution evidence, nor certify semantic truth. An independently rerun evaluation can strengthen confidence while allowing for model nondeterminism.

## Required verification exercises

Modify record date, summary text, source bytes, criteria digest and build digest independently: verification must fail in each case. Substitute a valid signature from the wrong identity: policy must reject it. Show correct pending/failed/verified proof states. Verify a copied release with the site offline. Append a correction and demonstrate that the original remains independently verifiable. Present a deliberately inaccurate but validly signed claim and ensure the UI still describes only its integrity as verified.
