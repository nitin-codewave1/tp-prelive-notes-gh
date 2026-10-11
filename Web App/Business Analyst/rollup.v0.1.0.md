---
id: agent-ba/web_app/rollup.md
type: spec
project: b0326ad3-78d5-4254-8e5e-32f701c09bc2
version: 0.1.0
author: c420e621-a820-5b3a-b15f-ca557d09cc88
produces: agent-ba/web_app/rollup.md
depends_on: []
commit_kind: append
confidence: agent
run_id: 606f48be-ebe8-5671-8655-8928680a875e
---

# Business Analysis — D-COPSCAR

## Discovery

**Brief Quality:** thin (Problem: weak, Outcomes: weak, Stakeholders: strong)

**Declaration:** Brief quality thin - missing explicit business problem statement (why shared notes matter, what breaks today) and adoption/engagement outcome metrics beyond the 300ms performance constraint. Assumptions made: (1) current state is informal/scattered note-taking with no shared tool, (2) success includes team-wide adoption and sustained use, not just technical delivery, (3) team size/volume implied by 'up to 10,000 notes' constraint, not stated directly as current scale.

## Business Objectives

### OBJ-001: Give the team a single, searchable home for shared knowledge, reducing time lost to scattered/duplicated notes.
- **Leading KPI:** Number of notes created and % tagged within first 30 days
- **Lagging KPI:** Weekly active users performing search/list actions
- **Target:** 80% of team members log in and create or search at least one note within 30 days of launch
- **Impact:** Team knowledge consolidates into one source of truth instead of scattered informal channels

### OBJ-002: Keep note retrieval fast as the team's note volume grows, so the tool stays usable at scale.
- **Leading KPI:** p95 response time for list/search endpoint under current note volume
- **Lagging KPI:** p95 response time for list/search sustained under 300ms at 10,000 notes (S-04)
- **Target:** List/search p95 < 300ms at 10,000 notes, measured continuously post-launch
- **Impact:** Tool remains viable and trusted as data grows instead of degrading into a slow, abandoned system

### OBJ-003: Give team leads control over note lifecycle so the active note list stays relevant and uncluttered.
- **Leading KPI:** Number of archive/restore actions performed by leads per month
- **Lagging KPI:** Ratio of archived-to-active notes stabilizes (no unbounded growth of stale active notes)
- **Target:** Leads archive outdated notes within 14 days of identification; active list contains no notes flagged stale > 30 days
- **Impact:** Note list stays navigable and trustworthy rather than cluttered with outdated content

### OBJ-004: Protect the team's data and meet compliance obligations via access control and EU residency.
- **Leading KPI:** % of access attempts authenticated via login (no bypass) and % of infrastructure confirmed EU-hosted
- **Lagging KPI:** Zero data residency or unauthorized-access incidents post-launch
- **Target:** 100% login-gated access and 100% EU data residency verified at launch; zero compliance incidents in first 90 days
- **Impact:** Organization avoids compliance risk and builds user trust that shared notes are secure and properly governed

## EP-001: Note Creation, Editing & Deletion UI (→ OBJ-001)
Forms/views for creating, editing and deleting notes (title, markdown body, tags); core workflow that drives note volume and tagging adoption.

### US-001-01: As a Team member, I want to create a new note with a title, markdown body and tags, so that quickly capture shared knowledge so note volume grows toward the 30-day creation KPI
**Priority:** Must Have

**Acceptance Criteria:**
- Given the team member is logged in and on the new note screen, when they enter a title, markdown body, at least one tag, and click Save, then the note is created and the team member is returned to the notes list with the new note shown first
- Given the team member is on the new note screen, when they click Save with the title field empty, then the form shows an inline validation error and the note is not saved
- Given the team member is creating a note, when they click Save without entering any tags, then the note is saved successfully with an empty tag list

**Test Cases:**
- **TC-001-01**: they enter a title, markdown body, at least one tag, and click Save
- **TC-001-02**: [error] they click Save with the title field empty
- **TC-001-03**: [edge] they click Save without entering any tags

