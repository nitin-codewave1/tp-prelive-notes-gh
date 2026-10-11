# Team Notes — product brief

Build a small web app where a team keeps shared notes.

## Users
- Team member: creates, edits and tags notes; searches notes by text or tag.
- Team lead: everything a member can do, plus archiving notes.

## Must have
1. Create, edit and delete a note (title, markdown body, tags).
2. List notes, newest first, with search by text and filter by tag.
3. Archive a note (lead only); archived notes are hidden from the list but can be restored.
4. Simple login with email and password.

## Constraints
- Web front end plus a REST API and a relational database.
- EU data residency; no third-party analytics.
- Response time under 300 ms for list and search with 10,000 notes.

## Out of scope
- Real-time collaboration, attachments, mobile apps.
