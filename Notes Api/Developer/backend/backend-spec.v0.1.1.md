---
id: agent-developer/notes_api/backend/BACKEND-SPEC.md
type: api_contract
project: b0326ad3-78d5-4254-8e5e-32f701c09bc2
version: 0.1.1
author: c420e621-a820-5b3a-b15f-ca557d09cc88
produces: agent-developer/notes_api/backend/BACKEND-SPEC.md
depends_on: [US-001-01, US-001-02, US-001-03, US-001-05, US-003-02, US-001-06, US-003-01, US-003-04, US-004-01, US-004-02, US-004-03, US-004-05, US-002-02, US-004-04, US-004-06, US-001-04, US-002-01, US-002-03, US-002-04, US-002-05, US-002-06, US-003-03, US-003-05, US-003-06]
commit_kind: append
confidence: agent
run_id: 5dd29246-c79a-57bc-97dc-ab0fbdf73733
okf_id: backend/BACKEND-SPEC.md
---

<!-- SECTION: backend.overview | status: DERIVED -->
## Overview

This specification covers the backend (`notes_api`) scope of the **pinned-flag** feature: a boolean `pinned` attribute on the Note resource, pinned-first ordering in list/search, a dedicated pin/unpin update path, and the EU-residency / no-third-party-analytics compliance constraints that govern the migration delivering it (BR-001, BR-002, BR-003, BR-004, BR-005, BR-006, BR-007, BR-008, BR-009, BR-010).

**Responsible for:**
- the `pinned` field on the Note resource, its default, and its validation (BR-001, US-001-01)
- pinned-first ordering semantics across list, search and tag-filtered queries, with existing sort/relevance order preserved within each group (BR-002, BR-003, US-001-02, US-001-03)
- a partial-update path that toggles `pinned` without a full edit payload (BR-005, US-001-05, US-003-02)
- exclusion of archived notes from pinned-first ordering and from the active list regardless of pin state, until restored (BR-004, US-001-06)
- zero-breaking-change evolution of the existing API contract (BR-006, NFR-006, US-003-01, US-003-04)
- an EU-region-scoped, reversible migration with a compliance gate (BR-008, BR-009, BR-010, NFR-007–NFR-010, EP-004 stories)
- meeting the sub-300ms p95 SLA for `GET /notes` list/search at 10,000 notes under concurrent load (NFR-001–NFR-005, EP-002 stories)

**Explicitly not responsible for:**
- any UI, screen, or client-side rendering of pinned state (no design or platform input was supplied for this run)
- any third-party analytics or telemetry integration — explicitly forbidden for all pinned-flag code, migrations and logging (BR-007, NFR-008)
- any infrastructure or data placement outside the EU region (BR-008, BR-009, NFR-009)
- defining or modifying the pre-existing Note schema and endpoints beyond what this feature touches

**Scope limitation.** No existing OpenAPI contract, platform doc, or api_docs input was supplied for this run (`inputs.api_docs: []`, `inputs.platform: []`), even though BR-006 and NFR-006 require a zero-breaking-change diff against "the previous API spec." This spec documents only what the supplied requirements and stories state about the pre-existing system; it cannot verify the diff claim itself. See **GAP-BE-001**.

<!-- /SECTION: backend.overview -->
<!-- SECTION: backend.domain-model | status: DRAFT-UNVERIFIED -->
## Domain model

### Entities and relationships

```
+--------------------------------------+        scoped by        +--------------------+
|                 Note                 |<------------------------|  Team / Owner      |
|---------------------------------------|   (ASM-BE-001)          |  (actor scope)     |
| id                 (PK, identity)     |                         +--------------------+
| pinned   boolean   NOT NULL DEFAULT false   -- BR-001          |
| archived boolean   (ASM-BE-002)                                 |
| <text field(s) searched by GET /notes>      -- GAP-BE-002       |
| tags     <representation unknown>            -- GAP-BE-003      |
| <creation timestamp, assumed name>           -- ASM-BE-003      |
+--------------------------------------+
```

