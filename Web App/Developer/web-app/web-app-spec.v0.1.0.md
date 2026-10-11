---
id: agent-developer/web_app/web-app/WEB-APP-SPEC.md
type: spec
project: b0326ad3-78d5-4254-8e5e-32f701c09bc2
version: 0.1.0
author: c420e621-a820-5b3a-b15f-ca557d09cc88
produces: agent-developer/web_app/web-app/WEB-APP-SPEC.md
depends_on: [US-004-01, US-004-03, US-001-04, US-001-05, US-001-06, US-001-03, US-002-01, US-003-01, US-004-02, US-004-04, US-004-05, US-003-02, US-002-03, US-001-01, US-001-02, US-003-03, US-002-02, US-002-06, US-002-04, US-002-05, US-003-04, US-003-05]
commit_kind: append
confidence: agent
run_id: de21d0c7-ffc3-593d-b762-95f1046f5945
okf_id: web-app/WEB-APP-SPEC.md
---

<!-- SECTION: web-app.overview | status: DERIVED -->
## Overview

This is the end-user web application for a shared team notes system. It is used directly by two actors who authenticate into it: the **Team Member** and the **Team Lead** [US-004-01, US-004-03, requirements.md BR-009, BR-011].

**In scope for this surface:**

- Note authoring: create, edit, delete, with a required title and markdown body [requirements.md BR-001], tag add/remove with client-side de-duplication [requirements.md BR-003, US-001-04], and a live markdown preview rendered by the same renderer used for the saved note's detail view [requirements.md BR-002, US-001-05].
- Discard-on-navigate protection for unsaved form edits [requirements.md BR-004, NFR-007, US-001-06].
- Delete confirmation [requirements.md BR-005, US-001-03].
- List browsing newest-first, free-text search (debounced), single-tag filtering, combined search+tag filtering (AND), pagination/infinite-scroll, and loading/empty/no-results/error feedback for all of the above [requirements.md BR-006, BR-007, BR-008, NFR-001..003, NFR-008, US-002-01..06].
- Lead-only archive and restore of notes, including a dedicated archived-notes view, archive confirmation, and lead-only visibility of archive/restore controls [requirements.md BR-009, BR-010, NFR-005, US-003-01..05, US-004-03].
- Login, session-gated routing for every notes page, logout, and visible (non-silent) feedback when a role-restricted action is rejected or a session expires mid-action [requirements.md BR-011..013, NFR-004, NFR-006, NFR-011, US-004-01, US-004-02, US-004-04, US-004-05].

**Explicitly out of scope for this surface:**

- Note and user data validation/persistence logic, request/response schemas, and error codes. These are owned by the backend contract and referenced by pinned ID only, never restated (core rule 6; see `backend-api.md`).
- The EU-residency compliance report (`API-COMP-001@1` in the backend contract). No story in `stories.md` or `requirements.md` assigns this surface a screen or flow for it; it is not built here. This is a scope boundary, not a missing input, so no `GAP-` is raised for it.
- Third-party analytics of any kind — explicitly prohibited [requirements.md NFR-010].
- Session/token issuance mechanics (algorithm, expiry duration, refresh) — these are backend-owned and, per the backend contract itself, undocumented there too (see `GAP-WEB-004` under Auth Flows below).

<!-- /SECTION: web-app.overview -->
<!-- SECTION: web-app.routing | status: DRAFT-UNVERIFIED -->
## Routing

**Route table:**

| Route | Screen | Layout | Protected | Permission | If unauthenticated |
|---|---|---|---|---|---|
| `/login` | `Login` | `AuthLayout` | No | — | n/a (this is the login route itself) |
| `/notes` | `NotesList` | `AppLayout` | Yes | any authenticated user | → `/login` |
| `/notes/new` | `NoteCreateForm` | `AppLayout` | Yes | any authenticated user | → `/login` |
| `/notes/:id` | `NoteDetail` | `AppLayout` | Yes | any authenticated user | → `/login` |
| `/notes/:id/edit` | `NoteEditForm` | `AppLayout` | Yes | any authenticated user | → `/login` |
| `/notes/archived` | `ArchivedNotesList` | `AppLayout` | Yes | Team Lead only | → `/login` (unauthenticated) or hidden/denied (authenticated Member) |