### US-001-02: As a Team member, I want to edit the title, markdown body or tags of an existing note, so that keep shared notes accurate and up to date, supporting reliable team reuse of the knowledge base
**Priority:** Must Have

**Acceptance Criteria:**
- Given the team member opens an existing note they can edit, when they change the title, body text, or tags and click Save, then the note is updated and the changes are visible when the note is reopened
- Given the team member is editing a note, when they remove all existing tags and save, then the note is saved with no tags without error
- Given two team members have the same note open for editing, when the second member saves after the note was already changed by the first, then the UI shows a conflict/stale-data message instead of silently overwriting

**Test Cases:**
- **TC-001-04**: they change the title, body text, or tags and click Save
- **TC-001-05**: [edge] they remove all existing tags and save
- **TC-001-06**: [error] the second member saves after the note was already changed by the first

### US-001-03: As a Team member, I want to delete a note they no longer need, so that keep the shared notes list free of clutter, directly supporting fast search/list performance
**Priority:** Must Have

**Acceptance Criteria:**
- Given the team member is viewing a note, when they click Delete and confirm the action, then the note is removed and no longer appears in the notes list
- Given the team member clicks Delete, when the confirmation dialog appears, then clicking Cancel leaves the note unchanged and still in the list
- Given the note was already deleted by another user, when the team member attempts to delete it again, then the UI shows a 'note no longer exists' message instead of a generic failure

**Test Cases:**
- **TC-001-07**: they click Delete and confirm the action
- **TC-001-08**: [edge] the confirmation dialog appears
- **TC-001-09**: [error] the team member attempts to delete it again

### US-001-04: As a Team member, I want to add or remove tags while creating or editing a note, so that improve tagging adoption, directly driving the % tagged KPI
**Priority:** Must Have

**Acceptance Criteria:**
- Given the team member is in the note form, when they type a tag and press Enter/comma, then the tag is added as a visible chip and included on Save
- Given the team member has added a tag chip, when they click the chip's remove icon, then the tag is removed from the note before saving
- Given the team member is adding a tag, when they try to add a duplicate of an existing tag on the note, then the duplicate is ignored and only one instance of the tag remains

**Test Cases:**
- **TC-001-10**: they type a tag and press Enter/comma
- **TC-001-11**: they click the chip's remove icon
- **TC-001-12**: [edge] they try to add a duplicate of an existing tag on the note

### US-001-05: As a Team member, I want to preview the rendered markdown while writing a note's body, so that write well-formatted notes confidently, encouraging more notes to be created and shared
**Priority:** Should Have

**Acceptance Criteria:**
- Given the team member is typing markdown in the body field, when they switch to the Preview tab, then the rendered HTML output matches the markdown entered
- Given the team member has entered malformed markdown syntax, when they switch to Preview, then the preview renders the text as plain text/best-effort without crashing the form

**Test Cases:**
- **TC-001-13**: they switch to the Preview tab
- **TC-001-14**: [edge] they switch to Preview

### US-001-06: As a Team member, I want to discard unsaved changes when leaving the note form, so that avoid accidental loss of work, keeping note creation friction-free and supporting creation volume KPI
**Priority:** Could Have

**Acceptance Criteria:**
- Given the team member has unsaved edits in the note form, when they navigate away without saving, then a confirmation prompt asks whether to discard changes
- Given the confirmation prompt is shown, when the team member confirms discard, then the form closes and no changes are persisted to the note

**Test Cases:**
- **TC-001-15**: they navigate away without saving
- **TC-001-16**: [edge] the team member confirms discard

## EP-002: Fast Note List, Search & Tag Filter UI (→ OBJ-002)
Newest-first note list with text search and tag filter, built with pagination/debouncing so UI stays responsive and doesn't strain the <300ms list/search target at 10k notes.

### US-002-01: As a Team member browsing shared notes, I want to see the note list ordered newest first when opening the notes view, so that quickly find recent work without scanning unordered notes, keeping retrieval fast as volume grows
**Priority:** Must Have

