---
id: agent-ba/notes_api/rollup.md
type: spec
project: b0326ad3-78d5-4254-8e5e-32f701c09bc2
version: 0.1.1
author: c420e621-a820-5b3a-b15f-ca557d09cc88
produces: agent-ba/notes_api/rollup.md
depends_on: []
commit_kind: append
confidence: agent
run_id: 5dd29246-c79a-57bc-97dc-ab0fbdf73733
---

# Business Analysis — D-COPSCAR

## Discovery

**Brief Quality:** thin (Problem: weak, Outcomes: weak, Stakeholders: weak)

**Declaration:** Brief is thin for this CR: no stated business pain point, no success metric, no role boundary for who may pin. Assumptions: pin is a per-note boolean (not per-user); pin action is available to 'team member' role as stated in CR wording, not lead-only; no cap on number of pinned notes; sort order becomes pinned-first, then newest-first as secondary key; this lane (notes_api) owns API spec, DB migration, endpoint logic, and tests only — UI toggle/display is out of scope here per lane split.

## Business Objectives

### OBJ-001: Let team members prioritize notes at the API layer so pinned notes surface first without relying on search/tags.
- **Leading KPI:** % of API list/search contract tests confirming pinned-first ordering
- **Lagging KPI:** % of active users with ≥1 pinned note (via API usage data)
- **Target:** 100% ordering correctness in contract tests at launch; ≥30% of active users pin ≥1 note within 60 days
- **Impact:** Pin toggle replaces repeated search/tag lookups as the way users resurface key notes

### OBJ-002: Preserve the committed sub-300ms list/search performance at 10,000 notes after adding pinned sort/filter logic.
- **Leading KPI:** p95 response time of GET /notes (list/search) under load test at 10,000 notes
- **Lagging KPI:** p95 production response time for list/search, rolling 30 days post-release
- **Target:** p95 < 300ms at 10,000 notes at launch, sustained for 30 days with no regression vs pre-CR baseline
- **Impact:** Feature ships without breaking the existing performance SLA (S-04)

### OBJ-003: Keep the notes API contract backward-compatible so the web_app lane integrates the pinned field without rework.
- **Leading KPI:** # of breaking changes found in API contract diff/consumer tests
- **Lagging KPI:** # of web_app-reported integration defects tied to the pinned field post-release
- **Target:** Zero breaking changes at release; zero pinned-related integration defects in first sprint post-release
- **Impact:** De-risks cross-lane delivery between notes_api and web_app

### OBJ-004: Ensure the pinned-flag schema change preserves EU data residency and no-third-party-analytics constraints.
- **Leading KPI:** % of pinned-flag migration/schema changes passing residency/no-analytics compliance checklist pre-merge
- **Lagging KPI:** # of compliance incidents tied to notes data within 60 days post-release
- **Target:** 100% of migrations pass compliance review before deploy; 0 incidents in 60 days
- **Impact:** New field adds functionality without introducing residency or governance risk

## EP-001: Pinned Flag & Pinned-First Ordering (→ OBJ-001)
Add a boolean 'pinned' field to the note resource and ensure list/search endpoints return pinned notes first (then newest-first) so members can resurface key notes without relying on search/tags.

### US-001-01: As a Team member, I want to set the pinned flag when creating or editing a note via the API, so that so the note is marked as important and will surface first in list/search results
**Priority:** Must Have

**Acceptance Criteria:**
- Given a valid create or update request includes pinned: true, when the API processes the request, then the note is stored with pinned=true and the response reflects pinned=true
- Given the pinned field is sent as a non-boolean value, when the API validates the request body, then a 400 validation error is returned and the note is not saved
- Given the pinned field is omitted from a create request, when the note is created, then pinned defaults to false

**Test Cases:**
- **TC-001-01**: the API processes the request
- **TC-001-02**: [error] the API validates the request body
- **TC-001-03**: [edge] the note is created

### US-001-02: As a Team member, I want to retrieve the note list with pinned notes shown first, so that so key notes resurface without having to search or filter by tag
**Priority:** Must Have