Screens are taken from `design-brief.md` screens-inventory.

**Protected-route rule:** every route under `AppLayout` requires an active, valid session; a session check runs before content renders [requirements.md BR-011, NFR-004]. An authenticated user who is already logged in and navigates to `/login` is redirected to `/notes` instead of seeing the login form [US-004-02, 3rd Given/When/Then].

**`/notes/archived` permission:** hidden from navigation, and denied if reached directly, for the Member role; visible and reachable for the Lead role [US-003-02, 3rd Given/When/Then; requirements.md BR-009].

**Unauthenticated redirect:** any user with no session or an expired session who requests a protected URL directly is redirected to `/login` [US-004-02, 2nd Given/When/Then].

**Not modeled as routes — ASM-WEB-001:** `design-brief.md`'s screens-inventory lists `ArchiveConfirmation`, `TagFilterPicker`, and `RoleRestrictedFeedback` as screens, but none has an `outgoing` transition, and the corresponding requirements describe them as a confirmation dialog [requirements.md BR-010], an inline filter control on the list [US-002-03], and an inline error message [requirements.md BR-012, NFR-011] respectively — not independent destinations. This spec treats them as a modal and two embedded components rendered within `NotesList`/`NoteDetail` (archive confirmation), `NotesList` (tag filter picker), and globally (role-restricted feedback), not as routes. No input confirms this structural choice directly; it is an assumption from the absence of any outgoing transition plus the dialog/inline language in the requirements.

**Layout nesting — ASM-WEB-002:** the two-layout split (`AuthLayout` for `/login`, `AppLayout` for everything else) is a structural default. No input specifies an explicit layout tree; it is inferred from the protected/unprotected grouping required by BR-011 and US-004-02.

<!-- /SECTION: web-app.routing -->
<!-- SECTION: web-app.module-map | status: DERIVED -->
## Module Map

Grouping follows `design-brief.md`'s screen-to-module column, cross-checked against the story epics in `stories.md`.

- **M1 — Note Form** (`NoteCreateForm`, `NoteEditForm`, `NoteDetail`): create/edit/view a single note, tag chips, markdown preview, delete, discard-unsaved-changes guard. Owns `/notes/new`, `/notes/:id`, `/notes/:id/edit`. [EP-001 / US-001-01..06, requirements.md BR-001..005, NFR-007]
- **M2 — Notes List & Filter** (`NotesList`, embedded `TagFilterPicker`): newest-first list, search, tag filter, combined filter, pagination, loading/empty/no-results/error states. Owns `/notes`. [EP-002 / US-002-01..06, requirements.md BR-006..008, NFR-001..003, NFR-008]
- **M3 — Archive Lifecycle** (`ArchivedNotesList`, embedded `ArchiveConfirmation`): archive (from M1/M2), restore, archived-notes listing, lead-only gating. Owns `/notes/archived`; archive/restore actions are also surfaced inside M1 and M2's note rows per US-003-01 ("directly from the notes list or detail view"). [EP-003 / US-003-01..05, requirements.md BR-009, BR-010, NFR-005]
- **M4 — Auth & Access Feedback** (`Login`, embedded `RoleRestrictedFeedback`): login, session guard, logout, role-restricted-action error feedback. Owns `/login`; the session guard and feedback component are cross-cutting into M1–M3. [EP-004 / US-004-01..05, requirements.md BR-011..013, NFR-004, NFR-006, NFR-011]

M1, M2, and M3 all depend on M4 for the session guard and role state; M4 has no internal dependency. No cyclic dependency exists.

**Design-coverage gaps found while mapping (partial, per core rule 10.2 — not total, not halting):**

- **GAP-WEB-001**: `design-brief.md`'s stories-map lists `US-001-06` (discard unsaved changes) against `NoteEditForm` only. `requirements.md` BR-004 states the rule applies to "the note create/edit form," i.e. both `NoteCreateForm` and `NoteEditForm`. Treated as applying to both until the design brief is corrected.
- **GAP-WEB-002**: `design-brief.md`'s screens-inventory lists `ArchivedNotesList` variants as `Default, Error` only — no `Empty` variant — while `US-003-02`'s second Given/When/Then requires an empty-state message when no notes have been archived yet. The empty state is specified by the story and will be built; the design brief simply does not yet enumerate it as a variant.