**Acceptance Criteria:**
- Given the team has multiple notes with different creation times, when the member opens the notes list, then notes display with the most recently created or edited note first
- Given the team has no notes yet, when the member opens the notes list, then an empty-state message displays instead of a blank or loading screen

**Test Cases:**
- **TC-002-01**: the member opens the notes list
- **TC-002-02**: [edge] the member opens the notes list

### US-002-02: As a Team member searching for a specific note, I want to type text into a search box to filter the list by note content, so that locate relevant notes instantly even as note volume grows toward 10,000, supporting the sub-300ms retrieval KPI
**Priority:** Must Have

**Acceptance Criteria:**
- Given the member is on the notes list, when they type a search term, then the request fires only after a short debounce pause (not on every keystroke) and the list updates to matching notes
- Given the member types a search term matching no notes, when the search completes, then a 'no results' message displays instead of an empty list with no feedback
- Given the search request to the API fails or times out, when the member has entered a search term, then an error message displays and the previous list state is preserved rather than showing a blank screen

**Test Cases:**
- **TC-002-03**: they type a search term
- **TC-002-04**: [edge] the search completes
- **TC-002-05**: [error] the member has entered a search term

### US-002-03: As a Team member narrowing notes by topic, I want to select a tag from a filter control to show only notes with that tag, so that find related notes by category without manually scanning the full list, keeping the UI responsive at scale
**Priority:** Must Have

**Acceptance Criteria:**
- Given notes exist with various tags, when the member selects a tag from the filter, then the list updates to show only notes carrying that tag, newest first
- Given a tag filter is active, when the member clears the tag filter, then the full newest-first list returns without needing a page reload

**Test Cases:**
- **TC-002-06**: the member selects a tag from the filter
- **TC-002-07**: [edge] the member clears the tag filter

### US-002-04: As a Team member scrolling a large shared note archive, I want to load additional notes via pagination or infinite scroll instead of all notes at once, so that keep the list view fast and responsive even as the team's note volume approaches 10,000, supporting the p95 latency KPI
**Priority:** Must Have

**Acceptance Criteria:**
- Given more notes exist than fit on one page, when the member scrolls to the end of the current page or clicks 'load more', then the next page of notes loads and appends in newest-first order
- Given the member has reached the last page of results, when they scroll further or click 'load more', then no further requests are made and an 'end of list' indicator shows instead of a repeated spinner

**Test Cases:**
- **TC-002-08**: the member scrolls to the end of the current page or clicks 'load more'
- **TC-002-09**: [edge] they scroll further or click 'load more'

### US-002-05: As a Team member combining search and tag filter, I want to apply a text search query together with a selected tag filter at the same time, so that pinpoint a specific note faster when both filters narrow the result set together
**Priority:** Should Have

**Acceptance Criteria:**
- Given the member has entered a search term and selected a tag, when both filters are active, then the list shows only notes matching both the text and the tag, newest first
- Given both filters are active and match no notes, when the combined filter returns zero results, then a 'no results' message displays referencing both active filters

**Test Cases:**
- **TC-002-10**: both filters are active
- **TC-002-11**: [edge] the combined filter returns zero results

### US-002-06: As a Team member waiting on a search or filter request, I want to see a lightweight loading indicator while the list is refreshing, so that get visible feedback that the system is responding, reinforcing trust in the fast retrieval experience
**Priority:** Should Have

**Acceptance Criteria:**
- Given a search or tag filter is applied, when the request is in flight, then a loading indicator shows and disappears once results render
- Given the member changes the search text again before the previous request finishes, when a newer request is issued, then only the result of the latest request is rendered, discarding any stale in-flight response

**Test Cases:**
- **TC-002-12**: the request is in flight
- **TC-002-13**: [edge] a newer request is issued

## EP-003: Archive & Restore UI (Lead Role) (→ OBJ-003)
Lead-only controls to archive a note (removing it from the active list) and restore archived notes, keeping the active list relevant without cluttering member views.

### US-003-01: As a Team lead, I want to archive a note directly from the notes list or detail view, so that remove stale notes from the active list so it stays relevant for the team
**Priority:** Must Have