**Acceptance Criteria:**
- Given a mix of pinned and unpinned notes exist, when GET /notes is called with no filters, then all pinned notes appear before unpinned notes, each group ordered newest-first
- Given no notes are pinned, when the list endpoint is called, then notes are returned newest-first exactly as before the pinned feature existed
- Given pinned data is missing or null for a legacy note record, when the list endpoint queries notes, then the note is treated as pinned=false and included in the unpinned group without error

**Test Cases:**
- **TC-001-04**: GET /notes is called with no filters
- **TC-001-05**: [edge] the list endpoint is called
- **TC-001-06**: [error] the list endpoint queries notes

### US-001-03: As a Team member, I want to search or filter notes while pinned-first ordering is preserved, so that so pinned notes still stand out even when narrowing results by text or tag
**Priority:** Must Have

**Acceptance Criteria:**
- Given a search query or tag filter matches both pinned and unpinned notes, when the search/filter endpoint returns results, then matching pinned notes are listed before matching unpinned notes, newest-first within each group
- Given a search query matches only unpinned notes, when results are returned, then results are ordered newest-first with no pinned group present
- Given a search query matches zero notes, when the endpoint is called, then an empty result array is returned with no ordering error

**Test Cases:**
- **TC-001-07**: the search/filter endpoint returns results
- **TC-001-08**: [edge] results are returned
- **TC-001-09**: [error] the endpoint is called

### US-001-04: As a Team member, I want to get list and search responses within 300ms even with pinned-first ordering applied, so that so performance stays within the required SLA as the pinned feature scales
**Priority:** Must Have

**Acceptance Criteria:**
- Given a dataset of 10,000 notes with a realistic pinned subset, when the list endpoint is called with default ordering, then the response returns within 300ms with pinned-first ordering correct
- Given a large pinned subset of roughly 2,000 notes within the 10,000-note dataset, when a search request is made, then the response still returns within 300ms with correct pinned-first, newest-first ordering
- Given the pinned sort query cannot use an index (e.g., missing composite index), when load testing is run at 10,000 notes, then the response exceeds 300ms, flagging the missing index as a defect to fix before release

**Test Cases:**
- **TC-001-10**: the list endpoint is called with default ordering
- **TC-001-11**: [edge] a search request is made
- **TC-001-12**: [error] load testing is run at 10,000 notes

### US-001-05: As a Team member, I want to toggle the pinned status of an existing note without submitting a full edit, so that so they can quickly promote or demote a note's visibility in the list
**Priority:** Should Have

**Acceptance Criteria:**
- Given an existing unpinned note, when a PATCH request sets pinned=true for that note's id, then the note's pinned status updates and it appears in the pinned group on the next list call
- Given a PATCH pinned request references a note id that does not exist, when the API processes the request, then a 404 not found error is returned and no record is modified

**Test Cases:**
- **TC-001-13**: a PATCH request sets pinned=true for that note's id
- **TC-001-14**: [error] the API processes the request

### US-001-06: As a Team lead, I want to have archived notes excluded from pinned-first ordering until restored, so that so archiving still hides a note from the active list regardless of its pinned flag
**Priority:** Should Have

**Acceptance Criteria:**
- Given a pinned note is archived, when the default (non-archived) list endpoint is called, then the archived pinned note is excluded from results
- Given an archived pinned note is restored, when the list endpoint is called afterward, then the note reappears in the active list in its pinned-first position

**Test Cases:**
- **TC-001-15**: the default (non-archived) list endpoint is called
- **TC-001-16**: [edge] the list endpoint is called afterward

## EP-002: Performance Validation at Scale (→ OBJ-002)
Verify pinned-aware sort/filter logic keeps GET /notes list/search under 300ms p95 at 10,000 notes, matching the existing SLA; add load tests to catch regressions.

### US-002-01: As a Backend engineer validating the notes_api service, I want to run a load test against GET /notes with 10,000 notes (mixed pinned and unpinned) measuring p95 latency for list and search, so that confirm the pinned-aware sort/filter logic still meets the sub-300ms list/search SLA at scale
**Priority:** Must Have