**Cited facts:**
- `pinned` is a boolean, `NOT NULL`, default `false`, on every note; existing notes receive this default on migration and new notes default to it unless explicitly set (BR-001).
- Notes have an archival state that, when set, excludes the note from the active list and from pinned-first ordering regardless of `pinned`, until the note is restored (BR-004, US-001-06).
- Notes are text-searchable and tag-filterable; both predicates compose with pinned-first ordering (BR-002, BR-003, US-001-03).
- A "newest-first" ordering exists today and is preserved as the secondary sort key within each pinned/unpinned group (BR-002, US-001-02).

**Assumptions taken (no input states these facts directly):**
- **ASM-BE-001** — Notes are scoped to a shared "Team" concept rather than strict single-user ownership, inferred from the "Team member" / "Team lead" actor labels used throughout the stories. Unconfirmed; would be confirmed by an ownership/authorization doc or the (missing) existing API spec.
- **ASM-BE-002** — Archival is represented as a boolean `archived` column. No input states its type explicitly, only its behaviour (BR-004).
- **ASM-BE-003** — The "newest-first" ordering key is a creation timestamp, assumed named `created_at` by convention. No input names the column.

**Gaps:**
- **GAP-BE-002** — The Note schema's substantive fields (title/body/content) are not named by any input. Degrades: this section, the index design in `backend.migration-plan`, and the error-model's request-body description. Closed by: the existing OpenAPI contract or a platform/data-model doc.
- **GAP-BE-003** — The tag representation (array column vs. join table vs. free-text) used for "tag filter" is not stated. Degrades: this section, NFR-003's index design. Closed by: existing schema docs.

<!-- /SECTION: backend.domain-model -->
<!-- SECTION: backend.module-map | status: DERIVED -->
## Module map

Exactly one backend module is evidenced by the supplied inputs:

| Module | Boundary | Depends on |
|---|---|---|
| `notes` | Owns the Note resource end-to-end: schema, CRUD, pin/unpin toggle, list/search with pinned-first ordering, archive-exclusion semantics. | none (no second module is described) |

**GAP-BE-004** — This is a brownfield addition to a production service (BR-006, US-004-01 reference an existing contract and 10,000 existing rows), so other backend modules almost certainly exist in the live system. None are described by the supplied inputs, so none are modelled here. Degrades: completeness of this map only. Closed by: the existing service's architecture doc or source.

<!-- /SECTION: backend.module-map -->
<!-- SECTION: backend.api-surface | status: DRAFT-UNVERIFIED -->
## API surface

| API ID | Method | Path | Auth | Module |
|---|---|---|---|---|
| `API-NOTES-001@1` | GET | `/notes` | required (see `backend.auth-model`) | notes |
| `API-NOTES-002@1` | POST | `/notes` | required | notes |
| `API-NOTES-003@1` | GET | `/notes/{id}` | required | notes |
| `API-NOTES-004@1` | PATCH | `/notes/{id}` | required | notes |

**Citations:**
- `GET /notes` serves list and search, with pinned-first ordering and search/tag-filter composition (BR-002, BR-003, US-001-02, US-001-03), and excludes archived notes from the active-list view (BR-004).
- `POST /notes` creates a note and accepts an optional `pinned` boolean, defaulting to `false` if omitted (US-001-01, US-003-01).
- `GET /notes/{id}` fetches a single note, which includes `pinned` with its default for pre-existing notes (US-003-01).
- `PATCH /notes/{id}` accepts a minimal `{"pinned": boolean}` body to toggle pin state without a full edit payload (BR-005, US-001-05, US-003-02).

**ASM-BE-004** — `PATCH /notes/{id}` is treated as the service's existing general partial-update endpoint, and the pin/unpin operation is a valid subset-payload use of it, rather than a newly-introduced, pin-only endpoint. This is inferred from BR-005's framing ("without requiring **a full note edit payload**"), which implies a full-edit payload already exists on this path. No input confirms this directly. If wrong, `PATCH /notes/{id}` may reject any body except `{pinned}`, which would change its contract shape.