<!-- /SECTION: web-app.module-map -->
<!-- SECTION: web-app.state-management | status: DERIVED -->
## State Management

**Server state** (fetched from the backend contract, reference only — core rule 6): the active notes list with current filters/pagination (`API-NOTE-002@1`), a single note (`API-NOTE-003@1`), the archived notes list (`API-NOTE-002@1` with `archived=archived`), and the authenticated session (`API-AUTH-001@1`).

**Cache invalidation policy** (derived from the user-visible effects each mutation must produce):

| Mutation | Invalidates | Evidence |
|---|---|---|
| Create (`API-NOTE-001@1`) | active notes list | new note shown first on return to list [US-001-01, 1st Given/When/Then] |
| Edit (`API-NOTE-004@1`) | that note's detail, active notes list if title/tags shown there | changes visible on reopen [US-001-02, 1st Given/When/Then] |
| Delete (`API-NOTE-005@1`) | active notes list, that note's detail | note no longer appears in list [US-001-03, 1st Given/When/Then] |
| Archive (`API-NOTE-006@1`) | active notes list, archived notes list | removed from active list immediately [US-003-01, 1st Given/When/Then] |
| Restore (`API-NOTE-007@1`) | active notes list, archived notes list | disappears from archived, reappears in active [US-003-03, 1st Given/When/Then] |

**Client state**: the in-progress search query text prior to debounce completion [US-002-02, 1st Given/When/Then], the selected tag filter [US-002-03], the note form's draft fields and a dirty flag driving the discard-confirmation guard [requirements.md BR-004, NFR-007], the tag-chip list within an open form [US-001-04], and a token identifying the most recent in-flight list/search request so that a stale response is discarded if a newer one has since been issued [US-002-06, 2nd Given/When/Then].

**Gaps found, not assumed:**

- **GAP-WEB-003**: `US-001-02`'s third Given/When/Then requires detecting that a note "was already changed by the first" editor and showing a conflict/stale-data message. No input specifies the detection mechanism (version number, `updated_at` comparison, ETag, or otherwise). This spec records the required *behavior* only; the mechanism is undefined pending that artefact.
- **GAP-WEB-004**: no input (including `backend-api.md`, which flags this itself as `GAP-BE-005`) names the session/token scheme, its expiry duration, or any refresh behavior. The client-state boundary above assumes only that "a session either exists or does not" and cannot specify more.

<!-- /SECTION: web-app.state-management -->
<!-- SECTION: web-app.auth-flows | status: DERIVED -->
## Auth Flows

**Login** [US-004-01, `API-AUTH-001@1`]: submitting correct credentials authenticates and redirects to `/notes`; submitting incorrect credentials shows an inline error with no session created; submitting with an empty email or password is blocked client-side with a required-field validation message before any request is sent.

**Session check on every protected route** [requirements.md BR-011, NFR-004; US-004-02]: a valid active session renders the requested page without re-login; no session or an expired session redirects to `/login`; an already-authenticated user requesting `/login` is redirected to `/notes`.

**Logout** [requirements.md BR-013, NFR-006; US-004-04]: clicking logout clears the session client-side and redirects to `/login`. After logout, pressing the browser back button to a notes page redirects to `/login` rather than rendering any cached note content.

**Mid-flow expiry** [US-004-05, 2nd Given/When/Then]: if the session expires between page load and the user submitting a note action, the submission redirects the user to `/login` instead of showing a generic error.

**Stale-role feedback** [US-004-05, 1st Given/When/Then; requirements.md BR-012, NFR-011]: if a role-restricted control was visible due to stale client state and the backend rejects the action with a 403 (`API-NOTE-006@1` / `API-NOTE-007@1`, reference only), a non-blocking "not authorized" message is shown and the note's displayed state is left unchanged — not silently dropped, not a generic failure.

**Gaps:**

