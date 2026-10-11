---
id: agent-developer/web_app/web-app/INTAKE.md
type: intake
project: b0326ad3-78d5-4254-8e5e-32f701c09bc2
version: 0.1.0
author: c420e621-a820-5b3a-b15f-ca557d09cc88
produces: agent-developer/web_app/web-app/INTAKE.md
depends_on: []
commit_kind: append
confidence: agent
run_id: de21d0c7-ffc3-593d-b762-95f1046f5945
okf_id: web-app/INTAKE.md
---

<!-- SECTION: web-app.intake | status: VERIFIED -->
## What this run read

| Input | Role(s) | Parsed | Taken from it | How the role was decided |
|---|---|---|---|---|
| `backend-api.md` | api_docs | yes | 22,115 bytes, 30 identifier(s) | declared in INPUTS.yaml |
| `design-brief.md` | design_brief | yes | 7,755 bytes, 4 identifier(s) | declared in INPUTS.yaml |
| `feedback.md` | feedback | yes | 75 bytes, 0 identifier(s) | declared in INPUTS.yaml |
| `requirements.md` | requirements | yes | 14,124 bytes, 11 identifier(s) | declared in INPUTS.yaml |
| `stories.md` | user_stories | yes | 13,808 bytes, 4 identifier(s) | declared in INPUTS.yaml |

## Inputs expected

| Role | Status |
|---|---|
| `requirements` | supplied (1 file(s)) |
| `user_stories` | supplied (1 file(s)) |
| `design_brief` | supplied (1 file(s)) |
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
