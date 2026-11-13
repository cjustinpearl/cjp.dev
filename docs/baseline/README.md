# M0 baseline preparation

Prepared 2026-11-13. **No start record has been signed or timestamped.**

The [draft record](baseline.draft.json) gives this project the stable ID `urn:uuid:5b000c4f-6153-4f29-a5b3-9b0bd8f2a05a`. Keep that ID through later milestones and repository moves. The draft pins the 15-file planning snapshot at commit `8ec3af396dcf1ded49e1e69d4b1a4391b28c9389`, with a SHA-256 and byte length for each file. Its start time is deliberately null.

This is a review snapshot, not the final freeze. Before signing, refresh the commit, tree, inventory and criteria digests to include all subsequent preparation. Preserve the final record as a release asset outside the source snapshot it describes. A commit cannot contain a record of its own final hash; a later commit may store the already-signed record without changing its meaning.

## What the record commits to

- The approved PRD-01–07 and launch SRS, including one résumé, two case studies and the reviewed Q&A evaluation. The three-second first-token objective requires reporting, not achievement as a release gate.
- Separate checks-passed, deployed and owner-accepted events. Changes to scope append amendments; they do not rewrite the baseline.
- Earlier planning, private career preparation, account/tool/repository setup and AI assistance. No private career files, paths, credential references or their hashes enter this public package.
- The expected personal signing identity and issuer, verified independently of whatever a signature bundle supplies.
- Verified Sigstore evidence and a retained OpenTimestamps submission before implementation; Bitcoin anchoring may remain pending and must be labeled accordingly.

Earlier planning dates are owner assertions. This record will establish an externally witnessed disclosed baseline, not the original moment of ideation, labor hours, prior unfamiliarity, or proof of no earlier work.

## Preservation locations

| Purpose | Location | State |
|---|---|---|
| Primary artifact publication | GitHub `cjustinpearl/cjp.dev`, planned release tag `m0-baseline` | Repository public; immutable releases rechecked enabled. No tag or release created. |
| Independent downloadable copy | Backblaze B2, dedicated public bucket `cjp-dev-public-evidence` | Provider and 365-day compliance retention accepted. Creation blocked by the account's public-bucket payment-history requirement; name not reserved. |
| Independently published signed digest | Sigstore public transparency log through Cosign | Accepted service; entry locator exists only after real signing. No trial start entry. |
| Independent existence-before anchor | OpenTimestamps calendar submission, later Bitcoin proof | Accepted service; no submission yet. Retain original and separately upgraded sidecars. |

B2 authentication succeeded using the owner-designated local credentials. The account advertises download origin `https://f005.backblazeb2.com` and S3 endpoint `https://s3.us-east-005.backblazeb2.com`. These are observed service endpoints, **not evidence that a project bucket or file exists**. No credentials are copied into this repository. No existing bucket was changed.

Once provisioned, use this object-key structure, with actual SHA-256 values:

```text
projects/5b000c4f-6153-4f29-a5b3-9b0bd8f2a05a/
  records/sha256/<baseline-digest>/<artifact-name>
  proofs/sha256/<baseline-digest>/<proof-digest>/baseline.json.ots
```

Capture the bucket ID, public origin, exact file/version IDs, hashes and retain-until dates in a publication receipt. By-name URLs and any `latest` index are discovery aids. Verify exact versions and the independently obtained baseline digest; an administrator could upload another version under the same name or change public access. Rekor establishes an external signed-digest entry, but does not select a unique authoritative project baseline or prevent an owner publishing competing records. Preserve and distribute the project-ID/digest pair with its log locator.

