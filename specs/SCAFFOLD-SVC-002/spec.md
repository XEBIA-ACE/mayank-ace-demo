# Feature Specification: SCAFFOLD-SVC-002

## Story
**SCAFFOLD-SVC-002 — Scaffold BFF Integration Layer**

Scaffold the BFF Integration Layer (integration) per its approved architecture.

---

## User Scenarios & Testing

No user-facing scenarios are evidenced in the source artifacts for this scaffold story. This section is intentionally left without fabricated content — the story is a backend service scaffold with no UI flows or end-user journeys defined at this stage.

---

## Functional Requirements

### Technology Stack

The following technologies must be present in the scaffolded service:

| # | Technology | Role |
|---|-----------|------|
| 1 | Node.js 20+ / TypeScript | Runtime and language |
| 2 | GraphQL (Apollo Server) **or** REST (Fastify / Express) | API surface to Next.js Frontend Shell |
| 3 | Redis | Session cache, ERP stock cache, progressive profiling cache |
| 4 | AWS SQS / Azure Service Bus | Durable Message Queue client |
| 5 | JWT / HMAC-SHA256 | Gated content token generation and validation |
| 6 | OpenTelemetry | Distributed tracing with correlation IDs |
| 7 | Resilience4j-pattern circuit breakers (opossum or equivalent Node.js library) | Per-dependency circuit breaking |
| 8 | HashiCorp Vault / AWS Secrets Manager SDK | Runtime secret retrieval |
| 9 | PostgreSQL client | PII Isolated Data Store access |
| 10 | PostGIS / geospatial query client | Geospatial Dealer Data Store |
| 11 | AWS S3 SDK | Driver/Firmware Metadata Store queries |
| 12 | Zod / Joi | Request/response schema validation |
| 13 | Docker / Kubernetes | Containerised, horizontally scalable, stateless deployment |

### Service Responsibilities

1. Expose a GraphQL or well-structured REST API to the Next.js Frontend Shell as the single data surface.
2. Aggregate and orchestrate calls to Salesforce CRM, ERP, Support System, Tax Service, Payment Provider, and Marketing Automation Platform.
3. Validate CIAM OIDC tokens (JWT signature verification against CIAM JWKS) on all authenticated endpoints.
4. Validate Entra ID tokens on all staff/admin endpoints; enforce strict separation — no endpoint accepts tokens from both providers.
5. Implement exponential back-off retry (1 m / 5 m / 30 m) and dead-letter handling for all outbound integrations.
6. Publish enriched lead payloads to the Durable Message Queue for guaranteed async delivery to Salesforce CRM and Marketing Automation.
7. Enrich lead payloads with UTM parameters, source page, and product interest before queuing.
8. Execute lead routing algorithm using ZIP-code territory table (Geospatial Dealer Data Store) with industry-based override to assign dealer or direct sales rep.
9. Issue short-lived HMAC-SHA256 / JWT signed tokens encoding specific asset ID for gated content download access.
10. Validate gated content tokens server-side on every download request (signature, expiry, asset ID match).
11. Perform progressive profiling CRM contact lookup for returning visitors; cache results per session in Redis.
12. Implement server-side field suppression logic for progressive profiling forms.
13. Cache ERP stock availability data with 5-minute TTL in Redis to reduce ERP API call volume.
14. Read and write customer PII to the PII Isolated Data Store for authenticated account hub operations.
15. Query Geospatial Dealer Data Store for dealer locator radius searches and lead territory routing.
16. Query Driver/Firmware Metadata Store for support centre driver finder feature.
17. Proxy support ticket create, list, and detail operations to Support System for authenticated customers.
18. Enforce idempotency on all outbound integration calls using idempotency keys generated at submission time.
19. Maintain structured audit logs (with correlation IDs) for all lead routing decisions, support ticket operations, gated content access, and admin actions.
20. Check GPC signal server-side on every SSR request to enforce server-side consent for data collection.
21. Orchestrate DSAR deletion workflow across PII store, CRM, and Marketing Automation with status tracking.
22. Retrieve all downstream API credentials from Secrets Vault at runtime.
23. Implement circuit breakers per downstream service dependency to prevent cascade failures.
24. Implement request/response schema validation at BFF boundary.