- **GAP-WEB-004** (restated from State Management): no input defines the session/token mechanism, so refresh behavior, exact expiry duration, and whether expiry is detected proactively or only on the next request are all unspecified.
- **GAP-WEB-005**: distinct from the mid-action case above (US-004-05) and the direct-navigation case (US-004-02), no input describes whether an idle session that expires while a protected page is simply open (no navigation, no submission in progress) is detected reactively (on the user's next action) or proactively (e.g. background polling). This spec assumes reactive-only detection is sufficient to satisfy the two cited stories, pending a defensible default confirmation from that artefact.

<!-- /SECTION: web-app.auth-flows -->
<!-- SECTION: web-app.authorization-ui | status: VERIFIED -->
## Authorization UI

**Roles that can sign in to this surface:** Team Member and Team Lead. These are the only two roles named anywhere in the supplied inputs [requirements.md BR-009, BR-011; US-004-01, US-004-03; and the backend contract's own actor list in `backend-api.md`'s auth-model section, cited for the role names only — not for schema]. No role is excluded: there is no story, requirement, or contract actor describing a third role, and no cited role lacks a route on this surface.

| Role | Visible navigation | Enabled actions |
|---|---|---|
| Team Member | Notes List, Logout | Create note, edit note, delete note (with confirmation) [requirements.md BR-005], search/filter/paginate, view note detail, markdown preview, add/remove tags [requirements.md BR-001..004, US-001-01..06, US-002-01..06] |
| Team Lead | Notes List, **Archived Notes**, Logout | All Team Member actions, plus: Archive (with confirmation) [requirements.md BR-010, US-003-01], Restore [US-003-03], view Archived Notes list [US-003-02] |

**Hidden from Team Member:** the "Archived Notes" navigation link, the Archive control on any note (list or detail), and the Restore control [US-003-01, 2nd Given/When/Then; US-003-02, 3rd Given/When/Then; requirements.md NFR-005]. Per `US-004-03`'s third Given/When/Then, if role data is missing or unresolved for *any* session, archive/restore controls default to hidden until the role is confirmed — this applies regardless of which role the session ultimately turns out to hold.

<!-- /SECTION: web-app.authorization-ui -->
<!-- SECTION: web-app.design-system | status: DRAFT-UNVERIFIED -->
## Design System

`figma_mcp` is `not-connected` and a design brief is present, so per core rule 4.5 this run is `spec_mode: FULL-FROM-BRIEF`: every token and component name below is taken from `design-brief.md`'s chain-handoff summary and is an **unverified claim** — it has not been checked against the live Figma file.

**Components** (from `components-inventory.md`):

| Component | Role | Variants | States |
|---|---|---|---|
| `Empty/State` | content | 1 | Default |
| `Input/Text` | entry | 5 | Default, Focused, Filled, Error, Disabled |
| `Loading/Skeleton` | content | 1 | Default |

Full prop/binding bodies were not supplied to this chain ("omitted from chain handoff" per `components-inventory.md`); only names, roles, and state lists are available.

**Tokens** (from `tokens-index.md`): 71 tokens supplied as IDs with file-path references only — no values (hex, px, etc.) were inlined. They span five categories: `UI/border/*` (9), `UI/color/*` (19), `UI/icon/*` (10), `UI/surface/*` (14), `UI/text/*` (13). Binding this spec's components to specific tokens is deferred to the corresponding module-level spec, where each binding will cite the specific token ID.

**Gaps:**

- **GAP-WEB-006**: no typography (type-scale) or spacing-scale tokens appear among the 71 supplied IDs. Neither a type scale nor a spacing scale can be stated.
- **GAP-WEB-007**: none of the 71 token IDs carry an inlined value in the supplied handoff; only the ID and a path reference to a file this run does not have. Exact colors, borders, icon treatments, and surface fills cannot be verified from the inputs supplied to this run.

<!-- /SECTION: web-app.design-system -->
<!-- SECTION: web-app.responsive | status: BLOCKED -->
## Responsive

**Blocked.** No input — `design-brief.md`, `requirements.md`, `stories.md`, nor `feedback.md` — states a breakpoint value, a breakpoint count, or a layout change at any breakpoint. `design-brief.md`'s screen variants (`Default`, `Loading`, `Empty`, `Error`, `Filled`) are state variants, not responsive/viewport variants.

**What would unblock this**: a design artefact (breakpoint table, responsive frame set, or an explicit statement in `requirements.md`) naming the supported breakpoints and the layout change at each. Logged as **GAP-WEB-008**.

<!-- /SECTION: web-app.responsive -->
<!-- SECTION: web-app.browser-support | status: BLOCKED -->
## Browser Support

**Blocked.** No input names a supported browser or a minimum version. `platform` is an empty input array in this run's `RUN-CONTEXT`.

**What would unblock this**: a platform or requirements document naming the supported browser matrix and minimum versions. Logged as **GAP-WEB-009**.

<!-- /SECTION: web-app.browser-support -->
<!-- SECTION: web-app.accessibility | status: VERIFIED -->
## Accessibility

**Target standard**: WCAG 2.1 AA conformance across note creation, editing, list, search, filter, and archive controls [requirements.md NFR-009].

**Verification**: not stated by any input. No input names an automated audit tool, a manual testing protocol, or a CI accessibility gate. Logged as **GAP-WEB-010**; closing artefact is an accessibility test-plan document or an addition to `requirements.md` naming the verification method.

<!-- /SECTION: web-app.accessibility -->
<!-- SECTION: web-app.performance | status: DERIVED -->
## Performance

**Cited targets:**

- p95 response-to-render under 300 ms at up to 10,000 notes for list/search/filter results [requirements.md NFR-001].
- Initial list render fetches no more than one page (example given: 50 notes) per request — pagination/infinite scroll, not full-set load [requirements.md NFR-002].
- Tag-filter interaction and list rendering stay responsive (≤300 ms) at up to 10,000 notes [requirements.md NFR-008].

**Largest-payload route (DERIVED)**: `/notes` (`NotesList`, module M2) is the largest-payload route. It is the only route targeted by all three performance NFRs above, it is the route that must sustain the 10,000-note scale target, and it is the one route whose payload varies with user-controlled search/filter/pagination state rather than a single fixed record.

**Gaps:**

- **GAP-WEB-011**: no input states a bundle-size budget (initial load, per-route, or total).
- **GAP-WEB-012**: no input states a route-level code-splitting policy. Given the latency targets on `/notes` above, code-splitting by route is a plausible mitigation, but no input requires or forbids it, so it is not asserted here.

<!-- /SECTION: web-app.performance -->
<!-- SECTION: web-app.coverage | status: DERIVED -->
## Coverage

All 22 stories supplied in `stories.md` are from `EP-001`–`EP-004`; none from `EP-005` (compliance) are present, consistent with Overview's scope statement. Functional requirement IDs (`WEB-<MOD>-FR-nnn`) are minted in the corresponding module-level spec, not in this document (core rule 6 — reference, don't restate); this table records story-to-module ownership and traceability only.