The selected B2 policy is compliance mode for **365 days per object version**, with no automatic deletion lifecycle rule. Retention may be extended before expiry. This protects stored versions during the retention period; continued availability still depends on account/service operation. Plan retention review during implementation; no recurring task has been created. Keep private operational backups in a separate private destination with their own policy. [Object Lock](https://www.backblaze.com/docs/cloud-storage-object-lock)

Current B2 pricing includes the first 10 GB of stored data; paid storage starts at $6.95/TB/month, with egress charges beyond the published allowance. The current public planning snapshot is about 114 KB, so its storage is small, but no blanket zero-cost guarantee is made for the account or downloads. Creating a first public bucket requires verified email and payment history or a small credited payment. Existing account-wide caps must not be changed for this project without review. [Pricing](https://www.backblaze.com/cloud-storage/pricing) · [Public-bucket prerequisites](https://www.backblaze.com/docs/cloud-storage-create-and-manage-buckets)

## Final artifact set

| Artifact | Purpose |
|---|---|
| `baseline.json` | Final exact bytes, including final source/archive digests, approved criteria and disclosed preparation. |
| `planning-source.tar` | Explicit `git archive` of the final reviewed planning commit, including preparation documents. No parent-directory traversal or untracked files. |
| `baseline.sigstore.json` | Cosign bundle containing signature, certificate and public-log evidence. |
| `baseline.json.ots` | Original submitted OpenTimestamps proof; pending is a valid initial state. |
| `VERIFY.md` | Standalone verification instructions, expected identity/issuer and prerequisites copied from the reviewed plan. |
| `SHA256SUMS` | Download checksums for the core files; a convenience, not a replacement for the signed record and external digest. Excludes itself. |
| `publication.json` | Core artifact locations/version IDs and retention observations. Does not include its own hash or claim that a locator authenticates content. |

Save verifier outputs after checking the final published downloads. Reports and later upgraded proofs are separate additional artifacts; do not attempt to append to or replace assets in an already-locked GitHub release. The original bundle and record remain available. A report saying “verified” is not a substitute for rerunning verification.

## Freeze and publish procedure

1. Resolve the storage account prerequisite, create only the dedicated bucket, read back compliance/365-day defaults and verify a small explicitly labeled setup file by unauthenticated download and per-object retention metadata. This is a storage check, not a trial baseline or milestone.
2. Resolve local development readiness, review the public inventory, commit preparation and require a clean worktree. Record the final commit and tree. Export its tracked files with standard `git archive`; hash those bytes and all inventory files. No reusable provenance automation is built before the baseline.
3. Produce `baseline.json` outside that snapshot from the reviewed draft. Replace draft-only fields, proposed destinations and unresolved freeze tasks with actual values; set the current asserted freeze time. Do not insert guessed future signing times, log IDs or Bitcoin dates. Review exact bytes and public disclosure before signing.
4. Use the installed tools and personal Google account. The commands below are instructions, **not actions already performed**. Select Google through the Sigstore browser flow; ambient CI/cloud identities are disabled.

   ```sh
   cosign sign-blob --oidc-disable-ambient-providers \
     --fulcio-auth-flow normal \
     --bundle baseline.sigstore.json baseline.json

   cosign verify-blob --bundle baseline.sigstore.json \
     --certificate-identity 'c.justin.pearl@gmail.com' \
     --certificate-oidc-issuer 'https://oauth2.sigstore.dev/auth' \
     baseline.json

   ots stamp baseline.json
   ots info baseline.json.ots
   ```

5. Save the verified log identity, entry/index locator, inclusion evidence and any timestamp-authority evidence from the actual bundle. Cosign versions may use different Rekor generations: v1 log time and a v2 timestamp-authority time are not interchangeable. Use the timestamp type actually verified; never treat a local JSON date as independent time evidence. [Sigstore timestamps](https://docs.sigstore.dev/cosign/verifying/timestamps/)
6. Assemble core assets; stage the GitHub draft release against the exact planning commit; upload copies to B2; record core file/version locators. Check hashes and retention. Include the receipt and verification instructions before publishing the immutable GitHub release. Check the published release's immutability, not only the repository setting.
7. Download from GitHub and independently from B2 without using the local files as the alleged download. Compare bytes and hashes; rerun Cosign with the pinned policy; verify archive inventory and the OTS proof's commitment to the record. Save results. Only then close M0 and begin M1.
8. Later upgrade a **copy** of the `.ots` sidecar and preserve both versions. The Python client's full Bitcoin verification requires suitable Bitcoin Core data/RPC access; that backend is still to be selected. A calendar response alone must not be labeled a verified Bitcoin anchor. [OpenTimestamps client](https://github.com/opentimestamps/opentimestamps-client)

Standard trust roots must be authenticated through the verifier's trust distribution; a downloaded root supplied beside an artifact is not automatically trusted. Preserve useful public trust material and tool versions for future verification without weakening that policy. See the broader [provenance policy](../provenance.md).

## Readiness and handoff

Observed on 2026-11-13: Cosign `v3.1.4`, OpenTimestamps client `0.8.0`, Node `v26.7.0`, pnpm `11.25.0`, uv `0.12.5` and Python `3.14.7` are available. These are observed installations, not approved application runtime pins. Docker and Docker Compose are not on PATH; installation or an alternative local container setup remains to be resolved. No application compatibility/build test or VPS inspection has been performed.

Preparation succeeds when the draft's snapshot hashes and criteria references verify, privacy review passes, and actual preservation locations have a verified upload/download path. Baseline witnessing succeeds only after the final signing/publication checks above. Current remaining work: resolve B2 public-bucket eligibility, provision/check storage, resolve local containers, refresh/freeze the final record, then personal signing and proof publication. CI identity pinning remains required before the first CI-signed milestone.
