# Primary technical references

Checked for this planning draft on 2026-10-09, except the skill shortlist researched on 2026-10-03 and the Hetzner/RabbitMQ sizing references added on 2026-10-12. Resolve exact supported versions and pin dependencies during implementation. These sources support capabilities and constraints; architectural choices remain project judgments.

## Hosting

- [Hetzner June 2026 price adjustment](https://docs.hetzner.com/general/infrastructure-and-availability/price-adjustment/) — current versus previous US CPX31 pricing; list prices do not establish the owner's actual bill, which is outside the additional project budget.
- [Hetzner billing FAQ](https://docs.hetzner.com/cloud/billing/faq/) — legacy-price changes, optional backup pricing, separate IP charges and billing-alert limitations.
- [SvelteKit Node adapter](https://svelte.dev/docs/kit/adapter-node) — standalone Node deployment.
- [SvelteKit Cloudflare adapter](https://svelte.dev/docs/kit/adapter-cloudflare) — alternative deployment target; review platform-specific dependencies.
- [Cloudflare Workers limits](https://developers.cloudflare.com/workers/platform/limits/) — runtime/resource boundaries; plan-dependent details must be verified before selecting a plan.
- [Render background workers](https://render.com/docs/background-workers) — persistent background services listening for queued work.
- [AWS Lambda timeout](https://docs.aws.amazon.com/lambda/latest/dg/configuration-timeout.html) — function invocation constraints illustrate why long-running consumer deployment requires a specific design. No Lambda selection is implied.

## Provenance

- [Cosign signing blobs](https://docs.sigstore.dev/cosign/signing/signing_with_blobs/) — ordinary file signing and verification bundles.
- [Cosign verification](https://docs.sigstore.dev/cosign/verifying/verify/) — identity/issuer checks and artifact verification.
- [Sigstore security model](https://docs.sigstore.dev/about/security/) — identity, transparency and trust assumptions.
- [Sigstore CI quickstart](https://docs.sigstore.dev/quickstart/quickstart-ci/) — CI identity-based signing; pin actions and verify expected workflow identity in the real implementation.
- [Cosign other artifact types](https://docs.sigstore.dev/cosign/signing/other_types/) — file attestations and structured metadata.
- [OpenTimestamps](https://opentimestamps.org/) — existence-before proofs, calendar submission and verification.

## Application and pipeline

- [GraphQL Yoga / SvelteKit integration](https://the-guild.dev/graphql/yoga-server/docs/integrations/integration-with-sveltekit) — one web application can host the GraphQL endpoint.
- [GraphQL security guidance](https://graphql.org/learn/security/) — demand controls, operation limits and authorization; consulted in earlier planning.
- [Better Auth magic links](https://better-auth.com/docs/plugins/magic-link) — token storage and atomic consumption behavior. Verify selected version and confirmation flow in a focused implementation spike.
- [Celery task guidance](https://docs.celeryq.dev/en/stable/userguide/tasks.html) — idempotence, acknowledgements, worker loss and bounded task execution.
- [RabbitMQ work queues](https://www.rabbitmq.com/tutorials/tutorial-two-python) — work distribution and acknowledgement concepts; consulted in earlier planning.
- [RabbitMQ memory alarms](https://www.rabbitmq.com/docs/memory) — explicit absolute threshold for containers; backpressure threshold is not a hard memory cap.
- [OpenTelemetry traces](https://opentelemetry.io/docs/concepts/signals/traces/) — portable tracing concepts; consulted in earlier planning.

## Skills and workflow

- [GPT-6 Astra skills guidance](https://developers.openai.com/blog/rethinking-skills-and-prompts-for-gpt-6-astra) — narrow triggers, relevant context and avoiding excessive procedural instruction.
- [Supabase Postgres skill](https://github.com/supabase/agent-skills/blob/main/skills/supabase-postgres-best-practices/SKILL.md) — candidate reference for Postgres schema/query work; not yet installed for this project.
- [Impeccable](https://github.com/pbakaus/impeccable) — existing UI/design skill candidate.
- [Microsoft Playwright CLI skill](https://github.com/microsoft/playwright-cli/blob/main/skills/playwright-cli/SKILL.md) — candidate browser automation/testing support.

Security tool evaluation (Wazuh/Shuffle) and Kafka operational configuration are deferred to their scoped milestones. No source here establishes GPT-6 performance of a third-party skill; evaluate actual tasks before making that claim.