**Acceptance Criteria:**
- Given a database seeded with 10,000 notes with a mix of pinned and unpinned, when a load test issues concurrent GET /notes requests with default sort (pinned first), then p95 response time is under 300ms
- Given a dataset where pinned notes are a large minority (e.g. 20%) scattered across the table, when GET /notes is called without filters, then p95 response time still stays under 300ms
- Given the load test detects p95 above 300ms during a run, when results are evaluated against the SLA threshold, then the test run fails and reports the measured p95 value

**Test Cases:**
- **TC-002-01**: a load test issues concurrent GET /notes requests with default sort (pinned firs
- **TC-002-02**: [edge] GET /notes is called without filters
- **TC-002-03**: [error] results are evaluated against the SLA threshold

### US-002-02: As a Backend engineer optimizing the notes database, I want to add a composite database index covering pinned status and sort/search fields used by GET /notes, so that keep query execution fast enough to preserve the sub-300ms p95 target as pinned becomes part of every list/search query
**Priority:** Must Have

**Acceptance Criteria:**
- Given the pinned column and index are deployed to the notes table, when a GET /notes query executes with pinned-first ordering, then the query plan uses the composite index rather than a full table scan
- Given a combined request with text search, tag filter, and pinned sort at 10,000 notes, when the query executes, then the index is still used and response stays within the 300ms p95 budget
- Given an index migration fails to apply in a target environment, when deployment runs, then the migration halts with an error rather than silently falling back to unindexed scans

**Test Cases:**
- **TC-002-04**: a GET /notes query executes with pinned-first ordering
- **TC-002-05**: [edge] the query executes
- **TC-002-06**: [error] deployment runs

### US-002-03: As a QA engineer responsible for regression safety, I want to add an automated load/performance test suite for GET /notes (list and search, with pinned sort) to the CI pipeline, so that catch future regressions against the committed sub-300ms p95 SLA before they reach production
**Priority:** Must Have

**Acceptance Criteria:**
- Given the CI pipeline runs on a build with a 10,000-note fixture dataset, when the performance test suite executes against GET /notes, then it reports p95 latency and passes if under 300ms
- Given a code change increases p95 latency above 300ms, when the CI performance suite runs, then the build is marked failed with the measured p95 value in the output
- Given the fixture dataset includes notes with no pins, all pins, and mixed pins, when the suite runs across these scenarios, then each scenario is reported individually, not averaged into a single pass/fail

**Test Cases:**
- **TC-002-07**: the performance test suite executes against GET /notes
- **TC-002-08**: [error] the CI performance suite runs
- **TC-002-09**: [edge] the suite runs across these scenarios

### US-002-04: As a Backend engineer tuning query performance, I want to profile and optimize the combined query path for pinned sort plus text search and tag filter at 10,000 notes, so that ensure no single combination of filters regresses the GET /notes p95 below the SLA threshold
**Priority:** Should Have

**Acceptance Criteria:**
- Given 10,000 notes with varied tags and pin states, when GET /notes is called with a text search term and a tag filter together, then pinned notes still sort first and p95 stays under 300ms
- Given a search term that matches a large subset of notes (e.g. 5,000 of 10,000), when the query executes with pinned-first sort applied, then p95 response time remains under 300ms

**Test Cases:**
- **TC-002-10**: GET /notes is called with a text search term and a tag filter together
- **TC-002-11**: [edge] the query executes with pinned-first sort applied

### US-002-05: As a Site reliability engineer monitoring the notes_api, I want to add p95 latency monitoring/alerting for GET /notes in production, so that detect SLA regressions from the pinned feature in real traffic, not just in load tests
**Priority:** Should Have

**Acceptance Criteria:**
- Given production traffic hitting GET /notes, when p95 latency is computed over a rolling window, then it is visible on a dashboard and compared against the 300ms threshold
- Given p95 latency exceeds 300ms for a sustained period, when the monitoring threshold is breached, then an alert fires to the on-call engineer

**Test Cases:**
- **TC-002-12**: p95 latency is computed over a rolling window
- **TC-002-13**: [error] the monitoring threshold is breached

### US-002-06: As a Backend engineer validating concurrency behavior, I want to run the load test under realistic concurrent user load (not just single-request timing) for GET /notes with pinned sort, so that ensure the p95 SLA holds under real team usage patterns, not just isolated requests
**Priority:** Could Have

**Acceptance Criteria:**
- Given a simulated set of concurrent team members issuing list/search requests, when load is applied against a 10,000-note dataset, then p95 response time across all concurrent requests is under 300ms
- Given a sudden burst of concurrent requests exceeding typical team size, when the burst occurs during the load test, then p95 latency is reported and compared against the SLA without the server returning errors

**Test Cases:**
- **TC-002-14**: load is applied against a 10,000-note dataset
- **TC-002-15**: [edge] the burst occurs during the load test

## EP-003: Backward-Compatible API Contract (→ OBJ-003)
Update the REST API spec to add 'pinned' as a non-breaking, additive field (with sensible default and pin/unpin endpoint or PATCH support) so the web_app lane integrates without rework or regressions.

### US-003-01: As a Notes API consumer (web_app backend integrator), I want to see a new 'pinned' boolean field on every note resource with a safe default, so that integrate the pinned feature without needing to change existing request/response parsing, keeping breaking changes at zero
**Priority:** Must Have

**Acceptance Criteria:**
- Given a note created before the pinned field existed, when the note is fetched via GET /notes/{id} or listed, then the response includes "pinned": false by default
- Given a new note is created without specifying pinned, when POST /notes is called, then the created note has pinned defaulted to false and the field is additive (no existing required fields removed or renamed)
- Given an existing API client that does not know about the pinned field, when it parses a note response, then the client's existing field access and schema validation continue to pass unchanged

**Test Cases:**
- **TC-003-01**: the note is fetched via GET /notes/{id} or listed
- **TC-003-02**: POST /notes is called
- **TC-003-03**: [edge] it parses a note response

### US-003-02: As a Team member using an API client, I want to pin or unpin a note via the API, so that mark important notes so they surface first, supporting the pinned-notes-first KPI without adding new breaking surface
**Priority:** Must Have

**Acceptance Criteria:**
- Given an existing unpinned note the user owns access to, when PATCH /notes/{id} is called with {"pinned": true}, then the API returns 200 with the note's pinned field set to true and other fields unchanged
- Given a pinned note, when PATCH /notes/{id} is called with {"pinned": false}, then the API returns 200 with pinned set to false
- Given a note id that does not exist or is archived, when PATCH /notes/{id} pin request is sent, then the API returns 404 (or 409 for archived) and leaves no partial state change

**Test Cases:**
- **TC-003-04**: PATCH /notes/{id} is called with {"pinned": true}
- **TC-003-05**: PATCH /notes/{id} is called with {"pinned": false}
- **TC-003-06**: [error] PATCH /notes/{id} pin request is sent

### US-003-03: As a Team member searching or listing notes via the API, I want to receive pinned notes ordered before unpinned notes in list/search responses, so that pinned notes are listed first as required, while list/search contract shape stays additive and performant
**Priority:** Must Have

**Acceptance Criteria:**
- Given a mix of pinned and unpinned notes matching a list or search query, when GET /notes (with optional search/tag filters) is called, then pinned notes appear before unpinned notes, each group still sorted newest first
- Given 10,000 notes with a subset pinned, when a list or search request is issued, then the response returns within 300 ms and ordering rule holds
- Given no notes are pinned, when a list request is issued, then ordering falls back to newest-first with no errors or schema changes

**Test Cases:**
- **TC-003-07**: GET /notes (with optional search/tag filters) is called
- **TC-003-08**: a list or search request is issued
- **TC-003-09**: [edge] a list request is issued

### US-003-04: As a Backend engineer maintaining the API contract, I want to run a contract/diff check between the previous and updated OpenAPI spec, so that prove zero breaking changes were introduced, directly measuring the epic's KPI
**Priority:** Must Have

**Acceptance Criteria:**
- Given the previous API spec version and the updated spec with pinned field and PATCH endpoint, when a contract diff tool compares them, then the diff reports zero breaking changes (no removed/renamed fields, no changed required-ness, no changed status codes on existing endpoints)
- Given a hypothetical change that removes or renames an existing field or endpoint, when the diff check runs in CI, then the check fails and blocks merge

**Test Cases:**
- **TC-003-10**: a contract diff tool compares them
- **TC-003-11**: [error] the diff check runs in CI

### US-003-05: As a Backend engineer documenting the API, I want to update the REST API spec (OpenAPI) to document the pinned field and pin/unpin PATCH endpoint, so that give the web_app lane an accurate, versioned contract so they integrate without rework
**Priority:** Should Have

**Acceptance Criteria:**
- Given the updated spec, when it is published, then note schema includes pinned: boolean (default false) and PATCH /notes/{id} is documented with request/response examples
- Given the spec is validated against the EU data residency and no-third-party-analytics constraints, when spec review occurs, then no new fields or endpoints introduce external data transmission

**Test Cases:**
- **TC-003-12**: it is published
- **TC-003-13**: [edge] spec review occurs

### US-003-06: As a Backend engineer writing automated tests, I want to add consumer/contract tests covering the pinned field default, PATCH pin/unpin, and pinned-first ordering, so that catch regressions before release, keeping the breaking-changes KPI at zero on an ongoing basis
**Priority:** Should Have

**Acceptance Criteria:**
- Given the test suite, when it runs against the updated API, then tests assert default pinned=false, successful pin/unpin via PATCH, and pinned-first ordering in list/search
- Given a code change that breaks the pinned-first ordering or removes the default, when the test suite runs in CI, then the relevant test fails and blocks the build

**Test Cases:**
- **TC-003-14**: it runs against the updated API
- **TC-003-15**: [error] the test suite runs in CI

## EP-004: Compliant Schema Migration (→ OBJ-004)
Design and apply the DB migration for the 'pinned' column within EU data residency boundaries, with no third-party analytics touchpoints, and pass compliance checklist before deploy.

### US-004-01: As a Backend developer (notes_api), I want to add a 'pinned' boolean column to the notes table via a migration scoped to the EU-hosted database instance, so that schema change ships without introducing data residency risk, supporting the residency/no-analytics compliance KPI
**Priority:** Must Have

**Acceptance Criteria:**
- Given the EU production database is targeted, when the migration runs, then a 'pinned' boolean column defaulting to false is added to the notes table with no data moved outside the EU region
- Given the migration tool is pointed at a non-EU database connection string, when the migration is executed, then the migration aborts with an explicit region-mismatch error and makes no schema change
- Given the notes table already has 10,000 existing rows, when the migration applies the new column, then all existing rows get pinned=false without data loss or downtime beyond the agreed maintenance window

**Test Cases:**
- **TC-004-01**: the migration runs
- **TC-004-02**: [error] the migration is executed
- **TC-004-03**: [edge] the migration applies the new column

### US-004-02: As a Backend developer (notes_api), I want to write the pinned-flag migration and related code using only internal logging, with no calls to third-party analytics or telemetry services, so that the change stays compliant with the no-third-party-analytics constraint, protecting the compliance checklist pass rate
**Priority:** Must Have

**Acceptance Criteria:**
- Given the migration and pinned-flag API code are complete, when a dependency/network-call audit is run, then no outbound calls to third-party analytics or telemetry endpoints are present
- Given a pull request introduces an SDK or library known to phone home to a third-party analytics service, when the dependency scan runs in CI, then the CI check fails and blocks merge until the dependency is removed

**Test Cases:**
- **TC-004-04**: a dependency/network-call audit is run
- **TC-004-05**: [error] the dependency scan runs in CI

### US-004-03: As a Compliance reviewer, I want to run the residency/no-analytics compliance checklist against the pinned-flag migration and implementation before merge, so that provides the auditable pass/fail signal that the KPI measures
**Priority:** Must Have

**Acceptance Criteria:**
- Given the pinned-flag migration PR is ready for review, when the reviewer runs the compliance checklist, then a checklist result (pass/fail) with timestamp and reviewer id is recorded against the PR
- Given the checklist run finds a residency or analytics violation, when the reviewer submits the checklist result, then the PR is marked blocked and cannot be merged until re-submitted as pass
- Given the checklist has not been run on a PR touching the pinned column or migration, when a merge is attempted, then the merge is blocked by a required-status-check gate

**Test Cases:**
- **TC-004-06**: the reviewer runs the compliance checklist
- **TC-004-07**: [error] the reviewer submits the checklist result
- **TC-004-08**: [edge] a merge is attempted

### US-004-04: As a Platform/DevOps engineer, I want to restrict deployment of the pinned-flag migration pipeline to EU region infrastructure only, so that enforces data residency at the infrastructure level, reducing the risk of a residency violation reaching production
**Priority:** Must Have

**Acceptance Criteria:**
- Given the deployment pipeline is configured for the notes_api service, when the pinned-flag migration is deployed, then the deploy target resolves to an EU-region database/host and succeeds
- Given a deploy configuration or environment variable points to a non-EU region, when the pipeline attempts to deploy, then the deployment is rejected before any migration runs

**Test Cases:**
- **TC-004-09**: the pinned-flag migration is deployed
- **TC-004-10**: [error] the pipeline attempts to deploy

### US-004-05: As a Backend developer (notes_api), I want to define and test a rollback for the pinned-flag migration that keeps all data within the EU boundary, so that limits downtime and compliance exposure if the migration fails in production
**Priority:** Should Have

**Acceptance Criteria:**
- Given the pinned-flag migration has been applied to a staging EU database, when the rollback script is run, then the pinned column is removed and the schema returns to its prior state with no data exported outside the EU
- Given the migration fails midway on a locked table, when automatic rollback triggers, then the database is left in a consistent pre-migration state and an alert is raised to the on-call engineer

**Test Cases:**
- **TC-004-11**: the rollback script is run
- **TC-004-12**: [error] automatic rollback triggers

### US-004-06: As a Backend developer (notes_api), I want to update the notes_api spec and migration documentation to record the pinned column and the compliance checklist result as part of the change record, so that creates the traceable evidence needed to report the KPI (% of migrations passing the checklist pre-merge)
**Priority:** Should Have

**Acceptance Criteria:**
- Given the pinned-flag migration has passed the compliance checklist, when the API spec is updated, then the spec documents the new 'pinned' field, its type/default, and links to the passed checklist record
- Given the API spec PR is submitted without a linked checklist record, when the review is performed, then the PR is flagged as incomplete and not approved for merge

**Test Cases:**
- **TC-004-13**: the API spec is updated
- **TC-004-14**: [edge] the review is performed

## Business Rules

### BR-001: Every note resource has a 'pinned' boolean field; existing notes default to pinned=false on migration and new notes default to pinned=false unless explicitly set
**Rationale:** Guarantees zero breaking changes for existing consumers and a predictable default state | **Enforcement:** DB column NOT NULL DEFAULT false; API schema validation

### BR-002: List and search results order pinned notes before unpinned notes; within each group, existing sort/relevance order is preserved
**Rationale:** Core KPI: pinned notes must surface first without disrupting existing ordering semantics | **Enforcement:** GET /notes query ORDER BY pinned DESC, <existing sort key> ASC

### BR-003: Pinned-first ordering is preserved under any text search or tag filter combination
**Rationale:** Pinned prominence must not be lost when narrowing results | **Enforcement:** Sort clause applied after filter/search predicates in query builder

### BR-004: Archived notes are excluded from pinned-first ordering and from the active notes list regardless of pinned status, until restored
**Rationale:** Archiving semantics take precedence over pin status to avoid surfacing hidden notes | **Enforcement:** GET /notes active-list query filters archived=false before applying pinned sort

### BR-005: Pin/unpin can be performed via a dedicated PATCH operation without requiring a full note edit payload
**Rationale:** Allows quick toggle action distinct from full edit workflow | **Enforcement:** PATCH /notes/{id} accepting only {pinned: boolean}

### BR-006: The 'pinned' field addition and pin/unpin endpoint must introduce zero breaking changes to the existing OpenAPI contract
**Rationale:** Protects downstream web_app and other consumers from integration rework | **Enforcement:** Automated OpenAPI diff check in CI comparing previous vs updated spec

### BR-007: No pinned-flag code, migration, or logging may call third-party analytics or telemetry services; only internal logging is permitted
**Rationale:** Enforces the no-third-party-analytics constraint from the project brief | **Enforcement:** Compliance checklist review + static/dependency scan in CI

### BR-008: All data and infrastructure touched by the pinned-flag migration must remain within EU region boundaries
**Rationale:** Enforces EU data residency constraint from the project brief | **Enforcement:** Deployment pipeline restricted to EU region infra; migration scoped to EU DB instance

### BR-009: The pinned-flag migration must pass the residency/no-analytics compliance checklist before merge to main
**Rationale:** Provides auditable pre-merge compliance gate feeding the compliance KPI | **Enforcement:** CI merge gate blocks on checklist pass/fail status

### BR-010: The pinned-flag migration must have a tested rollback procedure that keeps all data within the EU boundary
**Rationale:** Limits downtime and compliance exposure on migration failure | **Enforcement:** Rollback script reviewed and tested in EU staging environment prior to production deploy

## Non-Functional Requirements

### NFR-001: GET /notes list and search endpoints (including pinned-first sort, text search, and tag filter combinations) respond within SLA at 10,000 notes
**Category:** Performance | **Target:** p95 latency < 300ms | **Priority:** Must Have

### NFR-002: GET /notes p95 latency SLA holds under realistic concurrent user load, not just single-request timing
**Category:** Performance | **Target:** p95 < 300ms at specified concurrent request rate (e.g., 50 concurrent users) | **Priority:** Must Have

### NFR-003: Database schema includes a composite index covering pinned status plus sort/search fields used by GET /notes to sustain performance as data volume grows
**Category:** Scalability | **Target:** Composite index present; p95 < 300ms sustained at 10,000+ notes | **Priority:** Must Have

### NFR-004: Automated CI load/performance test suite for GET /notes runs on every build to catch SLA regressions pre-production
**Category:** Availability | **Target:** 100% of CI builds execute load test; fail build if p95 >= 300ms | **Priority:** Should Have

### NFR-005: Production monitoring and alerting on GET /notes p95 latency to detect SLA regressions in real traffic
**Category:** Availability | **Target:** Alert triggered within 5 minutes if p95 >= 300ms over a 5-minute rolling window | **Priority:** Should Have

### NFR-006: Contract/diff check between previous and updated OpenAPI spec must show zero breaking changes for the pinned feature release
**Category:** Compliance | **Target:** 0 breaking changes detected by automated diff tool | **Priority:** Must Have

### NFR-007: Pinned-flag migrations pass the EU-residency/no-third-party-analytics compliance checklist before merge
**Category:** Compliance | **Target:** 100% of migrations pass checklist pre-merge | **Priority:** Must Have

### NFR-008: Pinned-flag feature code and migration pipeline make no network calls to third-party analytics/telemetry endpoints
**Category:** Security | **Target:** 0 outbound calls to non-internal/non-EU-approved endpoints detected in scan | **Priority:** Must Have

### NFR-009: All infrastructure executing the pinned-flag migration pipeline is restricted to EU region deployment targets
**Category:** Compliance | **Target:** 100% of migration deploy jobs run on EU-region infra (0 non-EU executions) | **Priority:** Must Have

### NFR-010: Pinned-flag migration rollback completes successfully without data leaving the EU boundary if migration fails
**Category:** Availability | **Target:** Rollback tested successfully in staging; completes within 15 minutes with 0 data loss | **Priority:** Should Have

### NFR-011: Consumer/contract automated test coverage validates pinned field default, PATCH pin/unpin behavior, and pinned-first ordering
**Category:** Usability | **Target:** ≥95% test coverage of pinned-related contract scenarios, run on every CI build | **Priority:** Should Have
