# Implementation Plan: US-001

> **⚠ DEGRADED NOTICE**: Generated context artifacts for SVC-003, SVC-004, and SVC-005 (artifact-SVC-003-c3, artifact-SVC-003-c4, artifact-SVC-003-api_spec, artifact-SVC-004-c3, artifact-SVC-004-c4, artifact-SVC-004-api_spec, artifact-SVC-005-c3, artifact-SVC-005-c4, artifact-SVC-005-api_spec) were auto-regenerated during gap resolution but resolved to empty strings. All contract change assessments are grounded in the service identification manifest and functional specification only; implementation teams must validate all design steps and contract definitions against the actual service implementations before execution.

## Technical Context — Unresolved References
The following references were unresolved at export time. Implementation teams must ground all decisions against the actual artifacts before execution:

- unresolved reference (service:SVC-003)
- unresolved reference (service:SVC-004)
- unresolved reference (service:SVC-005)
- unresolved reference (artifact-SVC-003-c3)
- unresolved reference (artifact-SVC-003-c4)
- unresolved reference (artifact-SVC-003-api_spec)
- unresolved reference (artifact-SVC-004-c3)
- unresolved reference (artifact-SVC-004-c4)
- unresolved reference (artifact-SVC-004-api_spec)
- unresolved reference (artifact-SVC-005-c3)
- unresolved reference (artifact-SVC-005-c4)
- unresolved reference (artifact-SVC-005-api_spec)
- unresolved reference (spec_functional:US-001)
- unresolved reference (spec_functional:FR-001)
- unresolved reference (spec_functional:FR-002)
- unresolved reference (spec_functional:FR-003)
- unresolved reference (spec_functional:FR-004)
- unresolved reference (spec_functional:FR-005)
- unresolved reference (spec_functional:FR-006)
- unresolved reference (spec_functional:FR-007)
- unresolved reference (acceptance_criterion:US-001-ac-1)
- unresolved reference (acceptance_criterion:US-001-ac-2)
- unresolved reference (acceptance_criterion:US-001-ac-3)
- unresolved reference (acceptance_criterion:US-001-ac-4)
- unresolved reference (acceptance_criterion:US-001-ac-5)
- unresolved reference (acceptance_criterion:US-001-ac-6)
- unresolved reference (acceptance_criterion:US-001-ac-7)
- unresolved reference (gap_resolution:artifact-SVC-003-c3:auto_generate)
- unresolved reference (gap_resolution:artifact-SVC-003-c4:auto_generate)
- unresolved reference (gap_resolution:artifact-SVC-003-api_spec:auto_generate)
- unresolved reference (gap_resolution:artifact-SVC-004-c3:auto_generate)
- unresolved reference (gap_resolution:artifact-SVC-004-c4:auto_generate)
- unresolved reference (gap_resolution:artifact-SVC-004-api_spec:auto_generate)
- unresolved reference (gap_resolution:artifact-SVC-005-c3:auto_generate)
- unresolved reference (gap_resolution:artifact-SVC-005-c4:auto_generate)
- unresolved reference (gap_resolution:artifact-SVC-005-api_spec:auto_generate)
- unresolved reference (run_id:0d7c0043-b335-40e4-8b58-98d0dabf7e32)
- unresolved reference (project_id:61393f69-dfc8-40ff-8342-28015c6b565c)
- unresolved reference (story_id:US-001)

## Implementation-Readiness Gaps (must be resolved or explicitly accepted before execution)