**Acceptance Criteria:**
- Given a lead is viewing an active note in the list or detail view, when they select Archive and confirm, then the note is removed from the active list immediately
- Given a logged-in team member (non-lead) is viewing the notes list, when they view a note's controls, then no Archive button is shown or it is disabled
- Given a lead confirms archiving a note, when the archive request to the API fails, then the note remains visible in the active list and an error message is shown

**Test Cases:**
- **TC-003-01**: they select Archive and confirm
- **TC-003-02**: [edge] they view a note's controls
- **TC-003-03**: [error] the archive request to the API fails

### US-003-02: As a Team lead, I want to view a separate list of archived notes, so that locate notes that need to be restored without cluttering the active view
**Priority:** Must Have

**Acceptance Criteria:**
- Given a lead is logged in, when they navigate to the archived notes view, then they see all archived notes listed newest first
- Given no notes have been archived yet, when a lead opens the archived notes view, then an empty-state message is shown instead of a blank list
- Given a team member (non-lead) is logged in, when they try to access the archived notes view, then the view/navigation link is hidden or access is denied

**Test Cases:**
- **TC-003-04**: they navigate to the archived notes view
- **TC-003-05**: [edge] a lead opens the archived notes view
- **TC-003-06**: [error] they try to access the archived notes view

### US-003-03: As a Team lead, I want to restore an archived note back to the active list, so that bring a note back into circulation when it becomes relevant again
**Priority:** Must Have

**Acceptance Criteria:**
- Given a lead is viewing the archived notes list, when they select Restore on a note, then the note disappears from the archived list and reappears in the active list
- Given a lead restores a note, when the restore completes, then the note's position in the active list reflects its original creation/update order (newest first)
- Given a lead triggers Restore on an archived note, when the restore request to the API fails, then the note stays in the archived list and an error message is shown

**Test Cases:**
- **TC-003-07**: they select Restore on a note
- **TC-003-08**: [edge] the restore completes
- **TC-003-09**: [error] the restore request to the API fails

### US-003-04: As a Team member, I want to see only active notes in the main list and search results, so that keep the working note list uncluttered by notes a lead has archived
**Priority:** Must Have

**Acceptance Criteria:**
- Given some notes have been archived by a lead, when a team member opens the notes list, then archived notes are not displayed
- Given an archived note matches a search term or tag filter, when a team member searches or filters, then the archived note is excluded from the results

**Test Cases:**
- **TC-003-10**: a team member opens the notes list
- **TC-003-11**: [edge] a team member searches or filters

### US-003-05: As a Team lead, I want to confirm an archive action before it is applied, so that avoid accidentally hiding a note from the team's active list
**Priority:** Should Have

**Acceptance Criteria:**
- Given a lead selects Archive on a note, when a confirmation dialog appears and they confirm, then the note is archived and removed from the active list
- Given a lead selects Archive on a note, when the confirmation dialog appears and they cancel, then the note remains unchanged in the active list

**Test Cases:**
- **TC-003-12**: a confirmation dialog appears and they confirm
- **TC-003-13**: [edge] the confirmation dialog appears and they cancel

## EP-004: Login & Role-Based Access UI (→ OBJ-004)
Email/password login screen, session handling, and role-aware UI (hide/show archive controls) so only authenticated users access notes and only leads see archive/restore.

### US-004-01: As a Team member, I want to log in with their email and password, so that only authenticated users can reach team notes, supporting the 100% authenticated-access KPI
**Priority:** Must Have

**Acceptance Criteria:**
- Given a registered user on the login screen, when they submit a correct email and password, then they are authenticated and redirected to the notes list
- Given a user on the login screen, when they submit an incorrect email or password, then an inline error message is shown and no session is created
- Given a user on the login screen, when they submit the form with an empty email or password field, then submission is blocked and a required-field validation message is shown

**Test Cases:**
- **TC-004-01**: they submit a correct email and password
- **TC-004-02**: [error] they submit an incorrect email or password
- **TC-004-03**: [edge] they submit the form with an empty email or password field