**Gaps:**
- **GAP-BE-005** — BR-004's exception references "an explicit archived-notes endpoint/view" that may still show pinned flag on archived items. No method or path is given for it, so it is not assigned an `API-` ID. Closed by: the existing contract or a product decision naming the route.
- **GAP-BE-006** — Exact query parameter names for search text, tag filter, and any pagination/sort parameters on `GET /notes` are not stated (only their existence and behaviour are). Closed by: existing OpenAPI doc or a parameter-naming decision.

<!-- /SECTION: backend.api-surface -->
<!-- SECTION: backend.auth-model | status: DRAFT-UNVERIFIED -->
## Auth model

**Mechanism:** self-managed JWT/session authentication (human feedback, `feedback.md` §auth-type). No input names a specific library or token format beyond this; per the same feedback, the stack should otherwise be kept minimal (`feedback.md` §stack).

**Actors appearing in the inputs:**
- Team member — creates, lists/searches, and pin/unpins notes (EP-001, EP-003 stories)
- Team lead — same note-level operations exercised in EP-001 (US-001-06); no distinct permission is stated for this role
- Notes API consumer (web_app backend integrator) — an external client consuming the same contract (EP-003)
- Backend engineer / QA engineer / SRE / Compliance reviewer / Platform-DevOps engineer — process/pipeline roles (load testing, CI, compliance checklist, deployment), not note-resource actors

**ASM-BE-005** — "Team member" and "Team lead" are treated as a single authenticated role for the purposes of the Note resource permission matrix below, since no input states a permission difference between them.

**Permission matrix (Note resource):**

| Role | Create | Read / List / Search | Update `pinned` (PATCH) | Archive / Restore |
|---|---|---|---|---|
| Authenticated team member (incl. "team lead") | allowed (US-001-01) | allowed (US-001-02, US-001-03) | allowed (US-001-05, US-003-02) | referenced as occurring (US-001-06) but mechanism unspecified — see GAP-BE-005 |

**GAP-BE-007** — The ownership/visibility predicate (is a note visible to its creator only, or to the whole team?) is not stated by any input; `backend.domain-model`'s ASM-BE-001 assumes team-wide scope. Closed by: an authorization doc or product decision.

<!-- /SECTION: backend.auth-model -->
<!-- SECTION: backend.data-lifecycle | status: DERIVED -->
## Data lifecycle

**Stored:** Note records, including the new `pinned` boolean (BR-001), an archival flag (ASM-BE-002, `backend.domain-model`), and whatever search/tag fields exist today (GAP-BE-002, GAP-BE-003) — all within an EU-hosted database instance (BR-008, NFR-009).

**Region:** all data and infrastructure touched by the pinned-flag migration, including rollback, must remain within EU region boundaries at every stage — schema change, deploy, and rollback alike (BR-008, BR-010, NFR-009, NFR-010).

**Retention:** not stated by any input. **GAP-BE-008** — closed by a data-retention policy document.

**Encrypted at rest:** not stated by any input. Per the standing security constraint (core §12), this is recorded as a gap, not assumed. **GAP-BE-009** — closed by an infrastructure/security doc.

**Deletion:** no deletion endpoint or policy is referenced; archived notes persist and are excluded from the active list "until restored" (BR-004), i.e. archival is soft and reversible, not a deletion. Whether a hard-delete mechanism exists elsewhere in the system is unknown. **GAP-BE-010** — closed by the existing contract.

**Third-party analytics:** explicitly and categorically forbidden for all pinned-flag code, migrations, and associated logging — only internal logging is permitted (BR-007, NFR-008).

<!-- /SECTION: backend.data-lifecycle -->
<!-- SECTION: backend.nfr | status: VERIFIED -->
## Non-functional requirements

