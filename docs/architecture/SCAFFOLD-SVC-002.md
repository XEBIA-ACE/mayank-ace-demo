# SCAFFOLD-SVC-002 — BFF Integration Layer: architecture (scaffold scope)
 
Source: Ace Architecture for project "Ricoh USA Sample Website", service SCAFFOLD-SVC-002.
This is the scaffold-level view the implementing agent needs; it adds no requirements beyond
`specs/SCAFFOLD-SVC-002/`.
 
## C3 — Container
- **BFF Integration Layer** (this repo): Node.js 20, TypeScript, Fastify (REST). Stateless; runs on Kubernetes,
  horizontally scaled.
- Depends on:
  - PostgreSQL (PII Isolated Data Store)
  - PostGIS (Geospatial Dealer Data Store)
  - Redis (session, ERP stock and profiling caches)
  - AWS SQS (Durable Message Queue)
  - AWS Secrets Manager (runtime credentials)
  - AWS S3 (Driver/Firmware Metadata Store)
  - OpenTelemetry collector
 
## C4 — Components (one module each, per tasks T002–T012)
| Component | Module | Task |
|-----------|--------|------|
| Config | `src/config.ts` | T002 |
| Health route | `src/routes/health.ts` (`GET /health`) | T003 |
| PostgreSQL client | `src/db/postgres.ts` | T004 |
| Redis client | `src/cache/redis.ts` | T005 |
| Queue client (SQS) | `src/queue/dmq.ts` (`publish(payload)` stub) | T006 |
| Secrets client | `src/secrets/vault.ts` (AWS Secrets Manager) | T007 |
| Telemetry | `src/telemetry.ts` | T008 |
| Circuit breaker | `src/resilience/circuitBreaker.ts` | T009 |
| Validation | `src/middleware/validate.ts` (zod) | T010 |
| Geospatial client | `src/db/geospatial.ts` | T011 |
| S3 client | `src/storage/s3.ts` | T012 |
 
## API spec (scaffold — no contract changes)
```yaml
openapi: 3.0.3
info: { title: BFF Integration Layer, version: 0.1.0 }
paths:
  /health:
    get:
      summary: Liveness and readiness
      responses:
        "200":
          description: Service is up
          content:
            application/json:
              schema:
                type: object
                properties:
                  status: { type: string, example: ok }
                required: [status]
