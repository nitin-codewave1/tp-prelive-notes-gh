---
id: agent-design/web_app/okf/screens/notelistscreen.md
type: screen
project: b0326ad3-78d5-4254-8e5e-32f701c09bc2
version: 0.1.0
author: c420e621-a820-5b3a-b15f-ca557d09cc88
produces: agent-design/web_app/okf/screens/notelistscreen.md
depends_on: [US-001-01, US-001-02, US-001-04, US-001-06, US-002-01, US-002-02, US-002-03, US-002-04, US-002-06, US-003-01, US-003-04, US-003-05, US-003-06, US-004-01, US-004-04, US-004-05]
commit_kind: append
confidence: agent
run_id: 7213f693-d9f9-5517-98a8-e5c8999a9b44
okf_id: NoteListScreen
---

---
run: 2
spec_version: v1_initial
inputs_hash: "sha256:6ba6372df970abffafc6e728c9686afd23836baaad58796fadf4ff68e4a80a94"
generator: specgen.okf_lite
id: NoteListScreen
type: screen
title: Note List
module: M2
parent_ids: [US-001-01, US-001-02, US-001-04, US-001-06, US-002-01, US-002-02, US-002-03, US-002-04, US-002-06, US-003-01, US-003-04, US-003-05, US-003-06, US-004-01, US-004-04, US-004-05]
---

# NoteListScreen: Note List

Module: `M2`  ·  Variants: Default, Loading, Empty, Error

| components | outgoing |
|---|---|
| Empty/State, Input/Segmented, Loading/Skeleton | ON_CLICK→ArchivedNotesListScreen, ON_CLICK→NoteDetailScreen |

## Stories

- [[US-001-01]]
- [[US-001-02]]
- [[US-001-04]]
- [[US-001-06]]
- [[US-002-01]]
- [[US-002-02]]
- [[US-002-03]]
- [[US-002-04]]
- [[US-002-06]]
- [[US-003-01]]
- [[US-003-04]]
- [[US-003-05]]
- [[US-003-06]]
- [[US-004-01]]
- [[US-004-04]]
- [[US-004-05]]
