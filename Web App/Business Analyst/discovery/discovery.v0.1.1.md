---
id: agent-ba/web_app/discovery/discovery.md
type: discovery
project: b0326ad3-78d5-4254-8e5e-32f701c09bc2
version: 0.1.1
author: c420e621-a820-5b3a-b15f-ca557d09cc88
produces: agent-ba/web_app/discovery/discovery.md
depends_on: []
commit_kind: append
confidence: agent
run_id: 7213f693-d9f9-5517-98a8-e5c8999a9b44
okf_id: DISCOVERY
---

---
id: DISCOVERY
type: discovery
status: active
created_at: 2026-10-11T09:15:59.400224+00:00
rig: ba
---

# Discovery

**Brief quality:** thin (problem: weak, outcomes: weak, stakeholders: weak)

**Declaration:** Thin. CR states the mechanic (pin flag, sort-first) but gives no success metric, no permission rule (who may pin: owner only vs. any member vs. lead-only, unlike archive which is explicitly lead-only in S-03 #3), and no interaction rule with archive (does archiving clear the pin? can an archived note be pinned?) or with the 300ms/10k-note SLA (S-04) once sort logic changes. Assumptions used below: (1) pin is a per-note boolean settable by any team member per CR wording 'a team member can pin a note', not restricted like archive; (2) no cap on number of pinned notes; (3) archived notes are excluded from the pinned-first list (archive hiding, S-03 #3, takes precedence); (4) the existing <300ms SLA (S-04) applies unchanged to the pinned-first sorted list.

## Problem
CR: add a 'pinned' flag to notes so a team member can pin a note; pinned notes list first (API + web UI + tests).

**Root cause:** List is newest-first only (S-03 #2); as note volume grows toward the 10k-note ceiling (S-04), frequently-needed reference notes get buried and users must re-search or re-filter by tag every time, even though search/tag (S-03 #2) and archive (S-03 #3) already exist as the only prioritization tools.

**Boundary:** Most affected: team members maintaining a small set of high-reuse notes (onboarding doc, checklist) who currently re-search for them repeatedly. Least affected: team leads, whose distinct capability (archive, S-02) is orthogonal to pin and not mentioned in the CR.

**Prior attempts:** Search by text/tag and archive (S-03) are the existing-implied mechanisms for managing visibility/priority; CR implies these are insufficient for quick resurfacing of known-important notes, hence a new pin mechanism.

## Outcome
**Before:** Notes list is strictly newest-first; finding a known important note among up to 10,000 requires search or tag filter every time.

**After:** Any pinned note(s) appear at the top of the list regardless of recency, giving one-click/no-search access to priority notes.

**Measurement:** At 60 days: % of teams with ≥1 active pinned note, and change in search/filter actions per session for repeat access to known notes.

**Impact index:** Time-to-locate a known important note drops; notes list becomes a reliable 'home base' rather than a scroll/search chore.

## Stakeholders
**Decision maker:** Team lead/product owner - success = team stops re-searching for the same reference notes; failure = pin unused or list cluttered with too many pins, defeating the purpose.

**Champion:** The team member who owns the team's key reference note (e.g. onboarding checklist, sprint rules) - most invested since pinning keeps their note permanently visible.

**End user:** Team member on a Tuesday afternoon, context-switching between tasks and Slack, opens the app expecting the sprint-checklist note at the top instead of typing a search query; alternative today is remembering the tag or scrolling.