1. **DEGRADED NOTICE**: Generated context artifacts for SVC-003, SVC-004, and SVC-005 (artifact-SVC-003-c3, artifact-SVC-003-c4, artifact-SVC-003-api_spec, artifact-SVC-004-c3, artifact-SVC-004-c4, artifact-SVC-004-api_spec, artifact-SVC-005-c3, artifact-SVC-005-c4, artifact-SVC-005-api_spec) were auto-regenerated during gap resolution but resolved to empty strings. All contract change assessments are grounded in the service identification manifest and functional specification only; implementation teams must validate all design steps and contract definitions against the actual service implementations before execution.
2. The exact response schema for `GET /api/search/suggest?q=` (autocomplete) has not been defined in any available artifact; the contract shape (field names, types, maximum suggestion count) must be confirmed against the SRCH-01 provider's autocomplete API before implementation.
3. The maximum query-string length (cited as 512 chars in the design) and the exact special-character encoding rules for `GET /api/search?q=&facets=` have not been confirmed against SRCH-01 provider constraints; these values must be validated before the SearchQuery sanitisation contract is finalised.
4. The exact set of facet dimensions and their allowed values for the `facets{}` parameter on `GET /api/search?q=&facets=` have not been fully specified; the contract must enumerate all supported facet keys (e.g. category, productFamily, osCompatibility, contentType) and their value domains.
5. The category field values on ProductIndexRecord are specified as `devices | supplies | software | accessories` but the story description uses `devices/MFPs/printers` as a single category; the canonical enum values and their mapping from Commerce Platform product types must be confirmed before the SVC-005 data contract is finalised.
6. The `attributes{}` map structure on ProductIndexRecord (key names, value types, indexing strategy in SRCH-01) has not been specified in any available artifact; the contract must define which attribute keys are indexed and how they are tokenised for full-text matching to satisfy FR-004.
7. It is unconfirmed whether SRCH-01 supports a `contentTypes[]` (or equivalent) parameter to scope a single query across all four product categories simultaneously; this must be validated against the SRCH-01 provider API before the `GET /api/search?q=&facets=` contract is finalised.
8. The `hits[]` item schema within SearchResult has not been fully specified; in particular, the field name and type for the category label (e.g. `category`: string) must be contractually defined so the Frontend Shell can render it per US-001-ac-2.
9. SVC-004 (Async Queue Consumer Worker) has no direct scope in US-001 per the design, but its generated context artifacts also resolved to empty strings; any indirect dependency on SVC-004 (e.g. if a future story adds search-event analytics) should be re-evaluated when those artifacts are available.

## Impacted Services
- **SVC-003** — Backend-for-Frontend (BFF) Service
- **SVC-005** — Search Index Sync Worker
- **SVC-004** — Async Queue Consumer Worker (no direct US-001 scope; carried forward unchanged)

## Design Steps

1. **SVC-003** [FR-007, US-001-ac-7]: Implement input sanitisation/trimming middleware on the `GET /api/search?q=&facets=` handler — strip leading/trailing whitespace, enforce a maximum query-string length (e.g. 512 chars), and safely encode special characters before forwarding the processed value to SRCH-01. Evidence: service:SVC-003, artifact-SVC-003-api_spec.
2. **SVC-003** [FR-001, US-001-ac-1]: Ensure the `GET /api/search?q=&facets=` endpoint accepts a `contentTypes[]` (or equivalent facet) parameter that scopes the SRCH-01 query across all four product categories (devices/MFPs/printers, supplies, software, accessories) in a single request, returning a unified SearchResult payload without requiring a full page reload. Evidence: service:SVC-003, artifact-SVC-003-c3.
3. **SVC-003** [FR-002, US-001-ac-2]: Verify that the SearchResult `hits[]` items returned by the BFF include a category label field (sourced from the ProductIndexRecord.category field in SRCH-01) so the Frontend Shell can render a visible category label on each result card. Evidence: service:SVC-003, artifact-SVC-003-api_spec.
4. **SVC-003** [FR-003, US-001-ac-3]: Confirm the BFF passes through product name and position exactly as returned by SRCH-01 without reordering or mutating the name field in the SearchResult `hits[]` array. Evidence: service:SVC-003, artifact-SVC-003-c3.
5. **SVC-003** [FR-004, US-001-ac-4]: Confirm the BFF forwards the full SRCH-01 response including attribute-matched hits without suppressing or de-duplicating them; no BFF-side filtering that would remove attribute-matched products from the `hits[]` array. Evidence: service:SVC-003, artifact-SVC-003-c3.
6. **SVC-003** [FR-005, US-001-ac-5]: Ensure the BFF returns a well-formed SearchResult with `totalHits=0` and an empty `hits[]` array (not an error response) when SRCH-01 returns zero results, so the Frontend Shell can deterministically detect the empty-state condition and render the OD-02 copy message. Evidence: service:SVC-003, artifact-SVC-003-api_spec.
7. **SVC-005** [FR-002, FR-004, US-001-ac-2, US-001-ac-4]: Verify that the ProductIndexRecord schema in the Search Index Sync Worker includes a mandatory category field with values drawn from the four product categories (devices/MFPs/printers, supplies, software, accessories) and that the `attributes{}` map is fully indexed in SRCH-01 to enable attribute-value keyword matching. Evidence: service:SVC-005, artifact-SVC-005-c3.
8. **SVC-005** [FR-004, US-001-ac-4]: Confirm the Commerce Platform webhook handler (`POST /webhooks/commerce`) and the subsequent content-fetch and transform pipeline correctly populate `attributes{}` on ProductIndexRecord for all four product categories before pushing to SRCH-01 ingestion API. Evidence: service:SVC-005, artifact-SVC-005-c3, artifact-SVC-005-api_spec.
9. **SVC-005** [FR-001]: Confirm the full re-index job (`POST /admin/reindex`) and incremental IndexSyncJob pipeline cover all four product categories so that the search index is complete and consistent for cross-category queries. Evidence: service:SVC-005, artifact-SVC-005-c4.
10. **SVC-004** [no direct US-001 scope]: No changes required to the Async Queue Consumer Worker for US-001 (cross-category keyword search does not involve lead delivery or marketing automation event triggers). Service is carried forward unchanged. Evidence: service:SVC-004, artifact-SVC-004-c3.
11. **DEGRADED NOTICE**: Generated context artifacts for SVC-003, SVC-004, and SVC-005 (artifact-SVC-003-c3, artifact-SVC-003-c4, artifact-SVC-003-api_spec, artifact-SVC-004-c3, artifact-SVC-004-c4, artifact-SVC-004-api_spec, artifact-SVC-005-c3, artifact-SVC-005-c4, artifact-SVC-005-api_spec) were auto-regenerated during gap resolution but resolved to empty strings. Design steps are grounded in the service identification manifest (spec_service_identification) and functional specification (spec_functional). The design is marked degraded because provider-confirmed C3/C4/API-spec content is absent; implementation teams must validate all design steps against the actual service implementations before execution.