| ID | Category | Target | Source |
|---|---|---|---|
| NFR-001 | Performance | `GET /notes` list/search (incl. pinned-first sort, text search, tag filter) p95 < 300ms at 10,000 notes | NFR-001 |
| NFR-002 | Performance | p95 < 300ms holds under realistic concurrent load (e.g. 50 concurrent users) | NFR-002 |
| NFR-003 | Scalability | Composite index covering pinned status plus sort/search fields used by `GET /notes`; p95 < 300ms sustained at 10,000+ notes | NFR-003 |
| NFR-004 | Availability | Automated CI load/performance suite for `GET /notes` runs on every build; fails build if p95 ≥ 300ms | NFR-004 |
| NFR-005 | Availability | Production p95 monitoring/alerting; alert within 5 minutes if p95 ≥ 300ms over a 5-minute rolling window | NFR-005 |
| NFR-006 | Compliance | OpenAPI diff between previous and updated spec shows 0 breaking changes | NFR-006 |
| NFR-007 | Compliance | 100% of pinned-flag migrations pass the EU-residency / no-third-party-analytics checklist pre-merge | NFR-007 |
| NFR-008 | Security | 0 outbound calls to non-internal / non-EU-approved endpoints from pinned-flag code and migration pipeline | NFR-008 |
| NFR-009 | Compliance | 100% of migration deploy jobs run on EU-region infra (0 non-EU executions) | NFR-009 |
| NFR-010 | Availability | Rollback tested successfully in staging; completes within 15 minutes with 0 data loss, no data leaving EU | NFR-010 |
| NFR-011 | Usability | ≥95% contract-test coverage of pinned-related scenarios (default, PATCH toggle, pinned-first ordering), run every CI build | NFR-011 |

No baseline (pre-feature) performance figures were supplied, so regression comparison against a "before" measurement is not possible from these inputs alone — this does not block writing the targets above, which are stated as absolute thresholds, not deltas.

<!-- /SECTION: backend.nfr -->
<!-- SECTION: backend.error-model | status: DRAFT-UNVERIFIED -->
## Error model

**Global error envelope:** not stated by any input. **GAP-BE-012** — closed by the existing OpenAPI contract (GAP-BE-001) or an explicit envelope decision.

**Machine-readable error-code registry:** not stated by any input — only HTTP status codes are evidenced, not string error codes. **GAP-BE-011** — closed by the same.

**Status codes evidenced directly by inputs:**

| Status | Trigger | Source |
|---|---|---|
| 200 | `PATCH /notes/{id}` successfully sets `pinned` | US-003-02 AC1, AC2 |
| 400 | `pinned` sent as a non-boolean value on create/update | US-001-01 AC2 |
| 404 | `PATCH /notes/{id}` references a note id that does not exist | US-001-05 AC2 |
| 404 or 409 (unresolved in source) | `PATCH /notes/{id}` references an archived note | US-003-02 AC3 |

**ASM-BE-007** — The archived-note PATCH case is resolved to **409 Conflict** (resource exists but its state disallows the operation), rather than 404. The source story itself hedges ("404 (or 409 for archived)"); 409 is taken as the default per standard REST conflict semantics. Unconfirmed.

**ASM-BE-006** — Success status codes for `GET /notes`, `GET /notes/{id}`, and `POST /notes` are assumed to follow common HTTP convention (200 for the GETs, 201 for the POST). No input states any of these three explicitly; per the standing rule that status codes require a citation, this is recorded as an assumption rather than a verified fact.

<!-- /SECTION: backend.error-model -->
<!-- SECTION: backend.observability | status: DERIVED -->
## Observability

**Logged:** internal logging only. No pinned-flag code, migration, or logging path may call third-party analytics or telemetry services (BR-007, NFR-008) — a dependency/network-call audit is required to confirm this (US-004-02).

**Redacted:** not stated by any input which fields, if any, are redacted from logs. **GAP-BE-013** — closed by a logging/security policy doc. Per the standing security constraint, PII fields are masked in all examples regardless (core §12); this spec contains none, since no PII field was identified in the inputs.

**Metered:**
- `GET /notes` p95 latency, in CI load tests (NFR-001, NFR-002, NFR-004) and in production (NFR-005).
- Compliance checklist pass/fail outcome, timestamp, and reviewer id, recorded against each PR touching the pinned column or migration (US-004-03).

