---
id: agent-developer/web_app/web-app/INTAKE.md
type: intake
project: b0326ad3-78d5-4254-8e5e-32f701c09bc2
version: 0.1.1
author: c420e621-a820-5b3a-b15f-ca557d09cc88
produces: agent-developer/web_app/web-app/INTAKE.md
depends_on: []
commit_kind: append
confidence: agent
run_id: 7213f693-d9f9-5517-98a8-e5c8999a9b44
okf_id: web-app/INTAKE.md
---

<!-- SECTION: web-app.intake | status: VERIFIED -->
## What this run read

| Input | Role(s) | Parsed | Taken from it | How the role was decided |
|---|---|---|---|---|
| `backend-api.md` | api_docs | yes | 19,483 bytes, 15 identifier(s) | declared in INPUTS.yaml |
| `feedback.md` | feedback | yes | 109 bytes, 0 identifier(s) | declared in INPUTS.yaml |
| `requirements.md` | requirements | yes | 12,684 bytes, 11 identifier(s) | declared in INPUTS.yaml |
| `stories.md` | user_stories | yes | 15,317 bytes, 4 identifier(s) | declared in INPUTS.yaml |

## Inputs expected

| Role | Status |
|---|---|
| `requirements` | supplied (1 file(s)) |
| `user_stories` | supplied (1 file(s)) |
| `design_brief` | not supplied — the run degrades, see GAPS.md |
| `platform` | not supplied — the run degrades, see GAPS.md |
| `api_docs` | supplied (1 file(s)) |
| `feedback` | supplied (1 file(s)) |

## Identifiers

- Distinct identifiers found: **4**
- Of those, user stories: **4**
- With established acceptance detail: **4** (100%, floor 60%)
  - counted when a story carries inline criteria, **or** a `§` reference that resolves to a real requirements section
- Requirements sections available to resolve against: **0**

Roles were declared in `INPUTS.yaml`.
<!-- /SECTION: web-app.intake -->
