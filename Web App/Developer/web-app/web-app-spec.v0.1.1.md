---
id: agent-developer/web_app/web-app/WEB-APP-SPEC.md
type: spec
project: b0326ad3-78d5-4254-8e5e-32f701c09bc2
version: 0.1.1
author: c420e621-a820-5b3a-b15f-ca557d09cc88
produces: agent-developer/web_app/web-app/WEB-APP-SPEC.md
depends_on: [US-001-01, US-002-01, US-003-01, US-001-03, US-001-06, US-004-02, US-004-06, US-004-01, US-004-03, US-004-05, US-002-04, US-001-02, US-001-04, US-001-05, US-002-02, US-002-03, US-002-05, US-002-06, US-003-02, US-003-03, US-003-04, US-003-05, US-003-06, US-004-04]
commit_kind: append
confidence: agent
run_id: 7213f693-d9f9-5517-98a8-e5c8999a9b44
okf_id: web-app/WEB-APP-SPEC.md
---

<!-- SECTION: web-app.overview | status: DERIVED -->
## Overview

**Purpose.** This specification covers the `web-app` surface's delivery of the pinned-note feature: pinning/unpinning a note from the list or detail view, pinned-first ordering preserved across list, search and tag-filter, and the archive/restore lifecycle's interaction with pin state (BR-001–BR-010, EP-001–EP-004 in `stories.md`).

**Primary actors** (per `stories.md`, all 24 stories): **Team member** and **Team lead**. See `web-app.authorization-ui` for how their visible navigation and enabled actions differ.

**Upstream contract.** This surface builds against the in-run backend spec at `backend-api.md`, contract pin `sha256:138d982936e0` (`RUN-CONTEXT.upstream`). Endpoints are referenced by pinned ID only (`API-NOTES-001@1`…`API-NOTES-004@1`); their request/response shapes are not restated here (core rule 6).

**Design-input coverage.** No design brief was supplied (`inputs.design_brief: []`) and Figma MCP is not connected (`inputs.figma_mcp: "not-connected"`). Per core rule 4.5 this run is **`BEHAVIOUR-ONLY`**: screens, states, flows and data binding are drawn from `stories.md` and `requirements.md`; no component inventory, token names, or frame IDs are produced, and layout is out of scope rather than guessed. This governs why `web-app.design-system`, `web-app.responsive` and `web-app.browser-support` below are `BLOCKED` — it is stated once here and not repeated in each section.

**Explicitly out of scope:**
- Any backend behavior — schema, endpoint shape, migration, or data-residency constraint. Those belong to `backend-api.md` and are referenced, not restated.
- Any other frontend surface (mobile, admin). Not selected for this run (`RUN-CONTEXT.selection: ["web-app"]`) — I-1.
- Note creation (`POST /notes`, `API-NOTES-002@1`). No supplied story exercises note creation; every story operates on pre-existing notes.
- Visual design, token values, and exact breakpoints — `BEHAVIOUR-ONLY` scope (above).

<!-- /SECTION: web-app.overview -->
<!-- SECTION: web-app.routing | status: DERIVED -->
## Routing

**Screens evidenced by the inputs** (names, not URL paths — see gap below):

| Screen | Evidence | Protected |
|---|---|---|
| Notes List (main) | pinned-first list, search, tag filter (BR-001–BR-003, US-001-01…06, US-002-01…06, US-003-01…06) | Yes |
| Note Detail | single-note view with pin/unpin toggle; renders differently when the note is archived (pin control hidden — BR-003) (US-001-03) | Yes |
| Archived Notes List | read-only pin indicator, never pin-sorted (BR-006, US-001-06, US-004-02) | Yes |
| Archive Confirmation | dialog/screen shown when archiving; states "this note is pinned" conditionally (BR-009, US-004-06) | Yes |

**Protected-route rule.** All four screens require an authenticated session: pin/unpin actions require a valid session/token on 100% of requests (NFR-011, `requirements.md`), and the one session-expiry case given in the inputs (US-001-03 AC2) occurs on an action within one of these screens. No screen in the inputs is reachable without authentication.

**Unauthenticated redirect.** Not stated for initial (page-load) access — see **GAP-WEB-004**. The only redirect-adjacent behavior evidenced is mid-flow (see `web-app.auth-flows`).

**Layout nesting.** Not stated by any input (no design brief/platform doc describing a shared shell, header, or nav chrome). Not fabricated.

