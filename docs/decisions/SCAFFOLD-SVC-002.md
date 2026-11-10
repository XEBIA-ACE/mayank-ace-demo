# SCAFFOLD-SVC-002 — Recorded team decisions
 
Story: SCAFFOLD-SVC-002 — BFF Integration Layer (`integration` platform)
Status: **Decided** — recorded by <name>, <YYYY-MM-DD>
Scope: these decisions resolve every item under "Unresolved Gaps — Disclose, Do Not Work Around"
in the SCAFFOLD-SVC-002 handoff (AGENTS.md). The implementing agent may proceed with T001–T017.
 
| Gap | Decision |
|-----|----------|
| 1. Architecture artefacts | Provided in this repository: `docs/architecture/SCAFFOLD-SVC-002.md` (C3/C4 view and API spec). |
| 2. GraphQL vs REST | **REST with Fastify** (Node.js 20, TypeScript). No Apollo Server / GraphQL. Health check is `GET /health`. |
| 3. Queue / cloud provider | **AWS** is the target cloud. Durable Message Queue = **AWS SQS** via `@aws-sdk/client-sqs`. No Azure Service Bus. Confirmed in `.env.example` (`CLOUD_PROVIDER=aws`, `QUEUE_BACKEND=sqs`). |
| 4. Secrets backend | **AWS Secrets Manager** via `@aws-sdk/client-secrets-manager`. No HashiCorp Vault. Confirmed in `.env.example` (`SECRETS_BACKEND=aws-secrets-manager`). |
 
Further decisions for the scaffold:
- Schema validation: **zod** (not joi).
- Linting/formatting: ESLint + Prettier.
- PostgreSQL and PostGIS: `pg` with connection pooling; PostGIS queries use the same `pg` driver.
- T017 (accessibility): this service exposes no user interface. Accessibility criteria are **not applicable**;
  record "N/A — backend scaffold, no UI" as the T017 result. This is not a gap.
- Everything else follows `specs/SCAFFOLD-SVC-002/` exactly. No business logic, no authentication
  middleware, no endpoints other than `GET /health`.
