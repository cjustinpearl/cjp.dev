# cjp.dev — living résumé

Public planning repository · updated 2026-11-13 · baseline not yet signed; no application implemented or deployed

A fast public portfolio with evidence-backed career Q&A, an asynchronous content pipeline, and independently verifiable publication and delivery records.

## Read first

1. [Decisions and open questions](docs/decisions.md) — accepted choices, recommendations, and unresolved limits.
2. [Project requirements / PRD](docs/prd.md) — audience, outcomes, scope, and release acceptance.
3. [Architecture](docs/architecture.md) — application, system, data, and integration Mermaid diagrams.
4. [Software requirements specification](docs/srs.md) — testable behavior and proposed nonfunctional targets.
5. [Evidence and provenance](docs/provenance.md) — milestones, signed knowledge, verification limits, and future transparent matching.
6. [Delivery workflow](docs/delivery-workflow.md) — work divisions, automation, skills, and session handoffs.
7. [Technical sources](docs/sources.md) — primary references checked while preparing this draft.

## Repository boundary

This directory is the public Git repository root, located locally at `site/`. Git is initialized here on `main`; the parent workspace is outside the repository and contains private career inputs and application materials. The initial public inventory consists of planning documents, Mermaid diagrams, licensing files, and ignore rules. Do not initialize the parent workspace as the public repository or import the parent corpus wholesale. Stage and review explicit paths before publishing; ignore rules are a safeguard, not publication approval.

Public repository: [cjustinpearl/cjp.dev](https://github.com/cjustinpearl/cjp.dev), created on 2026-11-13 with immutable releases enabled. Selected personal signing account: Google identity `c.justin.pearl@gmail.com`; its signing flow has not yet been exercised. The baseline recommendations were approved on 2026-10-19. See [baseline readiness](docs/decisions.md#baseline-readiness--2026-10-19) and the [M0 checklist](docs/delivery-workflow.md#m0-preparation-and-verification) for the remaining preparation and verification before signing.

Only individually reviewed, publication-approved career material belongs in the eventual public `content/` directory inside this repository. A source being useful for résumé preparation does not establish permission to publish it.

## License

Original software and associated technical documentation are [MIT licensed](LICENSE). Personal résumé/career content, personal assets, and third-party material are excluded from that grant; see [license scope and content exclusions](LICENSE-SCOPE.md). The exclusions apply to content wherever it appears, including material embedded in code. Public availability does not grant reuse permission for excluded content.

## Status notation

- **Accepted**: the owner selected the decision in conversation.
- **Proposed**: recommended for this draft; not yet accepted as a binding constraint.
- **TBD**: unresolved; the affected implementation or launch check must resolve it.
- **Later**: a defined extension outside the first public release.

The measured implementation start has **not** been recorded. These drafts are planning work. The baseline will be externally witnessed immediately before implementation, using existing signing tools, and will disclose the planning and content that already exist.
