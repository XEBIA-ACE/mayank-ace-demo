# Implementation Plan: SCAFFOLD-SVC-001

## Technical Context

**Declared stack (from implementation constraints):**
- Next.js 14+ (App Router)
- React 18
- TypeScript
- Tailwind CSS / CSS Modules
- Shared Design System Component Library (internal React package)
- SWR / React Query (server-state management)
- React Context (lightweight client state for cart and consent)
- Webpack / Turbopack
- Next.js Bundle Analyzer (CI enforcement)
- OpenTelemetry (client-side RUM instrumentation)
- Vercel / AWS (hosting origin)

**Platform:** backend

> **Note:** The implementation constraints list a Next.js/React stack, while the acceptance criterion (AC-1) and functional behavior describe a COBOL/CICS/DB2/VSAM mainframe service. This discrepancy is carried forward verbatim from the approved artifacts; resolution is deferred to the implementing team, who must ground their work in the C3/C4 diagrams and API spec on file in Architecture.

---

## Scaffold Steps (from implementation constraints)

1. Set up the project skeleton for this stack: dependency manifest, entrypoint, and linting/formatting config.
2. Add environment-based configuration loading (no hardcoded secrets or endpoints).
3. Add a health-check endpoint so the service can be verified as running locally.

---

## Contract Changes

- No material API, event, or data contract change is evidenced in the approved artifacts.

---

## Security and Quality

- **Security:** Validate credential and personal-data handling against the story's acceptance criteria.
- **Quality:** The scaffolded service must start successfully and its health-check endpoint must respond, using only environment-based configuration.

---

## Verification Plan

| Criterion | Obligation |
|-----------|------------|
| **AC-1** | Scaffold the Customer Record Creation Service (business) per its approved architecture. Technology stack: COBOL (primary implementation language for LGACDB01, LGACDB02, LGACVS01), IBM CICS TS for z/OS (transaction processing runtime, CICS LINK, CICS SYNCPOINT, CICS START), IBM DB2 for z/OS with Embedded SQL (EXEC SQL INSERT for CUSTOMER and CUSTOMER_SECURE tables), CICS DB2 Attachment Facility (DB2 thread pool and UoW coordination), VSAM KSDS with CICS File Control (EXEC CICS WRITE FILE for customer dataset), CICS COMMAREA / LINKAGE SECTION (inter-program data passing interface), IBM RACF (transaction ID and resource authorization enforcement), CICSPlex SM (horizontal scaling and workload routing across CICS regions), IBM z/OS Connect EE (future-state RESTful API exposure — configured, not coded), IBM MQ for z/OS via CICS MQ Bridge (future-state CustomerCreated event publishing). Responsibilities: Accept inbound customer personal details (name, address, date of birth, caller ID) via CICS COMMAREA/LINKAGE interface (LGACDB01 façade entry point); Enforce a single, stable, versioned COMMAREA interface contract as the sole API boundary for all callers — no caller may bypass the façade; Perform all input validation and business rule checks before any data store write (fail-fast principle); Compute BD5 checksum default security password for CUSTOMER_SECURE insertion; Orchestrate the full customer creation workflow: validate → write DB2 CUSTOMER → write DB2 CUSTOMER_SECURE → write VSAM KSDS → CICS SYNCPOINT commit; Delegate DB2 INSERT operations (CUSTOMER and CUSTOMER_SECURE tables) to LGACDB02 via CICS LINK within the active Unit of Work; Delegate VSAM KSDS WRITE operations to LGACVS01 via CICS LINK within the same Unit of Work; Issue CICS SYNCPOINT to commit or SYNCPOINT ROLLBACK to abort the Unit of Work atomically across DB2 and VSAM; Write audit records (timestamp, caller program ID, RACF user ID, customer ID, outcome/reason code) in a separate CICS UoW via CICS START to ensure persistence regardless of main transaction outcome; Return structured success/failure response codes and status fields to the calling program via COMMAREA; Enforce RACF authorization on the LGACDB01 transaction ID — only authorized callers and user IDs may invoke customer creation; Implement retry logic with explicit limits for transient DB2 and VSAM errors (deadlock, NOSPACE) with idempotency checks to prevent duplicate records; Support future exposure as a RESTful API endpoint via IBM z/OS Connect EE without modification to core COBOL logic; Support future CustomerCreated event publishing to IBM MQ after successful CICS SYNCPOINT commit. Ground the generated spec in this service's own C3/C4 diagrams and API spec already on file in Architecture, not just this summary. |

**Expected evidence:** test result for AC-1

---

## Provenance

- **Policy version:** `ace-spec-format/v1`
- **Accepted impact hash:** *(none recorded)*
- **Accepted impact revision:** *(none recorded)*
- **Traceability:** AC-1 → `spec_functional`