> **Architecture grounding note:** The generated spec must be grounded in this service's own C3/C4 diagrams and API spec already on file in Architecture, not just this summary.

---

## Success Criteria

- **AC-1**: Scaffold the BFF Integration Layer (integration) per its approved architecture. Technology stack: Node.js 20+ / TypeScript, GraphQL (Apollo Server) or REST (Fastify / Express), Redis (session cache, ERP stock cache, progressive profiling cache), AWS SQS / Azure Service Bus (Durable Message Queue client), JWT / HMAC-SHA256 (gated content token generation and validation), OpenTelemetry (distributed tracing with correlation IDs), Resilience4j-pattern circuit breakers (opossum or equivalent Node.js library), HashiCorp Vault / AWS Secrets Manager SDK (runtime secret retrieval), PostgreSQL client (PII Isolated Data Store access), PostGIS / geospatial query client (Geospatial Dealer Data Store), AWS S3 SDK (Driver/Firmware Metadata Store queries), Zod / Joi (request/response schema validation), Docker / Kubernetes (containerised, horizontally scalable, stateless deployment). Responsibilities: Expose a GraphQL or well-structured REST API to the Next.js Frontend Shell as the single data surface; Aggregate and orchestrate calls to Salesforce CRM, ERP, Support System, Tax Service, Payment Provider, and Marketing Automation Platform; Validate CIAM OIDC tokens (JWT signature verification against CIAM JWKS) on all authenticated endpoints; Validate Entra ID tokens on all staff/admin endpoints; enforce strict separation — no endpoint accepts tokens from both providers; Implement exponential back-off retry (1m/5m/30m) and dead-letter handling for all outbound integrations; Publish enriched lead payloads to the Durable Message Queue for guaranteed async delivery to Salesforce CRM and Marketing Automation; Enrich lead payloads with UTM parameters, source page, and product interest before queuing; Execute lead routing algorithm using ZIP-code territory table (Geospatial Dealer Data Store) with industry-based override to assign dealer or direct sales rep; Issue short-lived HMAC-SHA256 / JWT signed tokens encoding specific asset ID for gated content download access; Validate gated content tokens server-side on every download request (signature, expiry, asset ID match); Perform progressive profiling CRM contact lookup for returning visitors; cache results per session in Redis; Implement server-side field suppression logic for progressive profiling forms; Cache ERP stock availability data with 5-minute TTL in Redis to reduce ERP API call volume; Read and write customer PII to the PII Isolated Data Store for authenticated account hub operations; Query Geospatial Dealer Data Store for dealer locator radius searches and lead territory routing; Query Driver/Firmware Metadata Store for support centre driver finder feature; Proxy support ticket create, list, and detail operations to Support System for authenticated customers; Enforce idempotency on all outbound integration calls using idempotency keys generated at submission time; Maintain structured audit logs (with correlation IDs) for all lead routing decisions, support ticket operations, gated content access, and admin actions; Check GPC signal server-side on every SSR request to enforce server-side consent for data collection; Orchestrate DSAR deletion workflow across PII store, CRM, and Marketing Automation with status tracking; Retrieve all downstream API credentials from Secrets Vault at runtime; Implement circuit breakers per downstream service dependency to prevent cascade failures; Implement request/response schema validation at BFF boundary. Ground the generated spec in this service's own C3/C4 diagrams and API spec already on file in Architecture, not just this summary.

---

## Security & Quality Obligations

- Validate credential and personal-data handling against the story's acceptance criteria.
- Verify the changed user flow against the stated accessibility criteria.

---

## Contract Changes

No material API, event, or data contract change is evidenced in the source artifacts for this scaffold story.

---

## Provenance

- **Story ID**: `SCAFFOLD-SVC-002`
- **Run ID**: `30fc8bf5-a529-46cb-92d9-9e4bcc341dff`
- **Project ID**: `61393f69-dfc8-40ff-8342-28015c6b565c`
- **Policy version**: `ace-spec-format/v1`
- **Source hash**: `sha256:f4d11c5f042c9f959e56aaf92639fa8a009ed5668827fcec09a603f9f5a08d43`
- **Accepted impact hash**: `null` (no impact analysis revision accepted at this stage)
- **Traceability**: AC-1 → `spec_functional`