### US-004-02: As a Team member, I want to have their session checked before viewing any notes page, so that unauthenticated access is impossible, preventing bypass of login
**Priority:** Must Have

**Acceptance Criteria:**
- Given a user with a valid active session, when they navigate directly to the notes list URL, then the notes list is displayed without re-login
- Given a user with no session or an expired session, when they navigate directly to any notes page URL, then they are redirected to the login screen
- Given a user who is already logged in, when they navigate to the login page URL, then they are redirected to the notes list instead of seeing the login form

**Test Cases:**
- **TC-004-04**: they navigate directly to the notes list URL
- **TC-004-05**: [error] they navigate directly to any notes page URL
- **TC-004-06**: [edge] they navigate to the login page URL

### US-004-03: As a Team lead, I want to see archive and restore controls only when logged in with the lead role, so that role-based access is enforced in the UI so only leads can trigger archiving, protecting data per access-control KPI
**Priority:** Must Have

**Acceptance Criteria:**
- Given a logged-in team lead viewing a note, when the note list renders, then an archive button is visible on active notes and a restore button is visible on archived notes
- Given a logged-in team member viewing the same notes, when the note list renders, then no archive or restore controls are visible anywhere in the UI
- Given a session where role data is missing or unresolved, when the notes list renders, then archive/restore controls default to hidden until role is confirmed

**Test Cases:**
- **TC-004-07**: the note list renders
- **TC-004-08**: [error] the note list renders
- **TC-004-09**: [edge] the notes list renders

### US-004-04: As a Team member, I want to log out to end their session, so that sessions are terminated on shared devices, reducing unauthorized access risk
**Priority:** Should Have

**Acceptance Criteria:**
- Given a logged-in user clicks logout, when the logout action completes, then their session is cleared and they are redirected to the login screen
- Given a user who has just logged out, when they press the browser back button to the notes page, then they are redirected to the login screen rather than seeing cached notes

**Test Cases:**
- **TC-004-10**: the logout action completes
- **TC-004-11**: [edge] they press the browser back button to the notes page

### US-004-05: As a Team member, I want to receive clear feedback if a role-restricted action they attempt is rejected, so that reinforces that access control cannot be bypassed via stale UI state, supporting the authentication KPI
**Priority:** Should Have

**Acceptance Criteria:**
- Given a user whose UI briefly shows an archive control due to stale state, when the archive action is submitted and the API returns 403, then a not-authorized message is shown and the note's state remains unchanged
- Given a logged-in user whose session has expired mid-action, when they submit any note action, then they are redirected to the login screen instead of seeing a generic error

**Test Cases:**
- **TC-004-12**: [error] the archive action is submitted and the API returns 403
- **TC-004-13**: [edge] they submit any note action

## Business Rules

### BR-001: A note must have a non-empty title and body before it can be saved
**Rationale:** Prevents empty/junk notes from polluting the shared knowledge base | **Enforcement:** Client-side form validation on create/edit, blocking save until satisfied

### BR-002: A note's body is authored and stored as markdown; the preview must render the same markdown the saved note will use
**Rationale:** Ensures what users preview matches what teammates will later see | **Enforcement:** Shared markdown renderer used for both preview and note detail view

### BR-003: Tags are optional on a note but, when present, must be unique within that note
**Rationale:** Duplicate tags on one note add no value and distort tag-based filtering | **Enforcement:** Tag input de-duplicates on add in the create/edit form

### BR-004: Leaving the note create/edit form with unsaved changes requires explicit user confirmation to discard
**Rationale:** Prevents accidental loss of work, keeping note creation low-friction | **Enforcement:** Navigation guard on the note form intercepts route change/close when dirty

### BR-005: Deleting a note is a permanent action and must be confirmed before executing
**Rationale:** Irreversible action warrants a safeguard against accidental clutter removal gone wrong | **Enforcement:** Confirmation dialog shown before delete request is sent

### BR-006: The main note list and search results show only active (non-archived) notes by default
**Rationale:** Keeps the working list uncluttered as archived notes are no longer current | **Enforcement:** List/search queries exclude archived notes unless the archived view is explicitly opened