**Gaps:**
- **GAP-WEB-003** — Exact URL path strings for the four screens above are not stated by any input (`inputs.design_brief: []`, `inputs.platform: []`). Screen names and relationships above are cited from stories; paths are not invented. Closed by: a platform/routing doc or a product decision naming paths.
- **GAP-WEB-004** — Behavior on unauthenticated initial access to any of the four screens (redirect target, whether a login screen exists at all) is not stated by any input. Closed by: an auth/login flow doc.

<!-- /SECTION: web-app.routing -->
<!-- SECTION: web-app.module-map | status: DERIVED -->
## Module map

| Module | Owns | Key behaviors | Citations |
|---|---|---|---|
| `notes-list` | Notes List screen | pinned-first ordering; secondary newest-first sort; search/tag-filter composition; pin/unpin toggle with in-flight disable and optimistic-revert-on-failure | BR-001, BR-002, BR-007, BR-008; US-001-01/02/04/05, US-002-01…06, US-003-01…06 |
| `note-detail` | Note Detail screen | pin/unpin toggle from detail; pin control hidden when the note is archived; session-expiry rejection of the toggle | BR-003; US-001-03 |
| `archive-restore` | Archived Notes List, Archive Confirmation | archive removes note from active list and pinned ordering immediately; pin shown read-only and never sorted in archived list; restore re-applies original pin state with safe default on missing field; sequential unpin-then-archive with no inconsistent intermediate state; conditional pinned-notice on confirmation | BR-004, BR-005, BR-006, BR-009, BR-010; US-001-06, US-004-01…06 |

No other module is evidenced. `archive-restore`'s write operations depend on a backend endpoint not yet assigned an `API-` ID — see **GAP-WEB-005** in `web-app.state-management`.

<!-- /SECTION: web-app.module-map -->
<!-- SECTION: web-app.state-management | status: DERIVED -->
## State management

**Server state** (fetched, not locally owned): note records including `pinned`, via `GET /notes` (`API-NOTES-001@1`, Notes List) and `GET /notes/{id}` (`API-NOTES-003@1`, Note Detail) — per `backend-api.md → backend.api-surface`.

**Client state** (locally owned, not persisted server-side):
- Per-note in-flight pin-toggle indicator, disabling the control until the request resolves (BR-007).
- Search term / tag-filter selection (ephemeral UI input; no input states it is persisted across sessions).
- Per-note error indicator shown after a failed toggle, cleared on retry or dismissal (BR-008).

**Cache/invalidation policy:**
- **Pin/unpin toggle** — applied optimistically to the client's note cache; the list re-sorts immediately (BR-001, BR-002) via `PATCH /notes/{id}` (`API-NOTES-004@1`, ASM-BE-004 per `backend-api.md`). On request failure, the optimistic change is reverted and an error indicator is shown on the affected note (BR-008); no refetch of the full list is required by any input.
- **Archive** — applied optimistically: the note disappears from the active list and from pinned-first ordering immediately (BR-004, US-004-01 AC1/AC2). On failure, the note remains visible with its prior pinned state and an error message, with no partial state shown (US-004-01 AC3).
- **Restore** — the restored note re-enters the active list honoring its pinned flag from the server response; if that response omits `pinned`, the client defaults the note to unpinned display rather than guessing (US-004-03 AC3, BR-005).
- **Sequential unpin-then-archive** — the client serializes the two requests and waits for each confirmation before updating the UI, so no intermediate state shows both actions partially applied (BR-010). If the first (unpin) succeeds and the second (archive) fails, the note shows unpinned-but-present, not archived (US-004-05 AC2).

**BLOCKED sub-item — archive/restore endpoint.** `backend-api.md → backend.api-surface` records its own **GAP-BE-005**: no method or path is assigned to the archive/restore operation. This surface cannot bind the cache-invalidation behavior above to a pinned `API-` ID until that gap is closed.
- **GAP-WEB-005** — Archive/restore has no pinned `API-` ID to integrate against. Degrades: the `archive-restore` module's eventual `api-integration` section (module-level, not part of this document). Closed by: backend assigning `API-NOTES-00n@v` to the archive/restore operation.
- **HANDOFF-WEB-001** — To `backend`: the archive and restore operations exercised by EP-004 (US-004-01, US-004-03, US-004-05) need a contract-assigned endpoint; without one, the web-app cannot build this module's data layer. What breaks if unresolved: the `archive-restore` module ships with no server integration.

**GAP-WEB-006** — No input names a data-fetching/caching library or technology (`feedback.md` states only "keep it minimal" as a framework preference, not a selection). Closed by: a platform/stack decision.

<!-- /SECTION: web-app.state-management -->
<!-- SECTION: web-app.auth-flows | status: DERIVED -->
## Auth flows

