---
id: agent-ba/web_app/rollup.md
type: spec
project: b0326ad3-78d5-4254-8e5e-32f701c09bc2
version: 0.1.1
author: c420e621-a820-5b3a-b15f-ca557d09cc88
produces: agent-ba/web_app/rollup.md
depends_on: []
commit_kind: append
confidence: agent
run_id: 7213f693-d9f9-5517-98a8-e5c8999a9b44
---

# Business Analysis — D-COPSCAR

## Discovery

**Brief Quality:** thin (Problem: weak, Outcomes: weak, Stakeholders: weak)

**Declaration:** Thin. CR states the mechanic (pin flag, sort-first) but gives no success metric, no permission rule (who may pin: owner only vs. any member vs. lead-only, unlike archive which is explicitly lead-only in S-03 #3), and no interaction rule with archive (does archiving clear the pin? can an archived note be pinned?) or with the 300ms/10k-note SLA (S-04) once sort logic changes. Assumptions used below: (1) pin is a per-note boolean settable by any team member per CR wording 'a team member can pin a note', not restricted like archive; (2) no cap on number of pinned notes; (3) archived notes are excluded from the pinned-first list (archive hiding, S-03 #3, takes precedence); (4) the existing <300ms SLA (S-04) applies unchanged to the pinned-first sorted list.

## Business Objectives

### OBJ-001: Team members find high-priority notes faster via pinning in the web UI.
- **Leading KPI:** % of active teams with at least one pinned note within 30 days of launch
- **Lagging KPI:** Reduction in search/filter actions per session for notes older than 7 days
- **Target:** 60% of active teams pin ≥1 note within 30 days; 20% reduction in repeat searches by day 60
- **Impact:** Notes list becomes the trusted first stop for known-important notes instead of a search chore.

### OBJ-002: Pinned-first sort preserves the existing list/search performance SLA.
- **Leading KPI:** p95 client-perceived list render time with pinned notes present, at up to 10,000 notes
- **Lagging KPI:** Sustained <300ms end-to-end list/search response (per S-04) measured post-launch
- **Target:** p95 render ≤300ms at 10k notes, no regression vs. pre-pin baseline, verified at launch and 60 days
- **Impact:** Pin feature ships without degrading the core list/search experience users already rely on.

### OBJ-003: Pin and archive states remain unambiguous and non-conflicting for team leads.
- **Leading KPI:** % of UAT/test cases covering pin+archive interaction (e.g. archiving a pinned note) that pass
- **Lagging KPI:** Production defect/support-report rate for pinned/archived note confusion
- **Target:** 100% of pin+archive interaction test cases pass before launch; 0 related defects in first 60 days
- **Impact:** Team leads trust that archiving still hides notes correctly regardless of pin state, preserving S-03 #3 guarantees.

## EP-001: Pin/Unpin Note Action (→ OBJ-001)
Let a team member mark a note as pinned/unpinned from the note list and detail views, so important notes are flagged for fast retrieval.

### US-001-01: As a team member viewing the note list, I want to pin an important note from the list view, so that mark high-priority notes so they surface first for fast retrieval, driving adoption of pinning
**Priority:** Must Have

**Acceptance Criteria:**
- Given an unpinned note is visible in the note list, when the team member clicks the pin control on that note, then the note is marked pinned and moves to the top of the list above all unpinned notes
- Given the pin request fails (e.g. network/API error), when the team member clicks the pin control, then the note remains unpinned in the list and an error message is shown

**Test Cases:**
- **TC-001-01**: the team member clicks the pin control on that note
- **TC-001-02**: [error] the team member clicks the pin control

### US-001-02: As a team member viewing the note list, I want to unpin a note from the list view, so that remove outdated pinned notes so the pinned section stays relevant for fast retrieval
**Priority:** Must Have

**Acceptance Criteria:**
- Given a pinned note is visible at the top of the list, when the team member clicks the unpin control on that note, then the note is marked unpinned and moves back into newest-first order among unpinned notes
- Given multiple notes are pinned, when one pinned note is unpinned, then the remaining pinned notes stay pinned and retain their relative order at the top

**Test Cases:**
- **TC-001-03**: the team member clicks the unpin control on that note
- **TC-001-04**: [edge] one pinned note is unpinned

### US-001-03: As a team member viewing a note's detail view, I want to pin or unpin the note from the detail screen, so that flag or unflag the note as high-priority without returning to the list, supporting faster retrieval
**Priority:** Must Have

**Acceptance Criteria:**
- Given a note's detail view shows it as unpinned, when the team member toggles the pin control to pin it, then the detail view shows the note as pinned and the state persists on reload
- Given the user's session has expired, when the team member toggles the pin control on the detail view, then the action is rejected and the user is prompted to log in again

**Test Cases:**
- **TC-001-05**: the team member toggles the pin control to pin it
- **TC-001-06**: [error] the team member toggles the pin control on the detail view

### US-001-04: As a team member browsing the note list, I want to see pinned notes displayed first, ahead of newest-first ordering, so that quickly find high-priority notes without scanning the full list, raising pinning adoption
**Priority:** Must Have

**Acceptance Criteria:**
- Given the list contains both pinned and unpinned notes, when the team member opens the note list, then all pinned notes appear first, followed by unpinned notes in newest-first order
- Given no notes are pinned, when the team member opens the note list, then notes display in standard newest-first order with no pinned section shown

**Test Cases:**
- **TC-001-07**: the team member opens the note list
- **TC-001-08**: [edge] the team member opens the note list

### US-001-05: As a team member searching or filtering notes, I want to see pin status reflected correctly while searching by text or filtering by tag, so that retain fast access to high-priority notes even within filtered results
**Priority:** Should Have

**Acceptance Criteria:**
- Given a search or tag filter returns multiple matching notes including pinned ones, when results are displayed, then pinned notes among the results appear first, followed by unpinned matches newest-first
- Given a search or filter returns only unpinned notes, when results are displayed, then notes display in standard newest-first order without a pinned grouping

**Test Cases:**
- **TC-001-09**: results are displayed
- **TC-001-10**: [edge] results are displayed

### US-001-06: As a team member viewing an archived note, I want to see that pin controls are unavailable on archived notes, so that avoid confusion about pin state on notes that are hidden from the active list
**Priority:** Should Have

**Acceptance Criteria:**
- Given a note is archived and was previously pinned, when the team member views it in the archived notes list, then the note shows no active pin control and is not displayed in the pinned section
- Given an archived pinned note is restored by a team lead, when the note reappears in the active note list, then it is shown pinned and listed ahead of unpinned notes

**Test Cases:**
- **TC-001-11**: [edge] the team member views it in the archived notes list
- **TC-001-12**: the note reappears in the active note list

## EP-002: Pinned-First Notes List Display (→ OBJ-001)
Render the notes list with pinned notes grouped/sorted ahead of unpinned notes (still newest-first within each group) so pinning actually surfaces priority notes.

### US-002-01: As a team member, I want to pin a note directly from the notes list or note detail view, so that surface high-priority notes for the team immediately, driving pinning adoption toward the 30-day KPI
**Priority:** Must Have

**Acceptance Criteria:**
- Given an unpinned note is visible in the list or detail view, when the team member clicks the pin control, then the note is marked pinned and immediately re-sorted to the top of the list
- Given the pin request fails (network/API error), when the team member clicks the pin control, then the UI reverts the note to unpinned state and shows an inline error message

**Test Cases:**
- **TC-002-01**: the team member clicks the pin control
- **TC-002-02**: [error] the team member clicks the pin control

### US-002-02: As a team member, I want to unpin a note that was previously pinned, so that keep the pinned section limited to currently relevant priorities
**Priority:** Must Have

**Acceptance Criteria:**
- Given a note is currently pinned and shown at the top of the list, when the team member clicks the unpin control, then the note moves out of the pinned group and re-sorts into the unpinned group by newest-first
- Given the unpinned note has the same creation timestamp as another unpinned note, when the list re-sorts after unpinning, then the two notes retain a stable, deterministic order (e.g. by id) within the unpinned group

**Test Cases:**
- **TC-002-03**: the team member clicks the unpin control
- **TC-002-04**: [edge] the list re-sorts after unpinning

### US-002-03: As a team member, I want to view the notes list with pinned notes grouped ahead of unpinned notes, so that find high-priority notes faster without scrolling, satisfying the epic's core objective
**Priority:** Must Have

**Acceptance Criteria:**
- Given the team has a mix of pinned and unpinned notes, when the team member opens the notes list, then all pinned notes render first (newest-first among themselves), followed by all unpinned notes (newest-first)
- Given no notes are pinned, when the team member opens the notes list, then the list renders as a single newest-first group with no visual pinned section

**Test Cases:**
- **TC-002-05**: the team member opens the notes list
- **TC-002-06**: [edge] the team member opens the notes list

### US-002-04: As a team member, I want to see a clear visual indicator on pinned notes in the list, so that distinguish pinned priority notes from regular notes at a glance
**Priority:** Must Have

**Acceptance Criteria:**
- Given a note is pinned, when the notes list renders, then the note displays a pin icon/badge distinct from unpinned notes
- Given a note is archived and also pinned, when a team lead restores it from archive, then the restored note retains its pinned status and renders in the pinned group

**Test Cases:**
- **TC-002-07**: the notes list renders
- **TC-002-08**: [edge] a team lead restores it from archive

### US-002-05: As a team member, I want to search or filter notes by text/tag while pinned notes remain grouped ahead of matching unpinned notes, so that quickly locate priority notes even when narrowing results, preserving the KPI benefit during search
**Priority:** Must Have

**Acceptance Criteria:**
- Given a search term or tag filter matches both pinned and unpinned notes, when the team member applies the search/filter, then matching pinned notes render first (newest-first), followed by matching unpinned notes (newest-first)
- Given a search term or tag filter matches only unpinned notes, when the team member applies the search/filter, then the filtered list shows only those unpinned notes, newest-first, with no empty pinned section displayed

**Test Cases:**
- **TC-002-09**: the team member applies the search/filter
- **TC-002-10**: [edge] the team member applies the search/filter

### US-002-06: As a team member, I want to have the pin/unpin control remain responsive while a toggle request is in flight, so that avoid duplicate pin actions or confusing list jumps that undermine trust in pinning
**Priority:** Should Have

**Acceptance Criteria:**
- Given the team member has just clicked pin on a note, when the toggle request is still pending, then the pin control is disabled and shows a loading state until the request resolves
- Given the team member rapidly double-clicks the pin control, when the first toggle request is still in flight, then only one toggle request is sent and the second click is ignored

**Test Cases:**
- **TC-002-11**: the toggle request is still pending
- **TC-002-12**: [edge] the first toggle request is still in flight

## EP-003: List/Search Rendering Performance with Pins (→ OBJ-002)
Ensure client-side list/search rendering with pinned-first sort stays within the 300ms SLA at 10,000 notes; no perceptible regression from the pin feature.

### US-003-01: As a team member browsing the notes list, I want to load the notes list with pinned notes rendered first, so that see pinned notes immediately without any added wait, keeping list render within the 300ms SLA at 10,000 notes
**Priority:** Must Have

**Acceptance Criteria:**
- Given 10,000 notes exist with some pinned, when the team member opens the notes list, then pinned notes render at the top and the full list is visible within 300ms (p95)
- Given no notes are pinned, when the team member opens the notes list, then the list renders in newest-first order within 300ms (p95), matching pre-pin performance
- Given the pinned-sort computation fails client-side, when the list attempts to render, then the UI falls back to newest-first order and displays the list rather than blocking or erroring

**Test Cases:**
- **TC-003-01**: the team member opens the notes list
- **TC-003-02**: [edge] the team member opens the notes list
- **TC-003-03**: [error] the list attempts to render

### US-003-02: As a team member searching for notes, I want to run a text search across 10,000 notes with pinned-first ordering applied to results, so that find notes quickly with pinned ones surfaced first, without the pin feature slowing search below the SLA
**Priority:** Must Have

**Acceptance Criteria:**
- Given 10,000 notes exist and some matching results are pinned, when the team member enters a search term, then matching results render with pinned notes first, within 300ms (p95)
- Given a search matches zero notes, when the team member enters that search term, then an empty-results message renders within 300ms without delay from pin-sort logic

**Test Cases:**
- **TC-003-04**: the team member enters a search term
- **TC-003-05**: [edge] the team member enters that search term

### US-003-03: As a team member filtering by tag, I want to filter the 10,000-note list by a tag with pinned-first ordering preserved, so that quickly narrow results while pinned notes still appear first, within the existing performance SLA
**Priority:** Must Have

**Acceptance Criteria:**
- Given 10,000 notes exist with mixed tags and pin states, when the team member selects a tag filter, then filtered results render pinned-first within 300ms (p95)
- Given a selected tag has no pinned notes among matches, when the filter is applied, then results render in newest-first order within 300ms, with no pinned-sort overhead

**Test Cases:**
- **TC-003-06**: the team member selects a tag filter
- **TC-003-07**: [edge] the filter is applied

### US-003-04: As a team member pinning or unpinning a note, I want to toggle a note's pinned state from the list view, so that immediately see the list re-sort with minimal re-render cost, keeping interactions within the performance budget
**Priority:** Must Have

**Acceptance Criteria:**
- Given the list is showing 10,000 notes, when the team member pins a note, then the list re-renders with that note moved to the pinned section within 300ms (p95)
- Given the pin toggle API call fails, when the team member clicks pin, then the list ordering remains unchanged and an error message is shown without a stalled or broken render

**Test Cases:**
- **TC-003-08**: the team member pins a note
- **TC-003-09**: [error] the team member clicks pin

### US-003-05: As a team member with a large note set, I want to scroll through the 10,000-note list after pinned notes are rendered first, so that continue browsing smoothly so the pin feature introduces no scroll-jank or perceptible regression
**Priority:** Should Have

**Acceptance Criteria:**
- Given 10,000 notes are loaded with a pinned section at the top, when the team member scrolls through the list, then scrolling remains smooth with no additional rendering delay attributable to pin sorting
- Given a large number of notes are pinned (e.g., hundreds), when the team member scrolls past the pinned section into unpinned notes, then render time for the transition stays within the same 300ms (p95) SLA

**Test Cases:**
- **TC-003-10**: the team member scrolls through the list
- **TC-003-11**: [edge] the team member scrolls past the pinned section into unpinned notes

### US-003-06: As a team lead comparing list behavior before and after the pin feature, I want to verify that list/search render performance is unchanged for workflows that don't use pinning, so that confirm the pin feature introduces no regression to existing search and archive-filtered views
**Priority:** Should Have

**Acceptance Criteria:**
- Given 10,000 notes exist, none pinned, with archived notes excluded, when the team lead searches and filters by tag, then render times match the pre-pin-feature baseline within 300ms (p95)
- Given all notes are archived and hidden from the list, when the team lead loads the list, then the empty/near-empty list renders within 300ms with no pin-sort overhead

**Test Cases:**
- **TC-003-12**: the team lead searches and filters by tag
- **TC-003-13**: [edge] the team lead loads the list

## EP-004: Pin + Archive State Clarity (UI) (→ OBJ-003)
Make pin and archive states visually unambiguous for team leads: archiving a pinned note removes it from the main list like any other note, and the archived/restore views correctly reflect pin state without conflicting signals.

### US-004-01: As a Team lead, I want to archive a note that is currently pinned, so that so the archived note is removed from the main list exactly like any other note, with no special pinned-note exception
**Priority:** Must Have

**Acceptance Criteria:**
- Given a pinned note is visible at the top of the main notes list, when the team lead archives it, then the note disappears from the main list immediately, same as archiving an unpinned note
- Given the pinned note being archived is the only pinned note, when the archive action completes, then the main list re-renders with no pinned section header and the next newest note shown first
- Given the archive request for a pinned note fails on the server, when the team lead attempts to archive it, then the note remains visible and pinned in the main list with an error message, and no partial state change is shown

**Test Cases:**
- **TC-004-01**: the team lead archives it
- **TC-004-02**: [edge] the archive action completes
- **TC-004-03**: [error] the team lead attempts to archive it

### US-004-02: As a Team lead, I want to view the archived notes list and see whether each archived note was pinned, so that so pin state is visible without implying the note is still active or sorted by pin, avoiding conflicting signals
**Priority:** Must Have

**Acceptance Criteria:**
- Given the archived notes list contains a note that was pinned before archiving, when the team lead opens the archived notes list, then the note shows a pin indicator badge but is NOT moved ahead of other archived notes
- Given the archived list contains both pinned and unpinned archived notes, when the list renders, then ordering follows the archived list's normal sort (e.g. archive date), never pin status
- Given a note's pin flag is missing or null in the API response for an archived note, when the archived list renders that note, then the UI treats it as unpinned (no indicator shown) rather than showing a broken or ambiguous badge

**Test Cases:**
- **TC-004-04**: the team lead opens the archived notes list
- **TC-004-05**: [edge] the list renders
- **TC-004-06**: [error] the archived list renders that note

### US-004-03: As a Team lead, I want to restore a previously pinned note from the archived list, so that so the note returns to the main list with its original pin state intact and sorted correctly
**Priority:** Must Have

**Acceptance Criteria:**
- Given a note was pinned when it was archived, when the team lead restores it, then the note reappears in the main list within the pinned section, sorted first among pinned notes by its restore/update time
- Given a note was unpinned before being archived, when the team lead restores it, then the note reappears in the main list in standard newest-first order, not in the pinned section
- Given the restore action succeeds on the server but the returned note payload is missing the pinned field, when the main list re-renders, then the UI defaults the note to unpinned display rather than showing it incorrectly pinned

**Test Cases:**
- **TC-004-07**: the team lead restores it
- **TC-004-08**: [edge] the team lead restores it
- **TC-004-09**: [error] the main list re-renders

### US-004-04: As a Team member, I want to see a pinned note vanish from the main list the moment a team lead archives it, so that so the pinned-first ordering never shows a stale or archived note at the top
**Priority:** Should Have

**Acceptance Criteria:**
- Given a team member is viewing the main list with a pinned note at top, when a team lead archives that note, then on next list refresh the note is absent and the pinned section reflects only remaining pinned notes
- Given the archived note was the only pinned note and no others are pinned, when the list refreshes, then no empty pinned section placeholder is shown; the list displays unpinned notes newest-first with no visual gap

**Test Cases:**
- **TC-004-10**: a team lead archives that note
- **TC-004-11**: [edge] the list refreshes

### US-004-05: As a Team lead, I want to unpin a note and then archive it in the same session, so that so pin and archive actions never combine into an inconsistent intermediate state in the UI
**Priority:** Should Have

**Acceptance Criteria:**
- Given a pinned note is open for editing, when the team lead unpins it and then immediately archives it, then the note is removed from the main list and appears in the archived list with no pin indicator
- Given the unpin action succeeds but the subsequent archive action fails, when the team lead views the main list, then the note shows as unpinned but still present (not archived), with no leftover pinned badge

**Test Cases:**
- **TC-004-12**: the team lead unpins it and then immediately archives it
- **TC-004-13**: [error] the team lead views the main list

### US-004-06: As a Team lead, I want to confirm archiving a pinned note via the archive confirmation screen, so that so the confirmation step explicitly communicates the note is pinned, preventing accidental loss of a highlighted note
**Priority:** Could Have

**Acceptance Criteria:**
- Given the team lead opens the archive confirmation dialog for a pinned note, when the dialog renders, then it displays a 'this note is pinned' notice alongside the standard archive warning
- Given the note is not pinned, when the archive confirmation dialog renders, then no pinned notice is shown, only the standard archive warning

**Test Cases:**
- **TC-004-14**: the dialog renders
- **TC-004-15**: [edge] the archive confirmation dialog renders

## Business Rules

### BR-001: Pinned notes are always rendered ahead of unpinned notes in the notes list, regardless of search or tag-filter applied
**Rationale:** Core value proposition of pinning is fast retrieval of high-priority notes in any view context | **Enforcement:** Client-side list/search/filter rendering logic sorts by pinned flag first

### BR-002: Within the pinned group and within the unpinned group, notes are secondarily sorted newest-first
**Rationale:** Preserves existing newest-first expectation while layering pin priority on top | **Enforcement:** Client-side list sort comparator (pinned desc, then created/updated timestamp desc)

### BR-003: Pin/unpin controls are hidden or disabled on archived note views (list and detail)
**Rationale:** Archived notes are inactive; allowing pin toggling would create confusing state on hidden notes | **Enforcement:** Conditional rendering in ArchivedNotesList and archived NoteDetail screens

### BR-004: Archiving a note removes it from the main list and pinned-first ordering immediately, regardless of its pinned state
**Rationale:** Pinned-first ordering must never surface a stale/archived note at the top of the active list | **Enforcement:** Client removes/re-fetches list on archive action; archived notes excluded from active list query

### BR-005: A note's pinned state is preserved unchanged through archive and restore (unarchive) actions
**Rationale:** Team leads expect pin priority to persist so restored notes reappear correctly sorted | **Enforcement:** Restore action re-inserts note into active list honoring existing pinned flag and sort rule

### BR-006: Archived notes list displays pin state as a read-only indicator only; it is never used to sort the archived list
**Rationale:** Prevents implying an archived note is still active or prioritized | **Enforcement:** ArchivedNotesList screen renders pin badge without applying pinned-first sort

### BR-007: The pin/unpin control is disabled (non-interactive) while a toggle request is in flight for that note
**Rationale:** Prevents duplicate pin actions and confusing list re-jump from rapid repeated clicks | **Enforcement:** Client-side per-note loading state disables control until API response resolves

### BR-008: If a pin/unpin API request fails, the client reverts the optimistic UI update and surfaces an error indicator on the affected note
**Rationale:** Maintains trust in pinning by never showing an inconsistent client/server pin state | **Enforcement:** Client error-handling in pin toggle action with rollback of local state

### BR-009: Archiving a currently pinned note requires passing through the archive confirmation screen, which must explicitly state the note is pinned
**Rationale:** Prevents accidental loss of visibility on a highlighted/high-priority note | **Enforcement:** ArchiveConfirmation screen conditionally renders pinned-state messaging when note.pinned is true

### BR-010: Unpinning and archiving a note performed in the same session must apply sequentially with no intermediate state where both actions appear partially applied
**Rationale:** Avoids inconsistent UI state when a team lead performs two actions in quick succession | **Enforcement:** Client serializes unpin and archive requests, waiting for each confirmation before the next UI update

## Non-Functional Requirements

### NFR-001: Notes list renders with pinned-first ordering applied within existing render SLA at scale
**Category:** Performance | **Target:** <=300ms render for 10,000 notes | **Priority:** Must Have

### NFR-002: Text search across the full note set returns pinned-first ordered results within existing SLA
**Category:** Performance | **Target:** <=300ms for search across 10,000 notes | **Priority:** Must Have

### NFR-003: Tag filter across the full note set returns pinned-first ordered results within existing SLA
**Category:** Performance | **Target:** <=300ms for filter across 10,000 notes | **Priority:** Must Have

### NFR-004: Pin/unpin toggle triggers a minimal-cost client re-render/re-sort of the list without a full reload
**Category:** Performance | **Target:** <=100ms client-side re-sort after toggle confirmation | **Priority:** Should Have

### NFR-005: Scrolling through the large note list after pinned-first rendering shows no perceptible jank
**Category:** Performance | **Target:** Sustained >=50fps during scroll on 10,000-note list | **Priority:** Should Have

### NFR-006: Pinned-first list/search/filter performance remains within SLA as note volume grows to at least the benchmark dataset size
**Category:** Scalability | **Target:** SLA holds at 10,000 notes with no degradation beyond stated targets | **Priority:** Must Have

### NFR-007: Existing non-pin workflows (plain search, plain archive-filtered view) show no measurable regression after pin feature introduction
**Category:** Performance | **Target:** <=5% variance vs. pre-pin baseline render time | **Priority:** Must Have

### NFR-008: Pin/unpin controls are keyboard-operable and announce pinned state changes to screen readers
**Category:** Accessibility | **Target:** WCAG 2.1 AA: keyboard focus + ARIA-pressed state on all pin controls | **Priority:** Should Have

### NFR-009: Visual indicator distinguishing pinned notes from unpinned notes meets minimum color/contrast legibility
**Category:** Usability | **Target:** Contrast ratio >=4.5:1 (WCAG AA) for pin indicator | **Priority:** Should Have

### NFR-010: Pin state and controls are consistently represented across list, detail, search/filter, and archive views
**Category:** Usability | **Target:** 0 reported inconsistencies in pin-state display across views in QA pass | **Priority:** Must Have

### NFR-011: Pin/unpin actions are only available to authenticated team members with access to the note workspace
**Category:** Security | **Target:** 100% of pin/unpin requests require valid session/auth token | **Priority:** Must Have