| Story | Module | Primary screen(s) | Note |
|---|---|---|---|
| US-001-01 | M1 | NoteCreateForm | |
| US-001-02 | M1 | NoteEditForm | conflict-detection mechanism open, `GAP-WEB-003` |
| US-001-03 | M1 | NoteDetail | |
| US-001-04 | M1 | NoteCreateForm, NoteEditForm | |
| US-001-05 | M1 | NoteCreateForm, NoteEditForm | |
| US-001-06 | M1 | NoteCreateForm, NoteEditForm | design brief maps only NoteEditForm, `GAP-WEB-001` |
| US-002-01 | M2 | NotesList | |
| US-002-02 | M2 | NotesList | |
| US-002-03 | M2 | TagFilterPicker (embedded in NotesList) | |
| US-002-04 | M2 | NotesList | |
| US-002-05 | M2 | NotesList | |
| US-002-06 | M2 | NotesList | |
| US-003-01 | M3 | NotesList, NoteDetail, ArchiveConfirmation | |
| US-003-02 | M3 | ArchivedNotesList | empty-state variant not yet in design brief, `GAP-WEB-002` |
| US-003-03 | M3 | ArchivedNotesList | |
| US-003-04 | M2 | NotesList | |
| US-003-05 | M3 | ArchiveConfirmation | |
| US-004-01 | M4 | Login | |
| US-004-02 | M4 | Login, AppLayout guard | |
| US-004-03 | M3, M4 | NotesList, NoteDetail, ArchivedNotesList | role gate on archive/restore controls |
| US-004-04 | M4 | AppLayout (logout control) | |
| US-004-05 | M4 | RoleRestrictedFeedback (embedded) | |

All 22 supplied stories are covered. No story is uncovered.

<!-- /SECTION: web-app.coverage -->