**Mechanism.** Referenced only, not restated: `backend-api.md → backend.auth-model` names self-managed JWT/session authentication. This surface treats the token format as backend's concern.

**Login.** Not stated by any input — no login screen, field set, or endpoint is named. **GAP-WEB-007.**

**Token refresh / logout.** Not stated by any input. **GAP-WEB-008.**

**Session expiry — mid-flow (cited).** The only session-expiry behavior any input states: *"Given the user's session has expired, when the team member toggles the pin control on the detail view, then the action is rejected and the user is prompted to log in again"* (US-001-03 AC2). This is specified for the pin toggle on Note Detail only.

**Session expiry — general case.** Not stated for any other mutating action (archive, restore, unpin-then-archive sequence) or for initial page load. **GAP-WEB-009** — closed by: a session-handling policy doc covering all mutating flows, not just the one evidenced case.

<!-- /SECTION: web-app.auth-flows -->
<!-- SECTION: web-app.authorization-ui | status: DRAFT-UNVERIFIED -->
## Authorization UI

| Role | Visible navigation | Enabled actions |
|---|---|---|
| **Team member** (authenticated) | Notes List, Note Detail, Archived Notes List (read-only) | View/search/filter list and detail; pin/unpin via list or detail with in-flight disable and failure-revert (BR-001, BR-002, BR-007, BR-008; US-001-01…05, US-002-01…06, US-003-01…06); view Archived Notes List with pin shown read-only, no pin control exposed (BR-003, BR-006; US-001-06, US-004-02) |
| **Team lead** (authenticated) | Notes List, Note Detail, Archived Notes List, Archive Confirmation | All Team member actions, **plus**: archive a note including a pinned one, via the Archive Confirmation screen with conditional pinned-notice (BR-009; US-004-01, US-004-06); restore an archived note with pin state preserved (BR-005; US-001-06 AC2, US-004-03); perform unpin-then-archive as a serialized sequence (BR-010; US-004-05) |

**Excluded roles** (named in `backend-api.md → backend.auth-model` but not modelled here, per I-1):
- *Notes API consumer* — the web-app's own backend-integration persona, not a human sign-in role.
- *Backend engineer, QA engineer, SRE, Compliance reviewer, Platform-DevOps engineer* — process/pipeline roles with no route on this surface.

**ASM-WEB-001** — The Team-lead-only gating of archive/restore above is inferred from a consistent pattern across stories: every archive/restore story (EP-004, plus US-001-06 AC2, US-002-04 AC2) names "Team lead" as actor, and every pin/unpin/search/view story (EP-001–EP-003) names "Team member," with no story showing a Team member archiving or being denied it. No business rule or NFR states this as an enforced permission rule. If wrong — i.e., if any authenticated user may archive — this role split should be flattened to one role. Confirmed by: a product/authorization decision stating the rule explicitly.

**GAP-WEB-010** — `backend-api.md → backend.auth-model` (ASM-BE-005) treats "Team member" and "Team lead" as a **single** role for the Note-resource permission matrix, since no input states a backend permission difference. If that holds, any client-side-only gating per ASM-WEB-001 above is cosmetic, not a security boundary — a Team member could archive by calling the same endpoint directly. Closed by: backend confirming (or denying) a role-differentiated permission on the archive/restore operation.

<!-- /SECTION: web-app.authorization-ui -->
<!-- SECTION: web-app.design-system | status: BLOCKED -->
## Design system

**BLOCKED.** No design brief was supplied (`inputs.design_brief: []`) and Figma MCP is not connected (`inputs.figma_mcp: "not-connected"`), placing this run in `spec_mode: BEHAVIOUR-ONLY` (core rule 4.5), under which tokens, type scale, spacing and breakpoints are out of scope rather than guessed.

**GAP-WEB-011** — Design tokens, type scale, spacing scale, and breakpoint values are not stated by any input. Closed by: a design brief document, or a connected Figma MCP session.

<!-- /SECTION: web-app.design-system -->
<!-- SECTION: web-app.responsive | status: BLOCKED -->
## Responsive behavior

**BLOCKED.** Breakpoints and their layout changes must be cited from a design input; none was supplied (same cause as `web-app.design-system`).

**GAP-WEB-012** — Breakpoint values and the layout change at each are not stated by any input. Closed by: a design brief document, or a connected Figma MCP session.

<!-- /SECTION: web-app.responsive -->
<!-- SECTION: web-app.browser-support | status: BLOCKED -->
## Browser support

