---
id: agent-ba/notes_api/discovery/discovery.md
type: discovery
project: b0326ad3-78d5-4254-8e5e-32f701c09bc2
version: 0.1.1
author: c420e621-a820-5b3a-b15f-ca557d09cc88
produces: agent-ba/notes_api/discovery/discovery.md
depends_on: []
commit_kind: append
confidence: agent
run_id: 5dd29246-c79a-57bc-97dc-ab0fbdf73733
okf_id: DISCOVERY
---

---
id: DISCOVERY
type: discovery
status: active
created_at: 2026-10-11T08:57:59.656950+00:00
rig: ba
---

# Discovery

**Brief quality:** thin (problem: weak, outcomes: weak, stakeholders: weak)

**Declaration:** Brief is thin for this CR: no stated business pain point, no success metric, no role boundary for who may pin. Assumptions: pin is a per-note boolean (not per-user); pin action is available to 'team member' role as stated in CR wording, not lead-only; no cap on number of pinned notes; sort order becomes pinned-first, then newest-first as secondary key; this lane (notes_api) owns API spec, DB migration, endpoint logic, and tests only — UI toggle/display is out of scope here per lane split.

## Problem
Add a 'pinned' flag so a team member can pin a note and pinned notes list first; update API spec, implementation, tests.

**Root cause:** Newest-first listing (S-03.2) buries high-value reference notes as volume grows toward the 10k-note scale constraint (S-04); tag/text search is a workaround, not a fix, for repeat access to the same notes.

**Boundary:** Most affected: team members who revisit a small set of reference notes often (onboarding docs, standing agendas). Least affected: occasional users or teams with few notes where newest-first is already sufficient.

**Prior attempts:** Implied workaround is using tags or search (S-03.2) to relocate important notes each time, rather than a persistent priority marker.

## Outcome
**Before:** Notes list is strictly newest-first; important notes get buried and must be re-found via search/tag filter every time.

**After:** Any team member can toggle a pinned flag on a note via the API; list/search responses return pinned notes first, ahead of recency ordering.

**Measurement:** At 60 days: % of active users with ≥1 pinned note, and list/search endpoint p95 latency stays under 300ms at 10,000 notes (no regression vs pre-CR baseline).

**Impact index:** Fewer repeated search/filter actions needed to reach the same frequently-used notes.

## Stakeholders
**Decision maker:** Client sponsoring the CR; success = pin feature ships via API/spec/tests with no latency or contract regression, failure = broken list endpoint or breaking change for web_app lane.

**Champion:** Team lead or power user who maintains a few always-relevant reference notes and wants them permanently visible without re-searching.

**End user:** Team member on a Tuesday afternoon between meetings, opening the app briefly, scanning the list for one or two notes they check daily; alternative today is retyping a tag/search term or keeping a separate bookmark.