## Data Models
- `SearchQuery: { query, facets{}, page, pageSize, contentTypes[] }` — used by US-001; query field receives sanitised/trimmed keyword from Frontend Shell; contentTypes[] scopes search across all four product categories (devices/MFPs/printers, supplies, software, accessories)
- `SearchResult: { hits[], facetCounts{}, totalHits, queryTime }` — used by US-001; hits[] items must carry a category label field; totalHits=0 signals empty-state to Frontend Shell
- `ProductIndexRecord: { recordId, sku, name, description, category, attributes{}, specSheetUrl, price, availability, imageUrl }` — category field is mandatory for US-001 FR-002 category label rendering; attributes{} must be fully indexed to support US-001 FR-004 attribute-value matching
- `SearchIndexRecord: { recordId, contentType, title, description, url, facets{ category, productFamily, osCompatibility, contentType }, body, metadata{}, indexedAt }`
- `IndexSyncJob: { jobId, source, entityIds[], status, startedAt, completedAt, errorMessage }`

## Contract Changes

### SVC-003 — `GET /api/search?q=&facets=` (api_contract) — **CONTRACT CHANGE REQUIRED**
Cross-category search proxy handler. Accepts a sanitised/trimmed keyword (q), facet parameters, and a `contentTypes[]` parameter scoping the query across all four product categories (devices/MFPs/printers, supplies, software, accessories). Returns a SearchResult payload: `{ hits[], facetCounts{}, totalHits, queryTime }` where each hit carries a mandatory category label field. A `totalHits=0` response (not an error) signals the empty-state condition to the Frontend Shell. Input sanitisation (whitespace trimming, 512-char max, special-character encoding) is applied before forwarding to SRCH-01.
- Data models: `SearchQuery: { query, facets{}, page, pageSize, contentTypes[] }`, `SearchResult: { hits[], facetCounts{}, totalHits, queryTime }`
- Functional requirements: FR-001, FR-002, FR-003, FR-004, FR-005, FR-007
- Acceptance criteria: US-001-ac-1, US-001-ac-2, US-001-ac-3, US-001-ac-4, US-001-ac-5, US-001-ac-6, US-001-ac-7
- Evidence: service:SVC-003, artifact-SVC-003-api_spec, artifact-SVC-003-c3, spec_functional:US-001, spec_functional:FR-001, spec_functional:FR-002, spec_functional:FR-003, spec_functional:FR-004, spec_functional:FR-005, spec_functional:FR-007

### SVC-003 — `GET /api/search/suggest?q=` (api_contract) — **CONTRACT CHANGE REQUIRED**
Autocomplete suggest proxy handler. Accepts a sanitised keyword (q) and proxies the request to SRCH-01. Response shape requires contract definition. Carried as a new endpoint in the BFF service for US-001.
- Data models: (none defined — see gap item 2 above)
- Functional requirements: FR-001
- Acceptance criteria: US-001-ac-1
- Evidence: service:SVC-003, artifact-SVC-003-api_spec, artifact-SVC-003-c3

### SVC-005 — `ProductIndexRecord` (data_contract) — **CONTRACT CHANGE REQUIRED**
The ProductIndexRecord schema must include a mandatory category field with values drawn from the four product categories (`devices | supplies | software | accessories`) for FR-002 category label rendering, and the `attributes{}` map must be fully indexed in SRCH-01 to enable attribute-value keyword matching for FR-004. These schema constraints are a contract requirement for the Search Index Sync Worker's Commerce Platform webhook handler (`POST /webhooks/commerce`) and the full re-index job (`POST /admin/reindex`).
- Data models: `ProductIndexRecord: { recordId, sku, name, description, category (devices | supplies | software | accessories), attributes{}, specSheetUrl, price, availability, imageUrl }`
- Functional requirements: FR-002, FR-004
- Acceptance criteria: US-001-ac-2, US-001-ac-4
- Evidence: service:SVC-005, artifact-SVC-005-c3, artifact-SVC-005-api_spec, spec_functional:FR-002, spec_functional:FR-004