**BLOCKED.** No input names a supported-browser list or minimum version. Per core rule 2, this is not assumed ("it is conventional to support the last two versions of evergreen browsers" is not a citation).

**GAP-WEB-013** — Supported browsers and minimum versions are not stated by any input. Closed by: a platform/browser-support policy document.

<!-- /SECTION: web-app.browser-support -->
<!-- SECTION: web-app.accessibility | status: DERIVED -->
## Accessibility

**Target standard (partial, cited):** WCAG 2.1 AA, explicitly for two scoped requirements:
- Pin/unpin controls are keyboard-operable and announce pinned-state changes to screen readers; keyboard focus plus `aria-pressed` state on all pin controls (NFR-008, `requirements.md`).
- The visual indicator distinguishing pinned from unpinned notes meets a contrast ratio ≥4.5:1 (NFR-009, `requirements.md`).

**Verification (as stated):** the acceptance criteria above are themselves the verification target for these two items. No automated tool, manual-audit cadence, or broader conformance scope is named.

**GAP-WEB-014** — A full-application accessibility target (beyond the two pin-specific requirements above) and its verification method (automated scan, manual audit, assistive-tech testing) are not stated by any input. Closed by: an accessibility policy document or an explicit broader WCAG conformance target.

<!-- /SECTION: web-app.accessibility -->
<!-- SECTION: web-app.performance | status: DERIVED -->
## Performance

**Note on citation scope.** The targets below cite `requirements.md`'s `NFR-001`–`NFR-007`, which describe **client-side render/interaction** performance. `backend-api.md` independently defines its own `NFR-001`–`NFR-011` for **server-side API** performance under the same numbering scheme — these are two different documents' independent ID sequences, not the same requirements; they are not conflated here.

| Target | Value | Source |
|---|---|---|
| List render with pinned-first ordering, 10,000 notes | ≤300ms | `requirements.md` NFR-001 |
| Text search across 10,000 notes, pinned-first | ≤300ms | `requirements.md` NFR-002 |
| Tag filter across 10,000 notes, pinned-first | ≤300ms | `requirements.md` NFR-003 |
| Client-side re-sort after toggle confirmation | ≤100ms | `requirements.md` NFR-004 |
| Scroll smoothness on 10,000-note list | ≥50fps sustained | `requirements.md` NFR-005 |
| SLA holds as volume grows to the benchmark size | no degradation beyond above targets at 10,000 notes | `requirements.md` NFR-006 |
| Non-pin workflow regression (plain search/archive-filtered view) | ≤5% variance vs. pre-pin baseline | `requirements.md` NFR-007 |

**Largest-payload route:** Notes List (main), backed by `GET /notes` (`API-NOTES-001@1`), under the explicit 10,000-note benchmark dataset (`requirements.md` NFR-001, NFR-006; corroborated server-side by `backend-api.md` NFR-001, NFR-003).

**Route-level code splitting:** not stated by any input. **GAP-WEB-015.**

**Bundle-size budget:** not stated by any input (`feedback.md`'s "keep it minimal" is a stack preference, not a budget figure). **GAP-WEB-016.**

<!-- /SECTION: web-app.performance -->
<!-- SECTION: web-app.coverage | status: VERIFIED -->
## Story → requirement coverage

| Story | Requirement(s) covering it |
|---|---|
| US-001-01 | BR-001, BR-008 |
| US-001-02 | BR-001, BR-002 |
| US-001-03 | BR-003, NFR-011 |
| US-001-04 | BR-001 |
| US-001-05 | BR-001 |
| US-001-06 | BR-003, BR-005 |
| US-002-01 | BR-001, BR-008 |
| US-002-02 | BR-002 |
| US-002-03 | BR-001 |
| US-002-04 | NFR-009, BR-005 |
| US-002-05 | BR-001 |
| US-002-06 | BR-007 |
| US-003-01 | NFR-001, NFR-006 |
| US-003-02 | NFR-002 |
| US-003-03 | NFR-003 |
| US-003-04 | NFR-004, BR-008 |
| US-003-05 | NFR-005 |
| US-003-06 | NFR-007 |
| US-004-01 | BR-004 |
| US-004-02 | BR-006 |
| US-004-03 | BR-005 |
| US-004-04 | BR-004 |
| US-004-05 | BR-010 |
| US-004-06 | BR-009 |

All 24 supplied stories are covered. No story is uncovered. (NFR-010 and NFR-011's broader security framing apply cross-cuttingly across the pin-display and auth-gated stories above and are not independently tied to a single story.)

<!-- /SECTION: web-app.coverage -->
