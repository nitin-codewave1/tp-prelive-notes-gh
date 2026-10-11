---
id: agent-developer/web_app/web-app/SELF-CHECK.md
type: self-check
project: b0326ad3-78d5-4254-8e5e-32f701c09bc2
version: 0.1.1
author: c420e621-a820-5b3a-b15f-ca557d09cc88
produces: agent-developer/web_app/web-app/SELF-CHECK.md
depends_on: []
commit_kind: append
confidence: agent
run_id: 7213f693-d9f9-5517-98a8-e5c8999a9b44
okf_id: web-app/SELF-CHECK.md
---

<!-- SECTION: web-app.self-check | status: VERIFIED -->
## Verification pass

| # | Check | Result | Detail |
|---|---|---|---|
| 1 | anchors open and close; ids unique and well-formed | **PASS** | 12 section(s); all 12 sections present, in order |
| 2 | every referenced id resolves | **NOT RUN** | 3 foreign id(s) cited — resolvable only against an upstream this run did not receive: ASM-BE-004, GAP-BE-005, ASM-BE-005 |
| 3 | every minted id carries this surface's code | **PASS** | 16 minted, all carrying `WEB` |
| 4 | no VERIFIED section contains an uncited claim | **NOT RUN** | a judgement about prose; the document's own verification pass covers it |
| 5 | locked sections reproduced byte-for-byte | **PASS** | the document is written through the locking engine |
| 6 | every story this document answers for appears in the coverage table | **PASS** | 4 in scope, 0 not found in the document |
| 7 | no schema is restated | **NOT RUN** | a judgement about prose, not a pattern this module can assert |
| 8 | no file written outside output_root | **PASS** | every write is jailed to `/tmp/qbit/specialist/7213f693-d9f9-5517-98a8-e5c8999a9b44/003_b0326ad3-78d5-4254-8e5e-32f701c09bc2-web_app-agent-developer_agent-developer/devagent/output/web-app` |
| 9 | every Q- has a class; every GAP- names its closing artefact | **NOT RUN** | 14 GAP- id(s) are defined in the specification's prose; whether each names a closing artefact is the document's own verification pass |
| 10 | no GAP- raised for a condition that cannot occur | **NOT RUN** | a judgement about the condition, not the id |

**5 passed, 0 failed, 5 not run.**

A check marked `NOT RUN` has nothing to check yet at this milestone — no specification sections exist. It is reported as not run rather than as a pass: reporting a pass that was never performed is invariant I-2 broken against ourselves.
<!-- /SECTION: web-app.self-check -->