### SVC-005 — `POST /webhooks/commerce` (api_contract) — no contract change flagged
Commerce Platform product update ingestion pipeline. Must correctly populate the mandatory category field and the `attributes{}` map on ProductIndexRecord for all four product categories before pushing to SRCH-01 ingestion API. Contract must specify the expected webhook payload shape and the transformation rules for category and attributes fields.
- Data models: `WebhookEvent: { eventId, source, eventType, entityId, entityType, occurredAt, payload{} }`, `ProductIndexRecord: { recordId, sku, name, description, category, attributes{}, specSheetUrl, price, availability, imageUrl }`
- Functional requirements: FR-004
- Acceptance criteria: US-001-ac-4
- Evidence: service:SVC-005, artifact-SVC-005-c3, artifact-SVC-005-api_spec

### SVC-005 — `POST /admin/reindex` (api_contract) — no contract change flagged
Full re-index job trigger. Must cover all four product categories to ensure the search index is complete and consistent for cross-category queries. Contract must specify authentication requirements and the IndexSyncJob response shape.
- Data models: `IndexSyncJob: { jobId, source, entityIds[], status (pending | running | completed | failed), startedAt, completedAt, errorMessage }`
- Functional requirements: FR-001
- Acceptance criteria: US-001-ac-1
- Evidence: service:SVC-005, artifact-SVC-005-c4

## Contract Change Reasons
- Grounded API impact was identified: `GET /api/search?q=&facets=` on SVC-003 requires a contract-level specification of the sanitised query parameter, the `contentTypes[]` facet parameter scoping all four product categories, and the structured SearchResult response shape including a mandatory category label field on each hit and a `totalHits=0` empty-state signal.
- Grounded data contract change was identified: the ProductIndexRecord schema on SVC-005 requires a mandatory category field (`devices | supplies | software | accessories`) and a fully indexed `attributes{}` map to support attribute-value keyword matching (FR-004, US-001-ac-4); these fields must be contractually defined before the Search Index Sync Worker can be implemented.
- Grounded API impact was identified: `GET /api/search/suggest?q=` on SVC-003 is a new autocomplete proxy endpoint that requires a contract definition for its query parameter and response shape.

## Security and Quality Obligations
- Verify the changed user flow against the stated accessibility criteria.

## Verification Plan
- **US-001-ac-1**: The product catalogue page must render a visible, accessible search input field that accepts free-text keyword entry and triggers a search across all four product categories (devices/MFPs/printers, supplies, software, accessories) without requiring a full page reload.
- **US-001-ac-2**: When search results are returned, the Frontend Shell must render each result card or row with a clearly visible product-category label so users can distinguish which category each result belongs to.
- **US-001-ac-3**: When a keyword matches a product name, the Frontend Shell must display that product in the results list in the correct position and with accurate name text as returned by the underlying data source.
- **US-001-ac-4**: When a keyword matches a product attribute value (not just the product name), the Frontend Shell must include that product in the rendered results list, correctly reflecting the attribute-matched product alongside any name-matched products.
- **US-001-ac-5**: When no products match the entered keyword across any category, the Frontend Shell must render the designated empty-state UI component — displaying the resolved OD-02 copy message and guidance text (e.g. suggesting the user clear or modify the search term) — without producing a blank page, a layout collapse, or an unhandled JavaScript error.
- **US-001-ac-6**: The empty-state component must be visually distinct from a loading state and a populated results state, ensuring the user always receives clear feedback about the outcome of their search.
- **US-001-ac-7**: The search input and results area must remain functional and error-free when the keyword contains special characters, leading/trailing whitespace, or very long strings, with the Frontend Shell sanitising or trimming input before passing it to any downstream data-fetching layer.

## Provenance
- **Policy version**: `ace-spec-format/v1`
- **Run ID**: `0d7c0043-b335-40e4-8b58-98d0dabf7e32`
- **Project ID**: `61393f69-dfc8-40ff-8342-28015c6b565c`
- **Story ID**: `US-001`
- **Source hash**: `sha256:7b21512947a7e269d30b1ffb9473ec10970c52422911d8ff5a32089541773cfa`
- **Accepted impact hash**: null
- **API impact status**: `ready_with_gaps`
- **Technical design degraded**: true