### BR-007: The note list is ordered newest-first by default
**Rationale:** Surfaces recent work first, matching how teams scan shared notes | **Enforcement:** Default sort order applied on list load, before any search/filter

### BR-008: Search text and selected tag filter apply together (AND) when both are set
**Rationale:** Combining filters must narrow, not replace, each other to let users pinpoint notes faster | **Enforcement:** Filter state combines search query and tag selection into a single list request

### BR-009: Only a user with the Team lead role can archive or restore a note
**Rationale:** Archiving affects what the whole team sees and must be a controlled, role-gated action | **Enforcement:** Archive/restore controls hidden for non-leads in UI; action also rejected if role check fails

### BR-010: Archiving a note requires explicit confirmation before it is applied
**Rationale:** Prevents a lead from accidentally hiding a note from the team's active list | **Enforcement:** Confirmation dialog shown before archive request is sent

### BR-011: Every notes page requires an active, valid session; unauthenticated users are redirected to login
**Rationale:** Ensures 100% authenticated access, no bypass of login via direct URL access | **Enforcement:** Session check/guard runs on every notes route before content renders

### BR-012: If a role-restricted action is attempted by an unauthorized role, the UI rejects it and shows a clear, non-blocking error message rather than silently failing
**Rationale:** Reinforces that access control cannot be bypassed via stale UI state and keeps users informed | **Enforcement:** Client handles 403-type responses with a visible message; role-gated controls also hidden proactively

### BR-013: Logging out immediately terminates the current session's access to all notes pages
**Rationale:** Reduces unauthorized access risk on shared devices | **Enforcement:** Logout clears session/token client-side and route guard re-checks auth on next navigation

## Non-Functional Requirements

### NFR-001: Note list and search/filter results render within target latency even as note volume grows
**Category:** Performance | **Target:** p95 response-to-render under 300ms at up to 10,000 notes | **Priority:** Must Have

### NFR-002: List view uses pagination or infinite scroll instead of loading the full note set at once
**Category:** Performance | **Target:** Initial list render fetches no more than one page (e.g., 50 notes) per request | **Priority:** Must Have

### NFR-003: A loading indicator appears during any search/filter/pagination request taking longer than a brief threshold
**Category:** Usability | **Target:** Indicator shown for requests exceeding 150ms | **Priority:** Should Have

### NFR-004: All notes pages are inaccessible without a valid authenticated session
**Category:** Security | **Target:** 100% of notes routes enforce session check before render | **Priority:** Must Have

### NFR-005: Archive/restore UI controls are rendered only for sessions with the Team lead role
**Category:** Security | **Target:** 0% of non-lead sessions see archive/restore controls in UI audit | **Priority:** Must Have

### NFR-006: Logout invalidates client-held session state so subsequent navigation requires re-authentication
**Category:** Security | **Target:** 100% of post-logout navigation attempts redirect to login | **Priority:** Must Have

### NFR-007: Unsaved-changes discard confirmation prevents accidental data loss when leaving the note form
**Category:** Usability | **Target:** Confirmation prompt triggered in 100% of dirty-form exit attempts | **Priority:** Should Have

### NFR-008: Tag filter control and list rendering remain responsive as tagging adoption and note volume increase
**Category:** Scalability | **Target:** UI interaction (tag select, filter apply) responds within 300ms at up to 10,000 notes | **Priority:** Must Have

### NFR-009: Note creation, editing, list, search, filter, and archive controls are operable via keyboard and screen reader
**Category:** Accessibility | **Target:** WCAG 2.1 AA conformance on all notes UI views | **Priority:** Should Have

### NFR-010: No third-party analytics scripts are loaded in the web front end
**Category:** Compliance | **Target:** 0 third-party analytics requests observed in network trace | **Priority:** Must Have

### NFR-011: Role-restricted action rejection produces a clear UI error state rather than a silent failure or crash
**Category:** Availability | **Target:** 100% of rejected role-restricted actions surface a visible error message | **Priority:** Should Have