**Alerts:**
- Production: if p95 latency for `GET /notes` is ≥300ms over a 5-minute rolling window, an alert fires to on-call within 5 minutes (NFR-005).
- CI: a build fails if p95 ≥300ms (NFR-004), or if a migration-triggered mid-run failure occurs — raising an on-call alert and leaving the database in a consistent pre-migration state (US-004-05 AC2).
- Merge gate: a PR touching the pinned column/migration is blocked by a required-status-check if the compliance checklist has not been run, or has failed (US-004-03 AC2, AC3).

<!-- /SECTION: backend.observability -->
<!-- SECTION: backend.migration-plan | status: DRAFT-UNVERIFIED -->
## Migration plan

**Migration 001 — add `pinned` column**
- Add `pinned boolean NOT NULL DEFAULT false` to the `notes` table (BR-001, US-004-01 AC1).
- Pre-flight: abort with an explicit region-mismatch error, making no schema change, if the migration's DB connection does not resolve to the EU region (US-004-01 AC2, BR-008).
- Applies to all existing rows (≥10,000 at time of writing), setting `pinned=false` with no data loss, within the agreed maintenance window (US-004-01 AC3).
- **Reversible.** Rollback drops the `pinned` column, returning the schema to its prior state, exporting no data outside the EU boundary; it must be tested in EU staging before any production deploy (BR-010, US-004-05 AC1).
- On mid-migration failure (e.g. a locked table), automatic rollback leaves the database in a consistent pre-migration state and raises an on-call alert (US-004-05 AC2).
- Rollback must complete within 15 minutes with zero data loss (NFR-010).
- **GAP-BE-015** — the rollback script's actual contents are not given, only its required behaviour. Closed by the tested rollback script itself.

**Migration 002 — composite index (candidate, not yet concrete)**
- Add a composite index covering `pinned` plus the sort/search fields used by `GET /notes` (NFR-003).
- Candidate column list: `(pinned, created_at)` at minimum (per ASM-BE-003), extended by whatever search/tag fields are confirmed — **cannot be finalized** until GAP-BE-002 and GAP-BE-003 are closed. **GAP-BE-014**.
- If this index migration fails to apply in a target environment, it must halt with an error rather than silently falling back to an unindexed scan (US-002-02 AC3).

**Deployment constraint (both migrations):** execute only on EU-region infrastructure; a non-EU deploy target is rejected before any migration runs (BR-008, NFR-009, US-004-04).

**Compliance gate (both migrations):** must pass the EU-residency / no-third-party-analytics checklist before merge (BR-009, NFR-007); checklist failure blocks merge (US-004-03 AC2); the passed checklist record must be linked from the updated API/migration docs as change-record evidence (US-004-06).

**GAP-BE-016** — No migration tooling, ORM, or framework is named by any input (`inputs.platform` is empty; `feedback.md` states only "keep it minimal" as a stack preference, which names no specific tool). Closed by a platform/stack decision.

<!-- /SECTION: backend.migration-plan -->
<!-- SECTION: backend.coverage | status: VERIFIED -->
## Story → requirement coverage

| Story | Requirement(s) covering it |
|---|---|
| US-001-01 | BR-001 |
| US-001-02 | BR-002 |
| US-001-03 | BR-003 |
| US-001-04 | NFR-001, NFR-003 |
| US-001-05 | BR-005 |
| US-001-06 | BR-004 |
| US-002-01 | NFR-001, NFR-002 |
| US-002-02 | NFR-003 |
| US-002-03 | NFR-004 |
| US-002-04 | NFR-003, NFR-001 |
| US-002-05 | NFR-005 |
| US-002-06 | NFR-002 |
| US-003-01 | BR-001, BR-006 |
| US-003-02 | BR-005 |
| US-003-03 | BR-002, BR-003, NFR-001 |
| US-003-04 | BR-006, NFR-006 |
| US-003-05 | BR-006, BR-007, BR-008 |
| US-003-06 | NFR-011 |
| US-004-01 | BR-008, BR-001 |
| US-004-02 | BR-007, NFR-008 |
| US-004-03 | BR-009, NFR-007 |
| US-004-04 | BR-008, NFR-009 |
| US-004-05 | BR-010, NFR-010 |
| US-004-06 | BR-009 |

All 24 supplied stories are covered. No story is uncovered.

<!-- /SECTION: backend.coverage -->
