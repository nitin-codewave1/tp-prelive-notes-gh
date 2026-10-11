---
id: agent-qa/web_app/rollup.md
type: spec
project: b0326ad3-78d5-4254-8e5e-32f701c09bc2
version: 0.1.0
author: c420e621-a820-5b3a-b15f-ca557d09cc88
produces: agent-qa/web_app/rollup.md
depends_on: []
commit_kind: append
confidence: agent
run_id: de21d0c7-ffc3-593d-b762-95f1046f5945
---

# agent-qa

```json
[
  {
    "testId": "TC-001-01",
    "title": "they enter a title, markdown body, at least one tag, and click Save",
    "module": "create a new note with a title, markdown body and tags",
    "coveredBy": "TC-001-01",
    "steps": [
      { "stepNo": 1, "instruction": "Log in as a team member", "action": "navigate", "selector": "/login", "value": "" },
      { "stepNo": 2, "instruction": "Enter email", "action": "fill", "selector": "[data-testid=\"login-email-input\"]", "value": "member@testmail.com" },
      { "stepNo": 3, "instruction": "Enter password", "action": "fill", "selector": "[data-testid=\"login-password-input\"]", "value": "Password123!" },
      { "stepNo": 4, "instruction": "Submit login", "action": "click", "selector": "[data-testid=\"login-submit-button\"]", "value": "" },
      { "stepNo": 5, "instruction": "Go to new note screen", "action": "navigate", "selector": "/notes/new", "value": "" },
      { "stepNo": 6, "instruction": "Enter title", "action": "fill", "selector": "[data-testid=\"note-form-title-input\"]", "value": "Sprint Planning Notes" },
      { "stepNo": 7, "instruction": "Enter markdown body", "action": "fill", "selector": "[data-testid=\"note-form-body-textarea\"]", "value": "# Agenda\n- Item one" },
      { "stepNo": 8, "instruction": "Add a tag and press Enter", "action": "fill", "selector": "[data-testid=\"note-form-tag-input\"]", "value": "planning\n" },
      { "stepNo": 9, "instruction": "Click Save", "action": "click", "selector": "[data-testid=\"note-form-save-button\"]", "value": "" },
      { "stepNo": 10, "instruction": "Verify redirect to notes list", "action": "assert_url", "selector": "", "value": "/notes" },
      { "stepNo": 11, "instruction": "Verify new note shown first", "action": "assert_text", "selector": "[data-testid=\"notes-list-item\"]:first-child", "value": "Sprint Planning Notes" }
    ],
    "expectedResult": "the note is created and the team member is returned to the notes list with the new note shown first"
  },
  {
    "testId": "TC-001-02",
    "title": "[error] they click Save with the title field empty",
    "module": "create a new note with a title, markdown body and tags",
    "coveredBy": "TC-001-02",
    "steps": [
      { "stepNo": 1, "instruction": "Log in and go to new note screen", "action": "navigate", "selector": "/notes/new", "value": "" },
      { "stepNo": 2, "instruction": "Leave title empty, fill body", "action": "fill", "selector": "[data-testid=\"note-form-body-textarea\"]", "value": "Body text" },
      { "stepNo": 3, "instruction": "Click Save", "action": "click", "selector": "[data-testid=\"note-form-save-button\"]", "value": "" },
      { "stepNo": 4, "instruction": "Verify inline validation error shown", "action": "assert_visible", "selector": "[data-testid=\"note-form-title-error\"]", "value": "" },
      { "stepNo": 5, "instruction": "Verify still on new note screen", "action": "assert_url", "selector": "", "value": "/notes/new" }
    ],
    "expectedResult": "the form shows an inline validation error and the note is not saved"
  },
  {
    "testId": "TC-001-03",
    "title": "[edge] they click Save without entering any tags",
    "module": "create a new note with a title, markdown body and tags",
    "coveredBy": "TC-001-03",
    "steps": [
      { "stepNo": 1, "instruction": "Go to new note screen", "action": "navigate", "selector": "/notes/new", "value": "" },
      { "stepNo": 2, "instruction": "Fill title", "action": "fill", "selector": "[data-testid=\"note-form-title-input\"]", "value": "No Tags Note" },
      { "stepNo": 3, "instruction": "Fill body", "action": "fill", "selector": "[data-testid=\"note-form-body-textarea\"]", "value": "Body text" },
      { "stepNo": 4, "instruction": "Click Save without tags", "action": "click", "selector": "[data-testid=\"note-form-save-button\"]", "value": "" },
      { "stepNo": 5, "instruction": "Verify note saved and shown first with no tag chips", "action": "assert_visible", "selector": "[data-testid=\"notes-list-item\"]:first-child", "value": "" }
    ],
    "expectedResult": "the note is saved successfully with an empty tag list"
  },
  {
    "testId": "TC-001-04",
    "title": "they change the title, body text, or tags and click Save",
    "module": "edit the title, markdown body or tags of an existing note",
    "coveredBy": "TC-001-04",
    "steps": [
      { "stepNo": 1, "instruction": "Open an existing note's detail page", "action": "navigate", "selector": "/notes/1", "value": "" },
      { "stepNo": 2, "instruction": "Click Edit", "action": "click", "selector": "[data-testid=\"note-detail-edit-button\"]", "value": "" },
      { "stepNo": 3, "instruction": "Change the title", "action": "fill", "selector": "[data-testid=\"note-form-title-input\"]", "value": "Updated Title" },
      { "stepNo": 4, "instruction": "Click Save", "action": "click", "selector": "[data-testid=\"note-form-save-button\"]", "value": "" },
      { "stepNo": 5, "instruction": "Reopen the note", "action": "navigate", "selector": "/notes/1", "value": "" },
      { "stepNo": 6, "instruction": "Verify updated title visible", "action": "assert_text", "selector": "[data-testid=\"note-detail-title\"]", "value": "Updated Title" }
    ],
    "expectedResult": "the note is updated and the changes are visible when the note is reopened"
  },
  {
    "testId": "TC-001-05",
    "title": "[edge] they remove all existing tags and save",
    "module": "edit the title, markdown body or tags of an existing note",
    "coveredBy": "TC-001-05",
    "steps": [
      { "stepNo": 1, "instruction": "Open note edit screen", "action": "navigate", "selector": "/notes/1/edit", "value": "" },
      { "stepNo": 2, "instruction": "Remove existing tag chip", "action": "click", "selector": "[data-testid=\"note-form-tag-chip\"] >> [data-testid=\"note-form-tag-remove-icon\"]", "value": "" },
      { "stepNo": 3, "instruction": "Click Save", "action": "click", "selector": "[data-testid=\"note-form-save-button\"]", "value": "" },
      { "stepNo": 4, "instruction": "Verify note saved with no tags and no error", "action": "assert_visible", "selector": "[data-testid=\"note-detail-title\"]", "value": "" }
    ],
    "expectedResult": "the note is saved with no tags without error"
  },
  {
    "testId": "TC-001-06",
    "title": "[error] the second member saves after the note was already changed by the first",
    "module": "edit the title, markdown body or tags of an existing note",
    "coveredBy": "TC-001-06",
    "steps": [
      { "stepNo": 1, "instruction": "Open note edit screen as member A (simulated prior edit already applied server-side)", "action": "navigate", "selector": "/notes/1/edit", "value": "" },
      { "stepNo": 2, "instruction": "Change body text", "action": "fill", "selector": "[data-testid=\"note-form-body-textarea\"]", "value": "Second editor's change" },
      { "stepNo": 3, "instruction": "Click Save on stale note", "action": "click", "selector": "[data-testid=\"note-form-save-button\"]", "value": "" },
      { "stepNo": 4, "instruction": "Verify conflict/stale-data message shown", "action": "assert_visible", "selector": "[data-testid=\"note-detail-conflict-message\"]", "value": "" }
    ],
    "expectedResult": "the UI shows a conflict/stale-data message instead of silently overwriting"
  },
  {
    "testId": "TC-001-07",
    "title": "they click Delete and confirm the action",
    "module": "delete a note they no longer need",
    "coveredBy": "TC-001-07",
    "steps": [
      { "stepNo": 1, "instruction": "Open an existing note", "action": "navigate", "selector": "/notes/1", "value": "" },
      { "stepNo": 2, "instruction": "Click Delete", "action": "click", "selector": "[data-testid=\"note-detail-delete-button\"]", "value": "" },
      { "stepNo": 3, "instruction": "Confirm deletion", "action": "click", "selector": "[data-testid=\"delete-confirm-button\"]", "value": "" },
      { "stepNo": 4, "instruction": "Go to notes list", "action": "navigate", "selector": "/notes", "value": "" },
      { "stepNo": 5, "instruction": "Verify note no longer appears", "action": "assert_visible", "selector": "[data-testid=\"notes-list-item\"]", "value": "" }
    ],
    "expectedResult": "the note is removed and no longer appears in the notes list"
  },
  {
    "testId": "TC-001-08",
    "title": "[edge] the confirmation dialog appears",
    "module": "delete a note they no longer need",
    "coveredBy": "TC-001-08",
    "steps": [
      { "stepNo": 1, "instruction": "Open an existing note", "action": "navigate", "selector": "/notes/1", "value": "" },
      { "stepNo": 2, "instruction": "Click Delete", "action": "click", "selector": "[data-testid=\"note-detail-delete-button\"]", "value": "" },
      { "stepNo": 3, "instruction": "Click Cancel in dialog", "action": "click", "selector": "[data-testid=\"delete-cancel-button\"]", "value": "" },
      { "stepNo": 4, "instruction": "Verify note still present in detail/list", "action": "assert_visible", "selector": "[data-testid=\"note-detail-title\"]", "value": "" }
    ],
    "expectedResult": "clicking Cancel leaves the note unchanged and still in the list"
  },
  {
    "testId": "TC-001-09",
    "title": "[error] the team member attempts to delete it again",
    "module": "delete a note they no longer need",
    "coveredBy": "TC-001-09",
    "steps": [
      { "stepNo": 1, "instruction": "Open a note already deleted by another user", "action": "navigate", "selector": "/notes/1", "value": "" },
      { "stepNo": 2, "instruction": "Click Delete", "action": "click", "selector": "[data-testid=\"note-detail-delete-button\"]", "value": "" },
      { "stepNo": 3, "instruction": "Confirm deletion", "action": "click", "selector": "[data-testid=\"delete-confirm-button\"]", "value": "" },
      { "stepNo": 4, "instruction": "Verify 'note no longer exists' message shown", "action": "assert_text", "selector": "[data-testid=\"note-detail-error-message\"]", "value": "no longer exists" }
    ],
    "expectedResult": "the UI shows a 'note no longer exists' message instead of a generic failure"
  },
  {
    "testId": "TC-001-10",
    "title": "they type a tag and press Enter/comma",
    "module": "add or remove tags while creating or editing a note",
    "coveredBy": "TC-001-10",
    "steps": [
      { "stepNo": 1, "instruction": "Go to new note screen", "action": "navigate", "selector": "/notes/new", "value": "" },
      { "stepNo": 2, "instruction": "Type a tag and press Enter", "action": "fill", "selector": "[data-testid=\"note-form-tag-input\"]", "value": "urgent\n" },
      { "stepNo": 3, "instruction": "Verify tag chip is visible", "action": "assert_visible", "selector": "[data-testid=\"note-form-tag-chip\"]", "value": "" },
      { "stepNo": 4, "instruction": "Fill required fields and Save", "action": "fill", "selector": "[data-testid=\"note-form-title-input\"]", "value": "Tag Test" },
      { "stepNo": 5, "instruction": "Click Save", "action": "click", "selector": "[data-testid=\"note-form-save-button\"]", "value": "" },
      { "stepNo": 6, "instruction": "Verify tag included on saved note", "action": "assert_text", "selector": "[data-testid=\"notes-list-item\"]:first-child", "value": "urgent" }
    ],
    "expectedResult": "the tag is added as a visible chip and included on Save"
  },
  {
    "testId": "TC-001-11",
    "title": "they click the chip's remove icon",
    "module": "add or remove tags while creating or editing a note",
    "coveredBy": "TC-001-11",
    "steps": [
      { "stepNo": 1, "instruction": "Go to note form with an existing tag chip", "action": "navigate", "selector": "/notes/1/edit", "value": "" },
      { "stepNo": 2, "instruction": "Click chip remove icon", "action": "click", "selector": "[data-testid=\"note-form-tag-chip\"] >> [data-testid=\"note-form-tag-remove-icon\"]", "value": "" },
      { "stepNo": 3, "instruction": "Click Save", "action": "click", "selector": "[data-testid=\"note-form-save-button\"]", "value": "" },
      { "stepNo": 4, "instruction": "Verify tag no longer present on note", "action": "assert_visible", "selector": "[data-testid=\"note-detail-title\"]", "value": "" }
    ],
    "expectedResult": "the tag is removed from the note before saving"
  },
  {
    "testId": "TC-001-12",
    "title": "[edge] they try to add a duplicate of an existing tag on the note",
    "module": "add or remove tags while creating or editing a note",
    "coveredBy": "TC-001-12",
    "steps": [
      { "stepNo": 1, "instruction": "Go to note form with an existing tag 'urgent'", "action": "navigate", "selector": "/notes/1/edit", "value": "" },
      { "stepNo": 2, "instruction": "Type the duplicate tag and press Enter", "action": "fill", "selector": "[data-testid=\"note-form-tag-input\"]", "value": "urgent\n" },
      { "stepNo": 3, "instruction": "Verify only one instance of the tag chip exists", "action": "assert_visible", "selector": "[data-testid=\"note-form-tag-chip\"]", "value": "" }
    ],
    "expectedResult": "the duplicate is ignored and only one instance of the tag remains"
  },
  {
    "testId": "TC-001-13",
    "title": "they switch to the Preview tab",
    "module": "preview the rendered markdown while writing a note's body",
    "coveredBy": "TC-001-13",
    "steps": [
      { "stepNo": 1, "instruction": "Go to new note screen", "action": "navigate", "selector": "/notes/new", "value": "" },
      { "stepNo": 2, "instruction": "Type markdown into the body field", "action": "fill", "selector": "[data-testid=\"note-form-body-textarea\"]", "value": "# Heading\n**bold text**" },
      { "stepNo": 3, "instruction": "Switch to Preview tab", "action": "click", "selector": "[data-testid=\"note-form-preview-tab\"]", "value": "" },
      { "stepNo": 4, "instruction": "Verify rendered HTML matches markdown", "action": "assert_text", "selector": "[data-testid=\"note-form-preview-content\"]", "value": "Heading" }
    ],
    "expectedResult": "the rendered HTML output matches the markdown entered"
  },
  {
    "testId": "TC-001-14",
    "title": "[edge] they switch to Preview",
    "module": "preview the rendered markdown while writing a note's body",
    "coveredBy": "TC-001-14",
    "steps": [
      { "stepNo": 1, "instruction": "Go to new note screen", "action": "navigate", "selector": "/notes/new", "value": "" },
      { "stepNo": 2, "instruction": "Type malformed markdown into the body field", "action": "fill", "selector": "[data-testid=\"note-form-body-textarea\"]", "value": "[unclosed](link **bold" },
      { "stepNo": 3, "instruction": "Switch to Preview tab", "action": "click", "selector": "[data-testid=\"note-form-preview-tab\"]", "value": "" },
      { "stepNo": 4, "instruction": "Verify preview renders without crashing the form", "action": "assert_visible", "selector": "[data-testid=\"note-form-preview-content\"]", "value": "" }
    ],
    "expectedResult": "the preview renders the text as plain text/best-effort without crashing the form"
  },
  {
    "testId": "TC-001-15",
    "title": "they navigate away without saving",
    "module": "discard unsaved changes when leaving the note form",
    "coveredBy": "TC-001-15",
    "steps": [
      { "stepNo": 1, "instruction": "Go to new note screen and make unsaved edits", "action": "navigate", "selector": "/notes/new", "value": "" },
      { "stepNo": 2, "instruction": "Type into title field without saving", "action": "fill", "selector": "[data-testid=\"note-form-title-input\"]", "value": "Unsaved Draft" },
      { "stepNo": 3, "instruction": "Attempt to navigate away", "action": "click", "selector": "[data-testid=\"app-nav-notes-link\"]", "value": "" },
      { "stepNo": 4, "instruction": "Verify discard confirmation prompt appears", "action": "assert_visible", "selector": "[data-testid=\"note-form-discard-dialog\"]", "value": "" }
    ],
    "expectedResult": "a confirmation prompt asks whether to discard changes"
  },
  {
    "testId": "TC-001-16",
    "title": "[edge] the team member confirms discard",
    "module": "discard unsaved changes when leaving the note form",
    "coveredBy": "TC-001-16",
    "steps": [
      { "stepNo": 1, "instruction": "Trigger the discard confirmation prompt", "action": "navigate", "selector": "/notes/new", "value": "" },
      { "stepNo": 2, "instruction": "Type into title field without saving", "action": "fill", "selector": "[data-testid=\"note-form-title-input\"]", "value": "Unsaved Draft" },
      { "stepNo": 3, "instruction": "Attempt to navigate away", "action": "click", "selector": "[data-testid=\"app-nav-notes-link\"]", "value": "" },
      { "stepNo": 4, "instruction": "Confirm discard", "action": "click", "selector": "[data-testid=\"note-form-discard-confirm-button\"]", "value": "" },
      { "stepNo": 5, "instruction": "Verify form closed and no changes persisted", "action": "assert_url", "selector": "", "value": "/notes" }
    ],
    "expectedResult": "the form closes and no changes are persisted to the note"
  },
  {
    "testId": "TC-002-01",
    "title": "the member opens the notes list",
    "module": "see the note list ordered newest first when opening the notes view",
    "coveredBy": "TC-002-01",
    "steps": [
      { "stepNo": 1, "instruction": "Navigate to notes list with multiple notes present", "action": "navigate", "selector": "/notes", "value": "" },
      { "stepNo": 2, "instruction": "Verify first item is the most recently created/edited note", "action": "assert_visible", "selector": "[data-testid=\"notes-list-item\"]:first-child", "value": "" }
    ],
    "expectedResult": "notes display with the most recently created or edited note first"
  },
  {
    "testId": "TC-002-02",
    "title": "[edge] the member opens the notes list",
    "module": "see the note list ordered newest first when opening the notes view",
    "coveredBy": "TC-002-02",
    "steps": [
      { "stepNo": 1, "instruction": "Navigate to notes list with no notes", "action": "navigate", "selector": "/notes", "value": "" },
      { "stepNo": 2, "instruction": "Verify empty-state message displays", "action": "assert_visible", "selector": "[data-testid=\"notes-list-empty-state\"]", "value": "" }
    ],
    "expectedResult": "an empty-state message displays instead of a blank or loading screen"
  },
  {
    "testId": "TC-002-03",
    "title": "they type a search term",
    "module": "type text into a search box to filter the list by note content",
    "coveredBy": "TC-002-03",
    "steps": [
      { "stepNo": 1, "instruction": "Go to notes list", "action": "navigate", "selector": "/notes", "value": "" },
      { "stepNo": 2, "instruction": "Type a search term", "action": "fill", "selector": "[data-testid=\"notes-list-search-input\"]", "value": "planning" },
      { "stepNo": 3, "instruction": "Wait for debounce", "action": "wait", "selector": "", "value": "500" },
      { "stepNo": 4, "instruction": "Verify list updates to matching notes", "action": "assert_text", "selector": "[data-testid=\"notes-list-item\"]:first-child", "value": "planning" }
    ],
    "expectedResult": "the request fires only after a short debounce pause (not on every keystroke) and the list updates to matching notes"
  },
  {
    "testId": "TC-002-04",
    "title": "[edge] the search completes",
    "module": "type text into a search box to filter the list by note content",
    "coveredBy": "TC-002-04",
    "steps": [
      { "stepNo": 1, "instruction": "Go to notes list", "action": "navigate", "selector": "/notes", "value": "" },
      { "stepNo": 2, "instruction": "Type a search term matching no notes", "action": "fill", "selector": "[data-testid=\"notes-list-search-input\"]", "value": "zzznomatch" },
      { "stepNo": 3, "instruction": "Wait for debounce and search completion", "action": "wait", "selector": "", "value": "500" },
      { "stepNo": 4, "instruction": "Verify 'no results' message displays", "action": "assert_visible", "selector": "[data-testid=\"notes-list-no-results\"]", "value": "" }
    ],
    "expectedResult": "a 'no results' message displays instead of an empty list with no feedback"
  },
  {
    "testId": "TC-002-05",
    "title": "[error] the member has entered a search term",
    "module": "type text into a search box to filter the list by note content",
    "coveredBy": "TC-002-05",
    "steps": [
      { "stepNo": 1, "instruction": "Go to notes list with search API set to fail", "action": "navigate", "selector": "/notes", "value": "" },
      { "stepNo": 2, "instruction": "Enter a search term", "action": "fill", "selector": "[data-testid=\"notes-list-search-input\"]", "value": "planning" },
      { "stepNo": 3, "instruction": "Wait for failed request", "action": "wait", "selector": "", "value": "500" },
      { "stepNo": 4, "instruction": "Verify error message displays and previous list preserved", "action": "assert_visible", "selector": "[data-testid=\"notes-list-error-message\"]", "value": "" }
    ],
    "expectedResult": "an error message displays and the previous list state is preserved rather than showing a blank screen"
  },
  {
    "testId": "TC-002-06",
    "title": "the member selects a tag from the filter",
    "module": "select a tag from a filter control to show only notes with that tag",
    "coveredBy": "TC-002-07",
    "steps": [
      { "stepNo": 1, "instruction": "Go to notes list", "action": "navigate", "selector": "/notes", "value": "" },
      { "stepNo": 2, "instruction": "Select a tag from the filter control", "action": "select", "selector": "[data-testid=\"notes-list-tag-filter-select\"]", "value": "planning" },
      { "stepNo": 3, "instruction": "Verify list updates to only notes with that tag, newest first", "action": "assert_text", "selector": "[data-testid=\"notes-list-item\"]:first-child", "value": "planning" }
    ],
    "expectedResult": "the list updates to show only notes carrying that tag, newest first"
  },
  {
    "testId": "TC-002-07",
    "title": "[edge] the member clears the tag filter",
    "module": "select a tag from a filter control to show only notes with that tag",
    "coveredBy": "TC-002-08",
    "steps": [
      { "stepNo": 1, "instruction": "Apply a tag filter", "action": "select", "selector": "[data-testid=\"notes-list-tag-filter-select\"]", "value": "planning" },
      { "stepNo": 2, "instruction": "Clear the tag filter", "action": "select", "selector": "[data-testid=\"notes-list-tag-filter-select\"]", "value": "" },
      { "stepNo": 3, "instruction": "Verify full newest-first list returns without reload", "action": "assert_visible", "selector": "[data-testid=\"notes-list-item\"]:first-child", "value": "" }
    ],
    "expectedResult": "the full newest-first list returns without needing a page reload"
  },
  {
    "testId": "TC-002-08",
    "title": "the member scrolls to the end of the current page or clicks 'load more'",
    "module": "load additional notes via pagination or infinite scroll instead of all notes at once",
    "coveredBy": "TC-002-12",
    "steps": [
      { "stepNo": 1, "instruction": "Go to notes list with more notes than fit one page", "action": "navigate", "selector": "/notes", "value": "" },
      { "stepNo": 2, "instruction": "Click 'load more'", "action": "click", "selector": "[data-testid=\"notes-list-load-more-button\"]", "value": "" },
      { "stepNo": 3, "instruction": "Verify next page appended in newest-first order", "action": "assert_visible", "selector": "[data-testid=\"notes-list-item\"]:nth-child(11)", "value": "" }
    ],
    "expectedResult": "the next page of notes loads and appends in newest-first order"
  },
  {
    "testId": "TC-002-09",
    "title": "[edge] they scroll further or click 'load more'",
    "module": "load additional notes via pagination or infinite scroll instead of all notes at once",
    "coveredBy": "TC-002-13",
    "steps": [
      { "stepNo": 1, "instruction": "Go to notes list already on last page of results", "action": "navigate", "selector": "/notes", "value": "" },
      { "stepNo": 2, "instruction": "Click 'load more' again", "action": "click", "selector": "[data-testid=\"notes-list-load-more-button\"]", "value": "" },
      { "stepNo": 3, "instruction": "Verify 'end of list' indicator shows instead of a spinner", "action": "assert_visible", "selector": "[data-testid=\"notes-list-end-indicator\"]", "value": "" }
    ],
    "expectedResult": "no further requests are made and an 'end of list' indicator shows instead of a repeated spinner"
  },
  {
    "testId": "TC-002-10",
    "title": "both filters are active",
    "module": "apply a text search query together with a selected tag filter at the same time",
    "coveredBy": "TC-002-10",
    "steps": [
      { "stepNo": 1, "instruction": "Go to notes list", "action": "navigate", "selector": "/notes", "value": "" },
      { "stepNo": 2, "instruction": "Enter a search term", "action": "fill", "selector": "[data-testid=\"notes-list-search-input\"]", "value": "sprint" },
      { "stepNo": 3, "instruction": "Select a tag filter", "action": "select", "selector": "[data-testid=\"notes-list-tag-filter-select\"]", "value": "planning" },
      { "stepNo": 4, "instruction": "Wait for debounce", "action": "wait", "selector": "", "value": "500" },
      { "stepNo": 5, "instruction": "Verify list shows notes matching both filters, newest first", "action": "assert_text", "selector": "[data-testid=\"notes-list-item\"]:first-child", "value": "sprint" }
    ],
    "expectedResult": "the list shows only notes matching both the text and the tag, newest first"
  },
  {
    "testId": "TC-002-11",
    "title": "[edge] the combined filter returns zero results",
    "module": "apply a text search query together with a selected tag filter at the same time",
    "coveredBy": "TC-002-11",
    "steps": [
      { "stepNo": 1, "instruction": "Enter a search term with no matches for the selected tag", "action": "fill", "selector": "[data-testid=\"notes-list-search-input\"]", "value": "zzznomatch" },
      { "stepNo": 2, "instruction": "Select a tag filter", "action": "select", "selector": "[data-testid=\"notes-list-tag-filter-select\"]", "value": "planning" },
      { "stepNo": 3, "instruction": "Wait for debounce", "action": "wait", "selector": "", "value": "500" },
      { "stepNo": 4, "instruction": "Verify 'no results' message references both active filters", "action": "assert_text", "selector": "[data-testid=\"notes-list-no-results\"]", "value": "planning" }
    ],
    "expectedResult": "a 'no results' message displays referencing both active filters"
  },
  {
    "testId": "TC-002-12",
    "title": "the request is in flight",
    "module": "see a lightweight loading indicator while the list is refreshing",
    "coveredBy": "TC-002-12",
    "steps": [
      { "stepNo": 1, "instruction": "Go to notes list", "action": "navigate", "selector": "/notes", "value": "" },
      { "stepNo": 2, "instruction": "Apply a search filter", "action": "fill", "selector": "[data-testid=\"notes-list-search-input\"]", "value": "planning" },
      { "stepNo": 3, "instruction": "Verify loading indicator shows while request is in flight", "action": "assert_visible", "selector": "[data-testid=\"notes-list-loading-indicator\"]", "value": "" },
      { "stepNo": 4, "instruction": "Verify loading indicator disappears once results render", "action": "assert_visible", "selector": "[data-testid=\"notes-list-item\"]:first-child", "value": "" }
    ],
    "expectedResult": "a loading indicator shows and disappears once results render"
  },
  {
    "testId": "TC-002-13",
    "title": "[edge] a newer request is issued",
    "module": "see a lightweight loading indicator while the list is refreshing",
    "coveredBy": "TC-002-13",
    "steps": [
      { "stepNo": 1, "instruction": "Type an initial search term", "action": "fill", "selector": "[data-testid=\"notes-list-search-input\"]", "value": "plan" },
      { "stepNo": 2, "instruction": "Change search text again before previous request finishes", "action": "fill", "selector": "[data-testid=\"notes-list-search-input\"]", "value": "planning" },
      { "stepNo": 3, "instruction": "Wait for debounce and completion", "action": "wait", "selector": "", "value": "700" },
      { "stepNo": 4, "instruction": "Verify only the latest search's results are rendered", "action": "assert_text", "selector": "[data-testid=\"notes-list-item\"]:first-child", "value": "planning" }
    ],
    "expectedResult": "only the result of the latest request is rendered, discarding any stale in-flight response"
  },
  {
    "testId": "TC-003-01",
    "title": "they select Archive and confirm",
    "module": "archive a note directly from the notes list or detail view",
    "coveredBy": "TC-003-01",
    "steps": [
      { "stepNo": 1, "instruction": "Log in as a team lead and open an active note", "action": "navigate", "selector": "/notes/1", "value": "" },
      { "stepNo": 2, "instruction": "Click Archive", "action": "click", "selector": "[data-testid=\"note-detail-archive-button\"]", "value": "" },
      { "stepNo": 3, "instruction": "Confirm archive", "action": "click", "selector": "[data-testid=\"archive-confirm-button\"]", "value": "" },
      { "stepNo": 4, "instruction": "Go to active notes list", "action": "navigate", "selector": "/notes", "value": "" },
      { "stepNo": 5, "instruction": "Verify note removed from active list", "action": "assert_visible", "selector": "[data-testid=\"notes-list-item\"]", "value": "" }
    ],
    "expectedResult": "the note is removed from the active list immediately"
  },
  {
    "testId": "TC-003-02",
    "title": "[edge] they view a note's controls",
    "module": "archive a note directly from the notes list or detail view",
    "coveredBy": "TC-004-07",
    "steps": [
      { "stepNo": 1, "instruction": "Log in as a team member (non-lead)", "action": "navigate", "selector": "/notes/1", "value": "" },
      { "stepNo": 2, "instruction": "Verify no Archive button is shown or it is disabled", "action": "assert_visible", "selector": "[data-testid=\"note-detail-archive-button\"]", "value": "" }
    ],
    "expectedResult": "no Archive button is shown or it is disabled"
  },
  {
    "testId": "TC-003-03",
    "title": "[error] the archive request to the API fails",
    "module": "archive a note directly from the notes list or detail view",
    "coveredBy": "TC-003-01",
    "steps": [
      { "stepNo": 1, "instruction": "Open an active note as lead with archive API set to fail", "action": "navigate", "selector": "/notes/1", "value": "" },
      { "stepNo": 2, "instruction": "Click Archive", "action": "click", "selector": "[data-testid=\"note-detail-archive-button\"]", "value": "" },
      { "stepNo": 3, "instruction": "Confirm archive", "action": "click", "selector": "[data-testid=\"archive-confirm-button\"]", "value": "" },
      { "stepNo": 4, "instruction": "Verify note still visible in active list and error shown", "action": "assert_visible", "selector": "[data-testid=\"note-detail-error-message\"]", "value": "" }
    ],
    "expectedResult": "the note remains visible in the active list and an error message is shown"
  },
  {
    "testId": "TC-003-04",
    "title": "they navigate to the archived notes view",
    "module": "view a separate list of archived notes",
    "coveredBy": "TC-003-12",
    "steps": [
      { "stepNo": 1, "instruction": "Log in as a team lead", "action": "navigate", "selector": "/notes/archived", "value": "" },
      { "stepNo": 2, "instruction": "Verify archived notes are listed newest first", "action": "assert_visible", "selector": "[data-testid=\"archived-notes-item\"]:first-child", "value": "" }
    ],
    "expectedResult": "they see all archived notes listed newest first"
  },
  {
    "testId": "TC-003-05",
    "title": "[edge] a lead opens the archived notes view",
    "module": "view a separate list of archived notes",
    "coveredBy": "TC-003-14",
    "steps": [
      { "stepNo": 1, "instruction": "Log in as a lead where no notes have been archived yet", "action": "navigate", "selector": "/notes/archived", "value": "" },
      { "stepNo": 2, "instruction": "Verify empty-state message is shown", "action": "assert_visible", "selector": "[data-testid=\"archived-notes-empty-state\"]", "value": "" }
    ],
    "expectedResult": "an empty-state message is shown instead of a blank list"
  },
  {
    "testId": "TC-003-06",
    "title": "[error] they try to access the archived notes view",
    "module": "view a separate list of archived notes",
    "coveredBy": "TC-003-13",
    "steps": [
      { "stepNo": 1, "instruction": "Log in as a team member (non-lead)", "action": "navigate", "selector": "/notes", "value": "" },
      { "stepNo": 2, "instruction": "Verify archived notes nav link is hidden", "action": "assert_visible", "selector": "[data-testid=\"app-nav-archived-notes-link\"]", "value": "" },
      { "stepNo": 3, "instruction": "Attempt direct navigation to archived notes view", "action": "navigate", "selector": "/notes/archived", "value": "" },
      { "stepNo": 4, "instruction": "Verify access is denied", "action": "assert_url", "selector": "", "value": "/notes" }
    ],
    "expectedResult": "the view/navigation link is hidden or access is denied"
  },
  {
    "testId": "TC-003-07",
    "title": "they select Restore on a note",
    "module": "restore an archived note back to the active list",
    "coveredBy": "TC-004-10",
    "steps": [
      { "stepNo": 1, "instruction": "Log in as lead and go to archived notes list", "action": "navigate", "selector": "/notes/archived", "value": "" },
      { "stepNo": 2, "instruction": "Click Restore on a note", "action": "click", "selector": "[data-testid=\"archived-notes-restore-button\"]", "value": "" },
      { "stepNo": 3, "instruction": "Verify note disappears from archived list", "action": "assert_visible", "selector": "[data-testid=\"archived-notes-item\"]", "value": "" },
      { "stepNo": 4, "instruction": "Go to active notes list", "action": "navigate", "selector": "/notes", "value": "" },
      { "stepNo": 5, "instruction": "Verify note reappears in active list", "action": "assert_visible", "selector": "[data-testid=\"notes-list-item\"]", "value": "" }
    ],
    "expectedResult": "the note disappears from the archived list and reappears in the active list"
  },
  {
    "testId": "TC-003-08",
    "title": "[edge] the restore completes",
    "module": "restore an archived note back to the active list",
    "coveredBy": "TC-004-10",
    "steps": [
      { "stepNo": 1, "instruction": "Restore a note from the archived list", "action": "click", "selector": "[data-testid=\"archived-notes-restore-button\"]", "value": "" },
      { "stepNo": 2, "instruction": "Go to active notes list", "action": "navigate", "selector": "/notes", "value": "" },
      { "stepNo": 3, "instruction": "Verify restored note position reflects newest-first order", "action": "assert_visible", "selector": "[data-testid=\"notes-list-item\"]", "value": "" }
    ],
    "expectedResult": "the note's position in the active list reflects its original creation/update order (newest first)"
  },
  {
    "testId": "TC-003-09",
    "title": "[error] the restore request to the API fails",
    "module": "restore an archived note back to the active list",
    "coveredBy": "TC-004-10",
    "steps": [
      { "stepNo": 1, "instruction": "Go to archived notes list with restore API set to fail", "action": "navigate", "selector": "/notes/archived", "value": "" },
      { "stepNo": 2, "instruction": "Click Restore", "action": "click", "selector": "[data-testid=\"archived-notes-restore-button\"]", "value": "" },
      { "stepNo": 3, "instruction": "Verify note stays in archived list and error message shown", "action": "assert_visible", "selector": "[data-testid=\"archived-notes-error-message\"]", "value": "" }
    ],
    "expectedResult": "the note stays in the archived list and an error message is shown"
  },
  {
    "testId": "TC-003-10",
    "title": "a team member opens the notes list",
    "module": "see only active notes in the main list and search results",
    "coveredBy": "TC-003-10",
    "steps": [
      { "stepNo": 1, "instruction": "Log in as team member where some notes are archived", "action": "navigate", "selector": "/notes", "value": "" },
      { "stepNo": 2, "instruction": "Verify archived notes are not displayed", "action": "assert_visible", "selector": "[data-testid=\"notes-list-item\"]", "value": "" }
    ],
    "expectedResult": "archived notes are not displayed"
  },
  {
    "testId": "TC-003-11",
    "title": "[edge] a team member searches or filters",
    "module": "see only active notes in the main list and search results",
    "coveredBy": "TC-003-11",
    "steps": [
      { "stepNo": 1, "instruction": "Search for a term matching an archived note", "action": "fill", "selector": "[data-testid=\"notes-list-search-input\"]", "value": "archivedterm" },
      { "stepNo": 2, "instruction": "Wait for debounce", "action": "wait", "selector": "", "value": "500" },
      { "stepNo": 3, "instruction": "Verify archived note excluded from results", "action": "assert_visible", "selector": "[data-testid=\"notes-list-no-results\"]", "value": "" }
    ],
    "expectedResult": "the archived note is excluded from the results"
  },
  {
    "testId": "TC-003-12",
    "title": "a confirmation dialog appears and they confirm",
    "module": "confirm an archive action before it is applied",
    "coveredBy": "TC-003-01",
    "steps": [
      { "stepNo": 1, "instruction": "Log in as lead and select Archive on a note", "action": "click", "selector": "[data-testid=\"note-detail-archive-button\"]", "value": "" },
      { "stepNo": 2, "instruction": "Verify confirmation dialog appears", "action": "assert_visible", "selector": "[data-testid=\"archive-confirm-dialog\"]", "value": "" },
      { "stepNo": 3, "instruction": "Confirm the archive", "action": "click", "selector": "[data-testid=\"archive-confirm-button\"]", "value": "" },
      { "stepNo": 4, "instruction": "Verify note removed from active list", "action": "assert_visible", "selector": "[data-testid=\"notes-list-item\"]", "value": "" }
    ],
    "expectedResult": "the note is archived and removed from the active list"
  },
  {
    "testId": "TC-003-13",
    "title": "[edge] the confirmation dialog appears and they cancel",
    "module": "confirm an archive action before it is applied",
    "coveredBy": "TC-003-01",
    "steps": [
      { "stepNo": 1, "instruction": "Select Archive on a note", "action": "click", "selector": "[data-testid=\"note-detail-archive-button\"]", "value": "" },
      { "stepNo": 2, "instruction": "Cancel the confirmation dialog", "action": "click", "selector": "[data-testid=\"archive-cancel-button\"]", "value": "" },
      { "stepNo": 3, "instruction": "Verify note remains unchanged in active list", "action": "assert_visible", "selector": "[data-testid=\"note-detail-title\"]", "value": "" }
    ],
    "expectedResult": "the note remains unchanged in the active list"
  },
  {
    "testId": "TC-004-01",
    "title": "they submit a correct email and password",
    "module": "log in with their email and password",
    "coveredBy": "TC-004-01",
    "steps": [
      { "stepNo": 1, "instruction": "Navigate to login screen", "action": "navigate", "selector": "/login", "value": "" },
      { "stepNo": 2, "instruction": "Enter correct email", "action": "fill", "selector": "[data-testid=\"login-email-input\"]", "value": "member@testmail.com" },
      { "stepNo": 3, "instruction": "Enter correct password", "action": "fill", "selector": "[data-testid=\"login-password-input\"]", "value": "Password123!" },
      { "stepNo": 4, "instruction": "Submit login", "action": "click", "selector": "[data-testid=\"login-submit-button\"]", "value": "" },
      { "stepNo": 5, "instruction": "Verify redirected to notes list", "action": "assert_url", "selector": "", "value": "/notes" }
    ],
    "expectedResult": "they are authenticated and redirected to the notes list"
  },
  {
    "testId": "TC-004-02",
    "title": "[error] they submit an incorrect email or password",
    "module": "log in with their email and password",
    "coveredBy": "TC-004-02",
    "steps": [
      { "stepNo": 1, "instruction": "Navigate to login screen", "action": "navigate", "selector": "/login", "value": "" },
      { "stepNo": 2, "instruction": "Enter incorrect password", "action": "fill", "selector": "[data-testid=\"login-password-input\"]", "value": "WrongPass1!" },
      { "stepNo": 3, "instruction": "Enter email", "action": "fill", "selector": "[data-testid=\"login-email-input\"]", "value": "member@testmail.com" },
      { "stepNo": 4, "instruction": "Submit login", "action": "click", "selector": "[data-testid=\"login-submit-button\"]", "value": "" },
      { "stepNo": 5, "instruction": "Verify inline error shown and no redirect", "action": "assert_visible", "selector": "[data-testid=\"login-error-message\"]", "value": "" }
    ],
    "expectedResult": "an inline error message is shown and no session is created"
  },
  {
    "testId": "TC-004-03",
    "title": "[edge] they submit the form with an empty email or password field",
    "module": "log in with their email and password",
    "coveredBy": "TC-004-03",
    "steps": [
      { "stepNo": 1, "instruction": "Navigate to login screen", "action": "navigate", "selector": "/login", "value": "" },
      { "stepNo": 2, "instruction": "Leave password empty, enter email", "action": "fill", "selector": "[data-testid=\"login-email-input\"]", "value": "member@testmail.com" },
      { "stepNo": 3, "instruction": "Submit login", "action": "click", "selector": "[data-testid=\"login-submit-button\"]", "value": "" },
      { "stepNo": 4, "instruction": "Verify required-field validation message shown", "action": "assert_visible", "selector": "[data-testid=\"login-password-error\"]", "value": "" }
    ],
    "expectedResult": "submission is blocked and a required-field validation message is shown"
  },
  {
    "testId": "TC-004-04",
    "title": "they navigate directly to the notes list URL",
    "module": "have their session checked before viewing any notes page",
    "coveredBy": "TC-004-04",
    "steps": [
      { "stepNo": 1, "instruction": "Log in to establish a valid session", "action": "navigate", "selector": "/login", "value": "" },
      { "stepNo": 2, "instruction": "Enter email", "action": "fill", "selector": "[data-testid=\"login-email-input\"]", "value": "member@testmail.com" },
      { "stepNo": 3, "instruction": "Enter password and submit", "action": "fill", "selector": "[data-testid=\"login-password-input\"]", "value": "Password123!" },
      { "stepNo": 4, "instruction": "Submit login", "action": "click", "selector": "[data-testid=\"login-submit-button\"]", "value": "" },
      { "stepNo": 5, "instruction": "Navigate directly to notes list URL", "action": "navigate", "selector": "/notes", "value": "" },
      { "stepNo": 6, "instruction": "Verify notes list displayed without re-login", "action": "assert_visible", "selector": "[data-testid=\"notes-list-item\"]", "value": "" }
    ],
    "expectedResult": "the notes list is displayed without re-login"
  },
  {
    "testId": "TC-004-05",
    "title": "[error] they navigate directly to any notes page URL",
    "module": "have their session checked before viewing any notes page",
    "coveredBy": "TC-004-05",
    "steps": [
      { "stepNo": 1, "instruction": "Clear any session and navigate directly to a protected notes URL", "action": "navigate", "selector": "/notes", "value": "" },
      { "stepNo": 2, "instruction": "Verify redirected to login screen", "action": "assert_url", "selector": "", "value": "/login" }
    ],
    "expectedResult": "they are redirected to the login screen"
  },
  {
    "testId": "TC-004-06",
    "title": "[edge] they navigate to the login page URL",
    "module": "have their session checked before viewing any notes page",
    "coveredBy": "TC-004-06",
    "steps": [
      { "stepNo": 1, "instruction": "Log in to establish a valid session", "action": "navigate", "selector": "/login", "value": "" },
      { "stepNo": 2, "instruction": "Enter email and password and submit", "action": "fill", "selector": "[data-testid=\"login-email-input\"]", "value": "member@testmail.com" },
      { "stepNo": 3, "instruction": "Submit login", "action": "click", "selector": "[data-testid=\"login-submit-button\"]", "value": "" },
      { "stepNo": 4, "instruction": "Navigate to login page URL while already logged in", "action": "navigate", "selector": "/login", "value": "" },
      { "stepNo": 5, "instruction": "Verify redirected to notes list", "action": "assert_url", "selector": "", "value": "/notes" }
    ],
    "expectedResult": "they are redirected to the notes list instead of seeing the login form"
  },
  {
    "testId": "TC-004-07",
    "title": "the note list renders",
    "module": "see archive and restore controls only when logged in with the lead role",
    "coveredBy": "TC-004-07",
    "steps": [
      { "stepNo": 1, "instruction": "Log in as a team lead", "action": "navigate", "selector": "/notes", "value": "" },
      { "stepNo": 2, "instruction": "Verify archive button visible on active notes", "action": "assert_visible", "selector": "[data-testid=\"note-detail-archive-button\"]", "value": "" },
      { "stepNo": 3, "instruction": "Navigate to archived notes", "action": "navigate", "selector": "/notes/archived", "value": "" },
      { "stepNo": 4, "instruction": "Verify restore button visible on archived notes", "action": "assert_visible", "selector": "[data-testid=\"archived-notes-restore-button\"]", "value": "" }
    ],
    "expectedResult": "an archive button is visible on active notes and a restore button is visible on archived notes"
  },
  {
    "testId": "TC-004-08",
    "title": "[error] the note list renders",
    "module": "see archive and restore controls only when logged in with the lead role",
    "coveredBy": "TC-004-07",
    "steps": [
      { "stepNo": 1, "instruction": "Log in as a team member (non-lead)", "action": "navigate", "selector": "/notes", "value": "" },
      { "stepNo": 2, "instruction": "Verify no archive or restore controls visible", "action": "assert_visible", "selector": "[data-testid=\"note-detail-archive-button\"]", "value": "" }
    ],
    "expectedResult": "no archive or restore controls are visible anywhere in the UI"
  },
  {
    "testId": "TC-004-09",
    "title": "[edge] the notes list renders",
    "module": "see archive and restore controls only when logged in with the lead role",
    "coveredBy": "TC-004-07",
    "steps": [
      { "stepNo": 1, "instruction": "Log in with a session where role data is missing or unresolved", "action": "navigate", "selector": "/notes", "value": "" },
      { "stepNo": 2, "instruction": "Verify archive/restore controls default to hidden", "action": "assert_visible", "selector": "[data-testid=\"note-detail-archive-button\"]", "value": "" }
    ],
    "expectedResult": "archive/restore controls default to hidden until role is confirmed"
  },
  {
    "testId": "TC-004-10",
    "title": "the logout action completes",
    "module": "log out to end their session",
    "coveredBy": "TC-004-07",
    "steps": [
      { "stepNo": 1, "instruction": "Log in and go to notes list", "action": "navigate", "selector": "/notes", "value": "" },
      { "stepNo": 2, "instruction": "Click logout", "action": "click", "selector": "[data-testid=\"app-nav-logout-button\"]", "value": "" },
      { "stepNo": 3, "instruction": "Verify session cleared and redirected to login", "action": "assert_url", "selector": "", "value": "/login" }
    ],
    "expectedResult": "their session is cleared and they are redirected to the login screen"
  },
  {
    "testId": "TC-004-11",
    "title": "[edge] they press the browser back button to the notes page",
    "module": "log out to end their session",
    "coveredBy": "TC-004-07",
    "steps": [
      { "stepNo": 1, "instruction": "Log in, view notes list, then log out", "action": "navigate", "selector": "/notes", "value": "" },
      { "stepNo": 2, "instruction": "Click logout", "action": "click", "selector": "[data-testid=\"app-nav-logout-button\"]", "value": "" },
      { "stepNo": 3, "instruction": "Press browser back button", "action": "navigate", "selector": "back", "value": "" },
      { "stepNo": 4, "instruction": "Verify redirected to login instead of cached notes", "action": "assert_url", "selector": "", "value": "/login" }
    ],
    "expectedResult": "they are redirected to the login screen rather than seeing cached notes"
  },
  {
    "testId": "TC-004-12",
    "title": "[error] the archive action is submitted and the API returns 403",
    "module": "receive clear feedback if a role-restricted action they attempt is rejected",
    "coveredBy": "TC-003-07",
    "steps": [
      { "stepNo": 1, "instruction": "Simulate stale UI state showing an archive control, then click Archive", "action": "click", "selector": "[data-testid=\"note-detail-archive-button\"]", "value": "" },
      { "stepNo": 2, "instruction": "Confirm archive action", "action": "click", "selector": "[data-testid=\"archive-confirm-button\"]", "value": "" },
      { "stepNo": 3, "instruction": "Verify not-authorized message shown", "action": "assert_visible", "selector": "[data-testid=\"role-restricted-feedback-message\"]", "value": "" },
      { "stepNo": 4, "instruction": "Verify note's state remains unchanged", "action": "assert_visible", "selector": "[data-testid=\"note-detail-title\"]", "value": "" }
    ],
    "expectedResult": "a not-authorized message is shown and the note's state remains unchanged"
  },
  {
    "testId": "TC-004-13",
    "title": "[edge] they submit any note action",
    "module": "receive clear feedback if a role-restricted action they attempt is rejected",
    "coveredBy": "TC-004-05",
    "steps": [
      { "stepNo": 1, "instruction": "Log in and open a note with session set to expire mid-action", "action": "navigate", "selector": "/notes/1/edit", "value": "" },
      { "stepNo": 2, "instruction": "Submit a note action (Save)", "action": "click", "selector": "[data-testid=\"note-form-save-button\"]", "value": "" },
      { "stepNo": 3, "instruction": "Verify redirected to login screen instead of a generic error", "action": "assert_url", "selector": "", "value": "/login" }
    ],
    "expectedResult": "they are redirected to the login screen instead of seeing a generic error"
  }
]
```
{"documents": [{"id": "TC-001-01", "type": "test_plan", "path": "test-plans/TC-001-01.md", "content": "---\nid: TC-001-01\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-11T08:45:38Z\nstory: [[US-001-01]]\nepic: [[EP-001]]\nmodule: create a new note with a title, markdown body and tags\n---\n\n# TC-001-01: they enter a title, markdown body, at least one tag, and click Save\n\n**Story:** [[US-001-01]]\n**Module:** create a new note with a title, markdown body and tags\n\n## Preconditions\nthe team member is logged in and on the new note screen\n\n## Expected Result\nthe note is created and the team member is returned to the notes list with the new note shown first\n\n## Acceptance Criteria\nGiven the team member is logged in and on the new note screen, when they enter a title, markdown body, at least one tag, and click Save, then the note is created and the team member is returned to the notes list with the new note shown first\nGiven the team member is on the new note screen, when they click Save with the title field empty, then the form shows an inline validation error and the note is not saved\nGiven the team member is creating a note, when they click Save without entering any tags, then the note is saved successfully with an empty tag list\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Log in as a team member | navigate | `/login` |  |\n| 2 | Enter email | fill | `[data-testid=\"login-email-input\"]` | member@testmail.com |\n| 3 | Enter password | fill | `[data-testid=\"login-password-input\"]` | Password123! |\n| 4 | Submit login | click | `[data-testid=\"login-submit-button\"]` |  |\n| 5 | Go to new note screen | navigate | `/notes/new` |  |\n| 6 | Enter title | fill | `[data-testid=\"note-form-title-input\"]` | Sprint Planning Notes |\n| 7 | Enter markdown body | fill | `[data-testid=\"note-form-body-textarea\"]` | # Agenda\n- Item one |\n| 8 | Add a tag and press Enter | fill | `[data-testid=\"note-form-tag-input\"]` | planning\n |\n| 9 | Click Save | click | `[data-testid=\"note-form-save-button\"]` |  |\n| 10 | Verify redirect to notes list | assert_url | `` | /notes |\n| 11 | Verify new note shown first | assert_text | `[data-testid=\"notes-list-item\"]:first-child` | Sprint Planning Notes |\n", "parent_ids": ["US-001-01", "EP-001"]}, {"id": "TC-001-02", "type": "test_plan", "path": "test-plans/TC-001-02.md", "content": "---\nid: TC-001-02\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-11T08:45:38Z\nstory: [[US-001-01]]\nepic: [[EP-001]]\nmodule: create a new note with a title, markdown body and tags\n---\n\n# TC-001-02: [error] they click Save with the title field empty\n\n**Story:** [[US-001-01]]\n**Module:** create a new note with a title, markdown body and tags\n\n## Preconditions\nthe team member is on the new note screen\n\n## Expected Result\nthe form shows an inline validation error and the note is not saved\n\n## Acceptance Criteria\nGiven the team member is logged in and on the new note screen, when they enter a title, markdown body, at least one tag, and click Save, then the note is created and the team member is returned to the notes list with the new note shown first\nGiven the team member is on the new note screen, when they click Save with the title field empty, then the form shows an inline validation error and the note is not saved\nGiven the team member is creating a note, when they click Save without entering any tags, then the note is saved successfully with an empty tag list\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Log in and go to new note screen | navigate | `/notes/new` |  |\n| 2 | Leave title empty, fill body | fill | `[data-testid=\"note-form-body-textarea\"]` | Body text |\n| 3 | Click Save | click | `[data-testid=\"note-form-save-button\"]` |  |\n| 4 | Verify inline validation error shown | assert_visible | `[data-testid=\"note-form-title-error\"]` |  |\n| 5 | Verify still on new note screen | assert_url | `` | /notes/new |\n", "parent_ids": ["US-001-01", "EP-001"]}, {"id": "TC-001-03", "type": "test_plan", "path": "test-plans/TC-001-03.md", "content": "---\nid: TC-001-03\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-11T08:45:38Z\nstory: [[US-001-01]]\nepic: [[EP-001]]\nmodule: create a new note with a title, markdown body and tags\n---\n\n# TC-001-03: [edge] they click Save without entering any tags\n\n**Story:** [[US-001-01]]\n**Module:** create a new note with a title, markdown body and tags\n\n## Preconditions\nthe team member is creating a note\n\n## Expected Result\nthe note is saved successfully with an empty tag list\n\n## Acceptance Criteria\nGiven the team member is logged in and on the new note screen, when they enter a title, markdown body, at least one tag, and click Save, then the note is created and the team member is returned to the notes list with the new note shown first\nGiven the team member is on the new note screen, when they click Save with the title field empty, then the form shows an inline validation error and the note is not saved\nGiven the team member is creating a note, when they click Save without entering any tags, then the note is saved successfully with an empty tag list\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Go to new note screen | navigate | `/notes/new` |  |\n| 2 | Fill title | fill | `[data-testid=\"note-form-title-input\"]` | No Tags Note |\n| 3 | Fill body | fill | `[data-testid=\"note-form-body-textarea\"]` | Body text |\n| 4 | Click Save without tags | click | `[data-testid=\"note-form-save-button\"]` |  |\n| 5 | Verify note saved and shown first with no tag chips | assert_visible | `[data-testid=\"notes-list-item\"]:first-child` |  |\n", "parent_ids": ["US-001-01", "EP-001"]}, {"id": "TC-001-04", "type": "test_plan", "path": "test-plans/TC-001-04.md", "content": "---\nid: TC-001-04\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-11T08:45:38Z\nstory: [[US-001-02]]\nepic: [[EP-001]]\nmodule: edit the title, markdown body or tags of an existing note\n---\n\n# TC-001-04: they change the title, body text, or tags and click Save\n\n**Story:** [[US-001-02]]\n**Module:** edit the title, markdown body or tags of an existing note\n\n## Preconditions\nthe team member opens an existing note they can edit\n\n## Expected Result\nthe note is updated and the changes are visible when the note is reopened\n\n## Acceptance Criteria\nGiven the team member opens an existing note they can edit, when they change the title, body text, or tags and click Save, then the note is updated and the changes are visible when the note is reopened\nGiven the team member is editing a note, when they remove all existing tags and save, then the note is saved with no tags without error\nGiven two team members have the same note open for editing, when the second member saves after the note was already changed by the first, then the UI shows a conflict/stale-data message instead of silently overwriting\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Open an existing note's detail page | navigate | `/notes/1` |  |\n| 2 | Click Edit | click | `[data-testid=\"note-detail-edit-button\"]` |  |\n| 3 | Change the title | fill | `[data-testid=\"note-form-title-input\"]` | Updated Title |\n| 4 | Click Save | click | `[data-testid=\"note-form-save-button\"]` |  |\n| 5 | Reopen the note | navigate | `/notes/1` |  |\n| 6 | Verify updated title visible | assert_text | `[data-testid=\"note-detail-title\"]` | Updated Title |\n", "parent_ids": ["US-001-02", "EP-001"]}, {"id": "TC-001-05", "type": "test_plan", "path": "test-plans/TC-001-05.md", "content": "---\nid: TC-001-05\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-11T08:45:38Z\nstory: [[US-001-02]]\nepic: [[EP-001]]\nmodule: edit the title, markdown body or tags of an existing note\n---\n\n# TC-001-05: [edge] they remove all existing tags and save\n\n**Story:** [[US-001-02]]\n**Module:** edit the title, markdown body or tags of an existing note\n\n## Preconditions\nthe team member is editing a note\n\n## Expected Result\nthe note is saved with no tags without error\n\n## Acceptance Criteria\nGiven the team member opens an existing note they can edit, when they change the title, body text, or tags and click Save, then the note is updated and the changes are visible when the note is reopened\nGiven the team member is editing a note, when they remove all existing tags and save, then the note is saved with no tags without error\nGiven two team members have the same note open for editing, when the second member saves after the note was already changed by the first, then the UI shows a conflict/stale-data message instead of silently overwriting\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Open note edit screen | navigate | `/notes/1/edit` |  |\n| 2 | Remove existing tag chip | click | `[data-testid=\"note-form-tag-chip\"] >> [data-testid=\"note-form-tag-remove-icon\"]` |  |\n| 3 | Click Save | click | `[data-testid=\"note-form-save-button\"]` |  |\n| 4 | Verify note saved with no tags and no error | assert_visible | `[data-testid=\"note-detail-title\"]` |  |\n", "parent_ids": ["US-001-02", "EP-001"]}, {"id": "TC-001-06", "type": "test_plan", "path": "test-plans/TC-001-06.md", "content": "---\nid: TC-001-06\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-11T08:45:38Z\nstory: [[US-001-02]]\nepic: [[EP-001]]\nmodule: edit the title, markdown body or tags of an existing note\n---\n\n# TC-001-06: [error] the second member saves after the note was already changed by the first\n\n**Story:** [[US-001-02]]\n**Module:** edit the title, markdown body or tags of an existing note\n\n## Preconditions\ntwo team members have the same note open for editing\n\n## Expected Result\nthe UI shows a conflict/stale-data message instead of silently overwriting\n\n## Acceptance Criteria\nGiven the team member opens an existing note they can edit, when they change the title, body text, or tags and click Save, then the note is updated and the changes are visible when the note is reopened\nGiven the team member is editing a note, when they remove all existing tags and save, then the note is saved with no tags without error\nGiven two team members have the same note open for editing, when the second member saves after the note was already changed by the first, then the UI shows a conflict/stale-data message instead of silently overwriting\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Open note edit screen as member A (simulated prior edit already applied server-side) | navigate | `/notes/1/edit` |  |\n| 2 | Change body text | fill | `[data-testid=\"note-form-body-textarea\"]` | Second editor's change |\n| 3 | Click Save on stale note | click | `[data-testid=\"note-form-save-button\"]` |  |\n| 4 | Verify conflict/stale-data message shown | assert_visible | `[data-testid=\"note-detail-conflict-message\"]` |  |\n", "parent_ids": ["US-001-02", "EP-001"]}, {"id": "TC-001-07", "type": "test_plan", "path": "test-plans/TC-001-07.md", "content": "---\nid: TC-001-07\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-11T08:45:38Z\nstory: [[US-001-03]]\nepic: [[EP-001]]\nmodule: delete a note they no longer need\n---\n\n# TC-001-07: they click Delete and confirm the action\n\n**Story:** [[US-001-03]]\n**Module:** delete a note they no longer need\n\n## Preconditions\nthe team member is viewing a note\n\n## Expected Result\nthe note is removed and no longer appears in the notes list\n\n## Acceptance Criteria\nGiven the team member is viewing a note, when they click Delete and confirm the action, then the note is removed and no longer appears in the notes list\nGiven the team member clicks Delete, when the confirmation dialog appears, then clicking Cancel leaves the note unchanged and still in the list\nGiven the note was already deleted by another user, when the team member attempts to delete it again, then the UI shows a 'note no longer exists' message instead of a generic failure\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Open an existing note | navigate | `/notes/1` |  |\n| 2 | Click Delete | click | `[data-testid=\"note-detail-delete-button\"]` |  |\n| 3 | Confirm deletion | click | `[data-testid=\"delete-confirm-button\"]` |  |\n| 4 | Go to notes list | navigate | `/notes` |  |\n| 5 | Verify note no longer appears | assert_visible | `[data-testid=\"notes-list-item\"]` |  |\n", "parent_ids": ["US-001-03", "EP-001"]}, {"id": "TC-001-08", "type": "test_plan", "path": "test-plans/TC-001-08.md", "content": "---\nid: TC-001-08\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-11T08:45:38Z\nstory: [[US-001-03]]\nepic: [[EP-001]]\nmodule: delete a note they no longer need\n---\n\n# TC-001-08: [edge] the confirmation dialog appears\n\n**Story:** [[US-001-03]]\n**Module:** delete a note they no longer need\n\n## Preconditions\nthe team member clicks Delete\n\n## Expected Result\nclicking Cancel leaves the note unchanged and still in the list\n\n## Acceptance Criteria\nGiven the team member is viewing a note, when they click Delete and confirm the action, then the note is removed and no longer appears in the notes list\nGiven the team member clicks Delete, when the confirmation dialog appears, then clicking Cancel leaves the note unchanged and still in the list\nGiven the note was already deleted by another user, when the team member attempts to delete it again, then the UI shows a 'note no longer exists' message instead of a generic failure\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Open an existing note | navigate | `/notes/1` |  |\n| 2 | Click Delete | click | `[data-testid=\"note-detail-delete-button\"]` |  |\n| 3 | Click Cancel in dialog | click | `[data-testid=\"delete-cancel-button\"]` |  |\n| 4 | Verify note still present in detail/list | assert_visible | `[data-testid=\"note-detail-title\"]` |  |\n", "parent_ids": ["US-001-03", "EP-001"]}, {"id": "TC-001-09", "type": "test_plan", "path": "test-plans/TC-001-09.md", "content": "---\nid: TC-001-09\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-11T08:45:38Z\nstory: [[US-001-03]]\nepic: [[EP-001]]\nmodule: delete a note they no longer need\n---\n\n# TC-001-09: [error] the team member attempts to delete it again\n\n**Story:** [[US-001-03]]\n**Module:** delete a note they no longer need\n\n## Preconditions\nthe note was already deleted by another user\n\n## Expected Result\nthe UI shows a 'note no longer exists' message instead of a generic failure\n\n## Acceptance Criteria\nGiven the team member is viewing a note, when they click Delete and confirm the action, then the note is removed and no longer appears in the notes list\nGiven the team member clicks Delete, when the confirmation dialog appears, then clicking Cancel leaves the note unchanged and still in the list\nGiven the note was already deleted by another user, when the team member attempts to delete it again, then the UI shows a 'note no longer exists' message instead of a generic failure\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Open a note already deleted by another user | navigate | `/notes/1` |  |\n| 2 | Click Delete | click | `[data-testid=\"note-detail-delete-button\"]` |  |\n| 3 | Confirm deletion | click | `[data-testid=\"delete-confirm-button\"]` |  |\n| 4 | Verify 'note no longer exists' message shown | assert_text | `[data-testid=\"note-detail-error-message\"]` | no longer exists |\n", "parent_ids": ["US-001-03", "EP-001"]}, {"id": "TC-001-10", "type": "test_plan", "path": "test-plans/TC-001-10.md", "content": "---\nid: TC-001-10\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-11T08:45:38Z\nstory: [[US-001-04]]\nepic: [[EP-001]]\nmodule: add or remove tags while creating or editing a note\n---\n\n# TC-001-10: they type a tag and press Enter/comma\n\n**Story:** [[US-001-04]]\n**Module:** add or remove tags while creating or editing a note\n\n## Preconditions\nthe team member is in the note form\n\n## Expected Result\nthe tag is added as a visible chip and included on Save\n\n## Acceptance Criteria\nGiven the team member is in the note form, when they type a tag and press Enter/comma, then the tag is added as a visible chip and included on Save\nGiven the team member has added a tag chip, when they click the chip's remove icon, then the tag is removed from the note before saving\nGiven the team member is adding a tag, when they try to add a duplicate of an existing tag on the note, then the duplicate is ignored and only one instance of the tag remains\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Go to new note screen | navigate | `/notes/new` |  |\n| 2 | Type a tag and press Enter | fill | `[data-testid=\"note-form-tag-input\"]` | urgent\n |\n| 3 | Verify tag chip is visible | assert_visible | `[data-testid=\"note-form-tag-chip\"]` |  |\n| 4 | Fill required fields and Save | fill | `[data-testid=\"note-form-title-input\"]` | Tag Test |\n| 5 | Click Save | click | `[data-testid=\"note-form-save-button\"]` |  |\n| 6 | Verify tag included on saved note | assert_text | `[data-testid=\"notes-list-item\"]:first-child` | urgent |\n", "parent_ids": ["US-001-04", "EP-001"]}, {"id": "TC-001-11", "type": "test_plan", "path": "test-plans/TC-001-11.md", "content": "---\nid: TC-001-11\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-11T08:45:38Z\nstory: [[US-001-04]]\nepic: [[EP-001]]\nmodule: add or remove tags while creating or editing a note\n---\n\n# TC-001-11: they click the chip's remove icon\n\n**Story:** [[US-001-04]]\n**Module:** add or remove tags while creating or editing a note\n\n## Preconditions\nthe team member has added a tag chip\n\n## Expected Result\nthe tag is removed from the note before saving\n\n## Acceptance Criteria\nGiven the team member is in the note form, when they type a tag and press Enter/comma, then the tag is added as a visible chip and included on Save\nGiven the team member has added a tag chip, when they click the chip's remove icon, then the tag is removed from the note before saving\nGiven the team member is adding a tag, when they try to add a duplicate of an existing tag on the note, then the duplicate is ignored and only one instance of the tag remains\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Go to note form with an existing tag chip | navigate | `/notes/1/edit` |  |\n| 2 | Click chip remove icon | click | `[data-testid=\"note-form-tag-chip\"] >> [data-testid=\"note-form-tag-remove-icon\"]` |  |\n| 3 | Click Save | click | `[data-testid=\"note-form-save-button\"]` |  |\n| 4 | Verify tag no longer present on note | assert_visible | `[data-testid=\"note-detail-title\"]` |  |\n", "parent_ids": ["US-001-04", "EP-001"]}, {"id": "TC-001-12", "type": "test_plan", "path": "test-plans/TC-001-12.md", "content": "---\nid: TC-001-12\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-11T08:45:38Z\nstory: [[US-001-04]]\nepic: [[EP-001]]\nmodule: add or remove tags while creating or editing a note\n---\n\n# TC-001-12: [edge] they try to add a duplicate of an existing tag on the note\n\n**Story:** [[US-001-04]]\n**Module:** add or remove tags while creating or editing a note\n\n## Preconditions\nthe team member is adding a tag\n\n## Expected Result\nthe duplicate is ignored and only one instance of the tag remains\n\n## Acceptance Criteria\nGiven the team member is in the note form, when they type a tag and press Enter/comma, then the tag is added as a visible chip and included on Save\nGiven the team member has added a tag chip, when they click the chip's remove icon, then the tag is removed from the note before saving\nGiven the team member is adding a tag, when they try to add a duplicate of an existing tag on the note, then the duplicate is ignored and only one instance of the tag remains\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Go to note form with an existing tag 'urgent' | navigate | `/notes/1/edit` |  |\n| 2 | Type the duplicate tag and press Enter | fill | `[data-testid=\"note-form-tag-input\"]` | urgent\n |\n| 3 | Verify only one instance of the tag chip exists | assert_visible | `[data-testid=\"note-form-tag-chip\"]` |  |\n", "parent_ids": ["US-001-04", "EP-001"]}, {"id": "TC-001-13", "type": "test_plan", "path": "test-plans/TC-001-13.md", "content": "---\nid: TC-001-13\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-11T08:45:38Z\nstory: [[US-001-05]]\nepic: [[EP-001]]\nmodule: preview the rendered markdown while writing a note's body\n---\n\n# TC-001-13: they switch to the Preview tab\n\n**Story:** [[US-001-05]]\n**Module:** preview the rendered markdown while writing a note's body\n\n## Preconditions\nthe team member is typing markdown in the body field\n\n## Expected Result\nthe rendered HTML output matches the markdown entered\n\n## Acceptance Criteria\nGiven the team member is typing markdown in the body field, when they switch to the Preview tab, then the rendered HTML output matches the markdown entered\nGiven the team member has entered malformed markdown syntax, when they switch to Preview, then the preview renders the text as plain text/best-effort without crashing the form\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Go to new note screen | navigate | `/notes/new` |  |\n| 2 | Type markdown into the body field | fill | `[data-testid=\"note-form-body-textarea\"]` | # Heading\n**bold text** |\n| 3 | Switch to Preview tab | click | `[data-testid=\"note-form-preview-tab\"]` |  |\n| 4 | Verify rendered HTML matches markdown | assert_text | `[data-testid=\"note-form-preview-content\"]` | Heading |\n", "parent_ids": ["US-001-05", "EP-001"]}, {"id": "TC-001-14", "type": "test_plan", "path": "test-plans/TC-001-14.md", "content": "---\nid: TC-001-14\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-11T08:45:38Z\nstory: [[US-001-05]]\nepic: [[EP-001]]\nmodule: preview the rendered markdown while writing a note's body\n---\n\n# TC-001-14: [edge] they switch to Preview\n\n**Story:** [[US-001-05]]\n**Module:** preview the rendered markdown while writing a note's body\n\n## Preconditions\nthe team member has entered malformed markdown syntax\n\n## Expected Result\nthe preview renders the text as plain text/best-effort without crashing the form\n\n## Acceptance Criteria\nGiven the team member is typing markdown in the body field, when they switch to the Preview tab, then the rendered HTML output matches the markdown entered\nGiven the team member has entered malformed markdown syntax, when they switch to Preview, then the preview renders the text as plain text/best-effort without crashing the form\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Go to new note screen | navigate | `/notes/new` |  |\n| 2 | Type malformed markdown into the body field | fill | `[data-testid=\"note-form-body-textarea\"]` | [unclosed](link **bold |\n| 3 | Switch to Preview tab | click | `[data-testid=\"note-form-preview-tab\"]` |  |\n| 4 | Verify preview renders without crashing the form | assert_visible | `[data-testid=\"note-form-preview-content\"]` |  |\n", "parent_ids": ["US-001-05", "EP-001"]}, {"id": "TC-001-15", "type": "test_plan", "path": "test-plans/TC-001-15.md", "content": "---\nid: TC-001-15\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-11T08:45:38Z\nstory: [[US-001-06]]\nepic: [[EP-001]]\nmodule: discard unsaved changes when leaving the note form\n---\n\n# TC-001-15: they navigate away without saving\n\n**Story:** [[US-001-06]]\n**Module:** discard unsaved changes when leaving the note form\n\n## Preconditions\nthe team member has unsaved edits in the note form\n\n## Expected Result\na confirmation prompt asks whether to discard changes\n\n## Acceptance Criteria\nGiven the team member has unsaved edits in the note form, when they navigate away without saving, then a confirmation prompt asks whether to discard changes\nGiven the confirmation prompt is shown, when the team member confirms discard, then the form closes and no changes are persisted to the note\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Go to new note screen and make unsaved edits | navigate | `/notes/new` |  |\n| 2 | Type into title field without saving | fill | `[data-testid=\"note-form-title-input\"]` | Unsaved Draft |\n| 3 | Attempt to navigate away | click | `[data-testid=\"app-nav-notes-link\"]` |  |\n| 4 | Verify discard confirmation prompt appears | assert_visible | `[data-testid=\"note-form-discard-dialog\"]` |  |\n", "parent_ids": ["US-001-06", "EP-001"]}, {"id": "TC-001-16", "type": "test_plan", "path": "test-plans/TC-001-16.md", "content": "---\nid: TC-001-16\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-11T08:45:38Z\nstory: [[US-001-06]]\nepic: [[EP-001]]\nmodule: discard unsaved changes when leaving the note form\n---\n\n# TC-001-16: [edge] the team member confirms discard\n\n**Story:** [[US-001-06]]\n**Module:** discard unsaved changes when leaving the note form\n\n## Preconditions\nthe confirmation prompt is shown\n\n## Expected Result\nthe form closes and no changes are persisted to the note\n\n## Acceptance Criteria\nGiven the team member has unsaved edits in the note form, when they navigate away without saving, then a confirmation prompt asks whether to discard changes\nGiven the confirmation prompt is shown, when the team member confirms discard, then the form closes and no changes are persisted to the note\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Trigger the discard confirmation prompt | navigate | `/notes/new` |  |\n| 2 | Type into title field without saving | fill | `[data-testid=\"note-form-title-input\"]` | Unsaved Draft |\n| 3 | Attempt to navigate away | click | `[data-testid=\"app-nav-notes-link\"]` |  |\n| 4 | Confirm discard | click | `[data-testid=\"note-form-discard-confirm-button\"]` |  |\n| 5 | Verify form closed and no changes persisted | assert_url | `` | /notes |\n", "parent_ids": ["US-001-06", "EP-001"]}, {"id": "TC-002-01", "type": "test_plan", "path": "test-plans/TC-002-01.md", "content": "---\nid: TC-002-01\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-11T08:45:38Z\nstory: [[US-002-01]]\nepic: [[EP-002]]\nmodule: see the note list ordered newest first when opening the notes view\n---\n\n# TC-002-01: the member opens the notes list\n\n**Story:** [[US-002-01]]\n**Module:** see the note list ordered newest first when opening the notes view\n\n## Preconditions\nthe team has multiple notes with different creation times\n\n## Expected Result\nnotes display with the most recently created or edited note first\n\n## Acceptance Criteria\nGiven the team has multiple notes with different creation times, when the member opens the notes list, then notes display with the most recently created or edited note first\nGiven the team has no notes yet, when the member opens the notes list, then an empty-state message displays instead of a blank or loading screen\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Navigate to notes list with multiple notes present | navigate | `/notes` |  |\n| 2 | Verify first item is the most recently created/edited note | assert_visible | `[data-testid=\"notes-list-item\"]:first-child` |  |\n", "parent_ids": ["US-002-01", "EP-002"]}, {"id": "TC-002-02", "type": "test_plan", "path": "test-plans/TC-002-02.md", "content": "---\nid: TC-002-02\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-11T08:45:38Z\nstory: [[US-002-01]]\nepic: [[EP-002]]\nmodule: see the note list ordered newest first when opening the notes view\n---\n\n# TC-002-02: [edge] the member opens the notes list\n\n**Story:** [[US-002-01]]\n**Module:** see the note list ordered newest first when opening the notes view\n\n## Preconditions\nthe team has no notes yet\n\n## Expected Result\nan empty-state message displays instead of a blank or loading screen\n\n## Acceptance Criteria\nGiven the team has multiple notes with different creation times, when the member opens the notes list, then notes display with the most recently created or edited note first\nGiven the team has no notes yet, when the member opens the notes list, then an empty-state message displays instead of a blank or loading screen\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Navigate to notes list with no notes | navigate | `/notes` |  |\n| 2 | Verify empty-state message displays | assert_visible | `[data-testid=\"notes-list-empty-state\"]` |  |\n", "parent_ids": ["US-002-01", "EP-002"]}, {"id": "TC-002-03", "type": "test_plan", "path": "test-plans/TC-002-03.md", "content": "---\nid: TC-002-03\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-11T08:45:38Z\nstory: [[US-002-02]]\nepic: [[EP-002]]\nmodule: type text into a search box to filter the list by note content\n---\n\n# TC-002-03: they type a search term\n\n**Story:** [[US-002-02]]\n**Module:** type text into a search box to filter the list by note content\n\n## Preconditions\nthe member is on the notes list\n\n## Expected Result\nthe request fires only after a short debounce pause (not on every keystroke) and the list updates to matching notes\n\n## Acceptance Criteria\nGiven the member is on the notes list, when they type a search term, then the request fires only after a short debounce pause (not on every keystroke) and the list updates to matching notes\nGiven the member types a search term matching no notes, when the search completes, then a 'no results' message displays instead of an empty list with no feedback\nGiven the search request to the API fails or times out, when the member has entered a search term, then an error message displays and the previous list state is preserved rather than showing a blank screen\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Go to notes list | navigate | `/notes` |  |\n| 2 | Type a search term | fill | `[data-testid=\"notes-list-search-input\"]` | planning |\n| 3 | Wait for debounce | wait | `` | 500 |\n| 4 | Verify list updates to matching notes | assert_text | `[data-testid=\"notes-list-item\"]:first-child` | planning |\n", "parent_ids": ["US-002-02", "EP-002"]}, {"id": "TC-002-04", "type": "test_plan", "path": "test-plans/TC-002-04.md", "content": "---\nid: TC-002-04\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-11T08:45:38Z\nstory: [[US-002-02]]\nepic: [[EP-002]]\nmodule: type text into a search box to filter the list by note content\n---\n\n# TC-002-04: [edge] the search completes\n\n**Story:** [[US-002-02]]\n**Module:** type text into a search box to filter the list by note content\n\n## Preconditions\nthe member types a search term matching no notes\n\n## Expected Result\na 'no results' message displays instead of an empty list with no feedback\n\n## Acceptance Criteria\nGiven the member is on the notes list, when they type a search term, then the request fires only after a short debounce pause (not on every keystroke) and the list updates to matching notes\nGiven the member types a search term matching no notes, when the search completes, then a 'no results' message displays instead of an empty list with no feedback\nGiven the search request to the API fails or times out, when the member has entered a search term, then an error message displays and the previous list state is preserved rather than showing a blank screen\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Go to notes list | navigate | `/notes` |  |\n| 2 | Type a search term matching no notes | fill | `[data-testid=\"notes-list-search-input\"]` | zzznomatch |\n| 3 | Wait for debounce and search completion | wait | `` | 500 |\n| 4 | Verify 'no results' message displays | assert_visible | `[data-testid=\"notes-list-no-results\"]` |  |\n", "parent_ids": ["US-002-02", "EP-002"]}, {"id": "TC-002-05", "type": "test_plan", "path": "test-plans/TC-002-05.md", "content": "---\nid: TC-002-05\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-11T08:45:38Z\nstory: [[US-002-02]]\nepic: [[EP-002]]\nmodule: type text into a search box to filter the list by note content\n---\n\n# TC-002-05: [error] the member has entered a search term\n\n**Story:** [[US-002-02]]\n**Module:** type text into a search box to filter the list by note content\n\n## Preconditions\nthe search request to the API fails or times out\n\n## Expected Result\nan error message displays and the previous list state is preserved rather than showing a blank screen\n\n## Acceptance Criteria\nGiven the member is on the notes list, when they type a search term, then the request fires only after a short debounce pause (not on every keystroke) and the list updates to matching notes\nGiven the member types a search term matching no notes, when the search completes, then a 'no results' message displays instead of an empty list with no feedback\nGiven the search request to the API fails or times out, when the member has entered a search term, then an error message displays and the previous list state is preserved rather than showing a blank screen\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Go to notes list with search API set to fail | navigate | `/notes` |  |\n| 2 | Enter a search term | fill | `[data-testid=\"notes-list-search-input\"]` | planning |\n| 3 | Wait for failed request | wait | `` | 500 |\n| 4 | Verify error message displays and previous list preserved | assert_visible | `[data-testid=\"notes-list-error-message\"]` |  |\n", "parent_ids": ["US-002-02", "EP-002"]}, {"id": "TC-002-06", "type": "test_plan", "path": "test-plans/TC-002-06.md", "content": "---\nid: TC-002-06\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-11T08:45:38Z\nstory: [[US-002-03]]\nepic: [[EP-002]]\nmodule: select a tag from a filter control to show only notes with that tag\n---\n\n# TC-002-06: the member selects a tag from the filter\n\n**Story:** [[US-002-03]]\n**Module:** select a tag from a filter control to show only notes with that tag\n\n## Preconditions\nnotes exist with various tags\n\n## Expected Result\nthe list updates to show only notes carrying that tag, newest first\n\n## Acceptance Criteria\nGiven notes exist with various tags, when the member selects a tag from the filter, then the list updates to show only notes carrying that tag, newest first\nGiven a tag filter is active, when the member clears the tag filter, then the full newest-first list returns without needing a page reload\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Go to notes list | navigate | `/notes` |  |\n| 2 | Select a tag from the filter control | select | `[data-testid=\"notes-list-tag-filter-select\"]` | planning |\n| 3 | Verify list updates to only notes with that tag, newest first | assert_text | `[data-testid=\"notes-list-item\"]:first-child` | planning |\n", "parent_ids": ["US-002-03", "EP-002"]}, {"id": "TC-002-07", "type": "test_plan", "path": "test-plans/TC-002-07.md", "content": "---\nid: TC-002-07\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-11T08:45:38Z\nstory: [[US-002-03]]\nepic: [[EP-002]]\nmodule: select a tag from a filter control to show only notes with that tag\n---\n\n# TC-002-07: [edge] the member clears the tag filter\n\n**Story:** [[US-002-03]]\n**Module:** select a tag from a filter control to show only notes with that tag\n\n## Preconditions\na tag filter is active\n\n## Expected Result\nthe full newest-first list returns without needing a page reload\n\n## Acceptance Criteria\nGiven notes exist with various tags, when the member selects a tag from the filter, then the list updates to show only notes carrying that tag, newest first\nGiven a tag filter is active, when the member clears the tag filter, then the full newest-first list returns without needing a page reload\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Apply a tag filter | select | `[data-testid=\"notes-list-tag-filter-select\"]` | planning |\n| 2 | Clear the tag filter | select | `[data-testid=\"notes-list-tag-filter-select\"]` |  |\n| 3 | Verify full newest-first list returns without reload | assert_visible | `[data-testid=\"notes-list-item\"]:first-child` |  |\n", "parent_ids": ["US-002-03", "EP-002"]}, {"id": "TC-002-08", "type": "test_plan", "path": "test-plans/TC-002-08.md", "content": "---\nid: TC-002-08\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-11T08:45:38Z\nstory: [[US-002-04]]\nepic: [[EP-002]]\nmodule: load additional notes via pagination or infinite scroll instead of all notes at once\n---\n\n# TC-002-08: the member scrolls to the end of the current page or clicks 'load more'\n\n**Story:** [[US-002-04]]\n**Module:** load additional notes via pagination or infinite scroll instead of all notes at once\n\n## Preconditions\nmore notes exist than fit on one page\n\n## Expected Result\nthe next page of notes loads and appends in newest-first order\n\n## Acceptance Criteria\nGiven more notes exist than fit on one page, when the member scrolls to the end of the current page or clicks 'load more', then the next page of notes loads and appends in newest-first order\nGiven the member has reached the last page of results, when they scroll further or click 'load more', then no further requests are made and an 'end of list' indicator shows instead of a repeated spinner\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Go to notes list with more notes than fit one page | navigate | `/notes` |  |\n| 2 | Click 'load more' | click | `[data-testid=\"notes-list-load-more-button\"]` |  |\n| 3 | Verify next page appended in newest-first order | assert_visible | `[data-testid=\"notes-list-item\"]:nth-child(11)` |  |\n", "parent_ids": ["US-002-04", "EP-002"]}, {"id": "TC-002-09", "type": "test_plan", "path": "test-plans/TC-002-09.md", "content": "---\nid: TC-002-09\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-11T08:45:38Z\nstory: [[US-002-04]]\nepic: [[EP-002]]\nmodule: load additional notes via pagination or infinite scroll instead of all notes at once\n---\n\n# TC-002-09: [edge] they scroll further or click 'load more'\n\n**Story:** [[US-002-04]]\n**Module:** load additional notes via pagination or infinite scroll instead of all notes at once\n\n## Preconditions\nthe member has reached the last page of results\n\n## Expected Result\nno further requests are made and an 'end of list' indicator shows instead of a repeated spinner\n\n## Acceptance Criteria\nGiven more notes exist than fit on one page, when the member scrolls to the end of the current page or clicks 'load more', then the next page of notes loads and appends in newest-first order\nGiven the member has reached the last page of results, when they scroll further or click 'load more', then no further requests are made and an 'end of list' indicator shows instead of a repeated spinner\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Go to notes list already on last page of results | navigate | `/notes` |  |\n| 2 | Click 'load more' again | click | `[data-testid=\"notes-list-load-more-button\"]` |  |\n| 3 | Verify 'end of list' indicator shows instead of a spinner | assert_visible | `[data-testid=\"notes-list-end-indicator\"]` |  |\n", "parent_ids": ["US-002-04", "EP-002"]}, {"id": "TC-002-10", "type": "test_plan", "path": "test-plans/TC-002-10.md", "content": "---\nid: TC-002-10\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-11T08:45:38Z\nstory: [[US-002-05]]\nepic: [[EP-002]]\nmodule: apply a text search query together with a selected tag filter at the same time\n---\n\n# TC-002-10: both filters are active\n\n**Story:** [[US-002-05]]\n**Module:** apply a text search query together with a selected tag filter at the same time\n\n## Preconditions\nthe member has entered a search term and selected a tag\n\n## Expected Result\nthe list shows only notes matching both the text and the tag, newest first\n\n## Acceptance Criteria\nGiven the member has entered a search term and selected a tag, when both filters are active, then the list shows only notes matching both the text and the tag, newest first\nGiven both filters are active and match no notes, when the combined filter returns zero results, then a 'no results' message displays referencing both active filters\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Go to notes list | navigate | `/notes` |  |\n| 2 | Enter a search term | fill | `[data-testid=\"notes-list-search-input\"]` | sprint |\n| 3 | Select a tag filter | select | `[data-testid=\"notes-list-tag-filter-select\"]` | planning |\n| 4 | Wait for debounce | wait | `` | 500 |\n| 5 | Verify list shows notes matching both filters, newest first | assert_text | `[data-testid=\"notes-list-item\"]:first-child` | sprint |\n", "parent_ids": ["US-002-05", "EP-002"]}, {"id": "TC-002-11", "type": "test_plan", "path": "test-plans/TC-002-11.md", "content": "---\nid: TC-002-11\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-11T08:45:38Z\nstory: [[US-002-05]]\nepic: [[EP-002]]\nmodule: apply a text search query together with a selected tag filter at the same time\n---\n\n# TC-002-11: [edge] the combined filter returns zero results\n\n**Story:** [[US-002-05]]\n**Module:** apply a text search query together with a selected tag filter at the same time\n\n## Preconditions\nboth filters are active and match no notes\n\n## Expected Result\na 'no results' message displays referencing both active filters\n\n## Acceptance Criteria\nGiven the member has entered a search term and selected a tag, when both filters are active, then the list shows only notes matching both the text and the tag, newest first\nGiven both filters are active and match no notes, when the combined filter returns zero results, then a 'no results' message displays referencing both active filters\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Enter a search term with no matches for the selected tag | fill | `[data-testid=\"notes-list-search-input\"]` | zzznomatch |\n| 2 | Select a tag filter | select | `[data-testid=\"notes-list-tag-filter-select\"]` | planning |\n| 3 | Wait for debounce | wait | `` | 500 |\n| 4 | Verify 'no results' message references both active filters | assert_text | `[data-testid=\"notes-list-no-results\"]` | planning |\n", "parent_ids": ["US-002-05", "EP-002"]}, {"id": "TC-002-12", "type": "test_plan", "path": "test-plans/TC-002-12.md", "content": "---\nid: TC-002-12\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-11T08:45:38Z\nstory: [[US-002-06]]\nepic: [[EP-002]]\nmodule: see a lightweight loading indicator while the list is refreshing\n---\n\n# TC-002-12: the request is in flight\n\n**Story:** [[US-002-06]]\n**Module:** see a lightweight loading indicator while the list is refreshing\n\n## Preconditions\na search or tag filter is applied\n\n## Expected Result\na loading indicator shows and disappears once results render\n\n## Acceptance Criteria\nGiven a search or tag filter is applied, when the request is in flight, then a loading indicator shows and disappears once results render\nGiven the member changes the search text again before the previous request finishes, when a newer request is issued, then only the result of the latest request is rendered, discarding any stale in-flight response\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Go to notes list | navigate | `/notes` |  |\n| 2 | Apply a search filter | fill | `[data-testid=\"notes-list-search-input\"]` | planning |\n| 3 | Verify loading indicator shows while request is in flight | assert_visible | `[data-testid=\"notes-list-loading-indicator\"]` |  |\n| 4 | Verify loading indicator disappears once results render | assert_visible | `[data-testid=\"notes-list-item\"]:first-child` |  |\n", "parent_ids": ["US-002-06", "EP-002"]}, {"id": "TC-002-13", "type": "test_plan", "path": "test-plans/TC-002-13.md", "content": "---\nid: TC-002-13\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-11T08:45:38Z\nstory: [[US-002-06]]\nepic: [[EP-002]]\nmodule: see a lightweight loading indicator while the list is refreshing\n---\n\n# TC-002-13: [edge] a newer request is issued\n\n**Story:** [[US-002-06]]\n**Module:** see a lightweight loading indicator while the list is refreshing\n\n## Preconditions\nthe member changes the search text again before the previous request finishes\n\n## Expected Result\nonly the result of the latest request is rendered, discarding any stale in-flight response\n\n## Acceptance Criteria\nGiven a search or tag filter is applied, when the request is in flight, then a loading indicator shows and disappears once results render\nGiven the member changes the search text again before the previous request finishes, when a newer request is issued, then only the result of the latest request is rendered, discarding any stale in-flight response\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Type an initial search term | fill | `[data-testid=\"notes-list-search-input\"]` | plan |\n| 2 | Change search text again before previous request finishes | fill | `[data-testid=\"notes-list-search-input\"]` | planning |\n| 3 | Wait for debounce and completion | wait | `` | 700 |\n| 4 | Verify only the latest search's results are rendered | assert_text | `[data-testid=\"notes-list-item\"]:first-child` | planning |\n", "parent_ids": ["US-002-06", "EP-002"]}, {"id": "TC-003-01", "type": "test_plan", "path": "test-plans/TC-003-01.md", "content": "---\nid: TC-003-01\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-11T08:45:38Z\nstory: [[US-003-01]]\nepic: [[EP-003]]\nmodule: archive a note directly from the notes list or detail view\n---\n\n# TC-003-01: they select Archive and confirm\n\n**Story:** [[US-003-01]]\n**Module:** archive a note directly from the notes list or detail view\n\n## Preconditions\na lead is viewing an active note in the list or detail view\n\n## Expected Result\nthe note is removed from the active list immediately\n\n## Acceptance Criteria\nGiven a lead is viewing an active note in the list or detail view, when they select Archive and confirm, then the note is removed from the active list immediately\nGiven a logged-in team member (non-lead) is viewing the notes list, when they view a note's controls, then no Archive button is shown or it is disabled\nGiven a lead confirms archiving a note, when the archive request to the API fails, then the note remains visible in the active list and an error message is shown\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Log in as a team lead and open an active note | navigate | `/notes/1` |  |\n| 2 | Click Archive | click | `[data-testid=\"note-detail-archive-button\"]` |  |\n| 3 | Confirm archive | click | `[data-testid=\"archive-confirm-button\"]` |  |\n| 4 | Go to active notes list | navigate | `/notes` |  |\n| 5 | Verify note removed from active list | assert_visible | `[data-testid=\"notes-list-item\"]` |  |\n", "parent_ids": ["US-003-01", "EP-003"]}, {"id": "TC-003-02", "type": "test_plan", "path": "test-plans/TC-003-02.md", "content": "---\nid: TC-003-02\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-11T08:45:38Z\nstory: [[US-003-01]]\nepic: [[EP-003]]\nmodule: archive a note directly from the notes list or detail view\n---\n\n# TC-003-02: [edge] they view a note's controls\n\n**Story:** [[US-003-01]]\n**Module:** archive a note directly from the notes list or detail view\n\n## Preconditions\na logged-in team member (non-lead) is viewing the notes list\n\n## Expected Result\nno Archive button is shown or it is disabled\n\n## Acceptance Criteria\nGiven a lead is viewing an active note in the list or detail view, when they select Archive and confirm, then the note is removed from the active list immediately\nGiven a logged-in team member (non-lead) is viewing the notes list, when they view a note's controls, then no Archive button is shown or it is disabled\nGiven a lead confirms archiving a note, when the archive request to the API fails, then the note remains visible in the active list and an error message is shown\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Log in as a team member (non-lead) | navigate | `/notes/1` |  |\n| 2 | Verify no Archive button is shown or it is disabled | assert_visible | `[data-testid=\"note-detail-archive-button\"]` |  |\n", "parent_ids": ["US-003-01", "EP-003"]}, {"id": "TC-003-03", "type": "test_plan", "path": "test-plans/TC-003-03.md", "content": "---\nid: TC-003-03\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-11T08:45:38Z\nstory: [[US-003-01]]\nepic: [[EP-003]]\nmodule: archive a note directly from the notes list or detail view\n---\n\n# TC-003-03: [error] the archive request to the API fails\n\n**Story:** [[US-003-01]]\n**Module:** archive a note directly from the notes list or detail view\n\n## Preconditions\na lead confirms archiving a note\n\n## Expected Result\nthe note remains visible in the active list and an error message is shown\n\n## Acceptance Criteria\nGiven a lead is viewing an active note in the list or detail view, when they select Archive and confirm, then the note is removed from the active list immediately\nGiven a logged-in team member (non-lead) is viewing the notes list, when they view a note's controls, then no Archive button is shown or it is disabled\nGiven a lead confirms archiving a note, when the archive request to the API fails, then the note remains visible in the active list and an error message is shown\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Open an active note as lead with archive API set to fail | navigate | `/notes/1` |  |\n| 2 | Click Archive | click | `[data-testid=\"note-detail-archive-button\"]` |  |\n| 3 | Confirm archive | click | `[data-testid=\"archive-confirm-button\"]` |  |\n| 4 | Verify note still visible in active list and error shown | assert_visible | `[data-testid=\"note-detail-error-message\"]` |  |\n", "parent_ids": ["US-003-01", "EP-003"]}, {"id": "TC-003-04", "type": "test_plan", "path": "test-plans/TC-003-04.md", "content": "---\nid: TC-003-04\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-11T08:45:38Z\nstory: [[US-003-02]]\nepic: [[EP-003]]\nmodule: view a separate list of archived notes\n---\n\n# TC-003-04: they navigate to the archived notes view\n\n**Story:** [[US-003-02]]\n**Module:** view a separate list of archived notes\n\n## Preconditions\na lead is logged in\n\n## Expected Result\nthey see all archived notes listed newest first\n\n## Acceptance Criteria\nGiven a lead is logged in, when they navigate to the archived notes view, then they see all archived notes listed newest first\nGiven no notes have been archived yet, when a lead opens the archived notes view, then an empty-state message is shown instead of a blank list\nGiven a team member (non-lead) is logged in, when they try to access the archived notes view, then the view/navigation link is hidden or access is denied\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Log in as a team lead | navigate | `/notes/archived` |  |\n| 2 | Verify archived notes are listed newest first | assert_visible | `[data-testid=\"archived-notes-item\"]:first-child` |  |\n", "parent_ids": ["US-003-02", "EP-003"]}, {"id": "TC-003-05", "type": "test_plan", "path": "test-plans/TC-003-05.md", "content": "---\nid: TC-003-05\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-11T08:45:38Z\nstory: [[US-003-02]]\nepic: [[EP-003]]\nmodule: view a separate list of archived notes\n---\n\n# TC-003-05: [edge] a lead opens the archived notes view\n\n**Story:** [[US-003-02]]\n**Module:** view a separate list of archived notes\n\n## Preconditions\nno notes have been archived yet\n\n## Expected Result\nan empty-state message is shown instead of a blank list\n\n## Acceptance Criteria\nGiven a lead is logged in, when they navigate to the archived notes view, then they see all archived notes listed newest first\nGiven no notes have been archived yet, when a lead opens the archived notes view, then an empty-state message is shown instead of a blank list\nGiven a team member (non-lead) is logged in, when they try to access the archived notes view, then the view/navigation link is hidden or access is denied\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Log in as a lead where no notes have been archived yet | navigate | `/notes/archived` |  |\n| 2 | Verify empty-state message is shown | assert_visible | `[data-testid=\"archived-notes-empty-state\"]` |  |\n", "parent_ids": ["US-003-02", "EP-003"]}, {"id": "TC-003-06", "type": "test_plan", "path": "test-plans/TC-003-06.md", "content": "---\nid: TC-003-06\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-11T08:45:38Z\nstory: [[US-003-02]]\nepic: [[EP-003]]\nmodule: view a separate list of archived notes\n---\n\n# TC-003-06: [error] they try to access the archived notes view\n\n**Story:** [[US-003-02]]\n**Module:** view a separate list of archived notes\n\n## Preconditions\na team member (non-lead) is logged in\n\n## Expected Result\nthe view/navigation link is hidden or access is denied\n\n## Acceptance Criteria\nGiven a lead is logged in, when they navigate to the archived notes view, then they see all archived notes listed newest first\nGiven no notes have been archived yet, when a lead opens the archived notes view, then an empty-state message is shown instead of a blank list\nGiven a team member (non-lead) is logged in, when they try to access the archived notes view, then the view/navigation link is hidden or access is denied\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Log in as a team member (non-lead) | navigate | `/notes` |  |\n| 2 | Verify archived notes nav link is hidden | assert_visible | `[data-testid=\"app-nav-archived-notes-link\"]` |  |\n| 3 | Attempt direct navigation to archived notes view | navigate | `/notes/archived` |  |\n| 4 | Verify access is denied | assert_url | `` | /notes |\n", "parent_ids": ["US-003-02", "EP-003"]}, {"id": "TC-003-07", "type": "test_plan", "path": "test-plans/TC-003-07.md", "content": "---\nid: TC-003-07\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-11T08:45:38Z\nstory: [[US-003-03]]\nepic: [[EP-003]]\nmodule: restore an archived note back to the active list\n---\n\n# TC-003-07: they select Restore on a note\n\n**Story:** [[US-003-03]]\n**Module:** restore an archived note back to the active list\n\n## Preconditions\na lead is viewing the archived notes list\n\n## Expected Result\nthe note disappears from the archived list and reappears in the active list\n\n## Acceptance Criteria\nGiven a lead is viewing the archived notes list, when they select Restore on a note, then the note disappears from the archived list and reappears in the active list\nGiven a lead restores a note, when the restore completes, then the note's position in the active list reflects its original creation/update order (newest first)\nGiven a lead triggers Restore on an archived note, when the restore request to the API fails, then the note stays in the archived list and an error message is shown\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Log in as lead and go to archived notes list | navigate | `/notes/archived` |  |\n| 2 | Click Restore on a note | click | `[data-testid=\"archived-notes-restore-button\"]` |  |\n| 3 | Verify note disappears from archived list | assert_visible | `[data-testid=\"archived-notes-item\"]` |  |\n| 4 | Go to active notes list | navigate | `/notes` |  |\n| 5 | Verify note reappears in active list | assert_visible | `[data-testid=\"notes-list-item\"]` |  |\n", "parent_ids": ["US-003-03", "EP-003"]}, {"id": "TC-003-08", "type": "test_plan", "path": "test-plans/TC-003-08.md", "content": "---\nid: TC-003-08\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-11T08:45:38Z\nstory: [[US-003-03]]\nepic: [[EP-003]]\nmodule: restore an archived note back to the active list\n---\n\n# TC-003-08: [edge] the restore completes\n\n**Story:** [[US-003-03]]\n**Module:** restore an archived note back to the active list\n\n## Preconditions\na lead restores a note\n\n## Expected Result\nthe note's position in the active list reflects its original creation/update order (newest first)\n\n## Acceptance Criteria\nGiven a lead is viewing the archived notes list, when they select Restore on a note, then the note disappears from the archived list and reappears in the active list\nGiven a lead restores a note, when the restore completes, then the note's position in the active list reflects its original creation/update order (newest first)\nGiven a lead triggers Restore on an archived note, when the restore request to the API fails, then the note stays in the archived list and an error message is shown\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Restore a note from the archived list | click | `[data-testid=\"archived-notes-restore-button\"]` |  |\n| 2 | Go to active notes list | navigate | `/notes` |  |\n| 3 | Verify restored note position reflects newest-first order | assert_visible | `[data-testid=\"notes-list-item\"]` |  |\n", "parent_ids": ["US-003-03", "EP-003"]}, {"id": "TC-003-09", "type": "test_plan", "path": "test-plans/TC-003-09.md", "content": "---\nid: TC-003-09\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-11T08:45:38Z\nstory: [[US-003-03]]\nepic: [[EP-003]]\nmodule: restore an archived note back to the active list\n---\n\n# TC-003-09: [error] the restore request to the API fails\n\n**Story:** [[US-003-03]]\n**Module:** restore an archived note back to the active list\n\n## Preconditions\na lead triggers Restore on an archived note\n\n## Expected Result\nthe note stays in the archived list and an error message is shown\n\n## Acceptance Criteria\nGiven a lead is viewing the archived notes list, when they select Restore on a note, then the note disappears from the archived list and reappears in the active list\nGiven a lead restores a note, when the restore completes, then the note's position in the active list reflects its original creation/update order (newest first)\nGiven a lead triggers Restore on an archived note, when the restore request to the API fails, then the note stays in the archived list and an error message is shown\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Go to archived notes list with restore API set to fail | navigate | `/notes/archived` |  |\n| 2 | Click Restore | click | `[data-testid=\"archived-notes-restore-button\"]` |  |\n| 3 | Verify note stays in archived list and error message shown | assert_visible | `[data-testid=\"archived-notes-error-message\"]` |  |\n", "parent_ids": ["US-003-03", "EP-003"]}, {"id": "TC-003-10", "type": "test_plan", "path": "test-plans/TC-003-10.md", "content": "---\nid: TC-003-10\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-11T08:45:38Z\nstory: [[US-003-04]]\nepic: [[EP-003]]\nmodule: see only active notes in the main list and search results\n---\n\n# TC-003-10: a team member opens the notes list\n\n**Story:** [[US-003-04]]\n**Module:** see only active notes in the main list and search results\n\n## Preconditions\nsome notes have been archived by a lead\n\n## Expected Result\narchived notes are not displayed\n\n## Acceptance Criteria\nGiven some notes have been archived by a lead, when a team member opens the notes list, then archived notes are not displayed\nGiven an archived note matches a search term or tag filter, when a team member searches or filters, then the archived note is excluded from the results\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Log in as team member where some notes are archived | navigate | `/notes` |  |\n| 2 | Verify archived notes are not displayed | assert_visible | `[data-testid=\"notes-list-item\"]` |  |\n", "parent_ids": ["US-003-04", "EP-003"]}, {"id": "TC-003-11", "type": "test_plan", "path": "test-plans/TC-003-11.md", "content": "---\nid: TC-003-11\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-11T08:45:38Z\nstory: [[US-003-04]]\nepic: [[EP-003]]\nmodule: see only active notes in the main list and search results\n---\n\n# TC-003-11: [edge] a team member searches or filters\n\n**Story:** [[US-003-04]]\n**Module:** see only active notes in the main list and search results\n\n## Preconditions\nan archived note matches a search term or tag filter\n\n## Expected Result\nthe archived note is excluded from the results\n\n## Acceptance Criteria\nGiven some notes have been archived by a lead, when a team member opens the notes list, then archived notes are not displayed\nGiven an archived note matches a search term or tag filter, when a team member searches or filters, then the archived note is excluded from the results\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Search for a term matching an archived note | fill | `[data-testid=\"notes-list-search-input\"]` | archivedterm |\n| 2 | Wait for debounce | wait | `` | 500 |\n| 3 | Verify archived note excluded from results | assert_visible | `[data-testid=\"notes-list-no-results\"]` |  |\n", "parent_ids": ["US-003-04", "EP-003"]}, {"id": "TC-003-12", "type": "test_plan", "path": "test-plans/TC-003-12.md", "content": "---\nid: TC-003-12\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-11T08:45:38Z\nstory: [[US-003-05]]\nepic: [[EP-003]]\nmodule: confirm an archive action before it is applied\n---\n\n# TC-003-12: a confirmation dialog appears and they confirm\n\n**Story:** [[US-003-05]]\n**Module:** confirm an archive action before it is applied\n\n## Preconditions\na lead selects Archive on a note\n\n## Expected Result\nthe note is archived and removed from the active list\n\n## Acceptance Criteria\nGiven a lead selects Archive on a note, when a confirmation dialog appears and they confirm, then the note is archived and removed from the active list\nGiven a lead selects Archive on a note, when the confirmation dialog appears and they cancel, then the note remains unchanged in the active list\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Log in as lead and select Archive on a note | click | `[data-testid=\"note-detail-archive-button\"]` |  |\n| 2 | Verify confirmation dialog appears | assert_visible | `[data-testid=\"archive-confirm-dialog\"]` |  |\n| 3 | Confirm the archive | click | `[data-testid=\"archive-confirm-button\"]` |  |\n| 4 | Verify note removed from active list | assert_visible | `[data-testid=\"notes-list-item\"]` |  |\n", "parent_ids": ["US-003-05", "EP-003"]}, {"id": "TC-003-13", "type": "test_plan", "path": "test-plans/TC-003-13.md", "content": "---\nid: TC-003-13\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-11T08:45:38Z\nstory: [[US-003-05]]\nepic: [[EP-003]]\nmodule: confirm an archive action before it is applied\n---\n\n# TC-003-13: [edge] the confirmation dialog appears and they cancel\n\n**Story:** [[US-003-05]]\n**Module:** confirm an archive action before it is applied\n\n## Preconditions\na lead selects Archive on a note\n\n## Expected Result\nthe note remains unchanged in the active list\n\n## Acceptance Criteria\nGiven a lead selects Archive on a note, when a confirmation dialog appears and they confirm, then the note is archived and removed from the active list\nGiven a lead selects Archive on a note, when the confirmation dialog appears and they cancel, then the note remains unchanged in the active list\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Select Archive on a note | click | `[data-testid=\"note-detail-archive-button\"]` |  |\n| 2 | Cancel the confirmation dialog | click | `[data-testid=\"archive-cancel-button\"]` |  |\n| 3 | Verify note remains unchanged in active list | assert_visible | `[data-testid=\"note-detail-title\"]` |  |\n", "parent_ids": ["US-003-05", "EP-003"]}, {"id": "TC-004-01", "type": "test_plan", "path": "test-plans/TC-004-01.md", "content": "---\nid: TC-004-01\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-11T08:45:38Z\nstory: [[US-004-01]]\nepic: [[EP-004]]\nmodule: log in with their email and password\n---\n\n# TC-004-01: they submit a correct email and password\n\n**Story:** [[US-004-01]]\n**Module:** log in with their email and password\n\n## Preconditions\na registered user on the login screen\n\n## Expected Result\nthey are authenticated and redirected to the notes list\n\n## Acceptance Criteria\nGiven a registered user on the login screen, when they submit a correct email and password, then they are authenticated and redirected to the notes list\nGiven a user on the login screen, when they submit an incorrect email or password, then an inline error message is shown and no session is created\nGiven a user on the login screen, when they submit the form with an empty email or password field, then submission is blocked and a required-field validation message is shown\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Navigate to login screen | navigate | `/login` |  |\n| 2 | Enter correct email | fill | `[data-testid=\"login-email-input\"]` | member@testmail.com |\n| 3 | Enter correct password | fill | `[data-testid=\"login-password-input\"]` | Password123! |\n| 4 | Submit login | click | `[data-testid=\"login-submit-button\"]` |  |\n| 5 | Verify redirected to notes list | assert_url | `` | /notes |\n", "parent_ids": ["US-004-01", "EP-004"]}, {"id": "TC-004-02", "type": "test_plan", "path": "test-plans/TC-004-02.md", "content": "---\nid: TC-004-02\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-11T08:45:38Z\nstory: [[US-004-01]]\nepic: [[EP-004]]\nmodule: log in with their email and password\n---\n\n# TC-004-02: [error] they submit an incorrect email or password\n\n**Story:** [[US-004-01]]\n**Module:** log in with their email and password\n\n## Preconditions\na user on the login screen\n\n## Expected Result\nan inline error message is shown and no session is created\n\n## Acceptance Criteria\nGiven a registered user on the login screen, when they submit a correct email and password, then they are authenticated and redirected to the notes list\nGiven a user on the login screen, when they submit an incorrect email or password, then an inline error message is shown and no session is created\nGiven a user on the login screen, when they submit the form with an empty email or password field, then submission is blocked and a required-field validation message is shown\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Navigate to login screen | navigate | `/login` |  |\n| 2 | Enter incorrect password | fill | `[data-testid=\"login-password-input\"]` | WrongPass1! |\n| 3 | Enter email | fill | `[data-testid=\"login-email-input\"]` | member@testmail.com |\n| 4 | Submit login | click | `[data-testid=\"login-submit-button\"]` |  |\n| 5 | Verify inline error shown and no redirect | assert_visible | `[data-testid=\"login-error-message\"]` |  |\n", "parent_ids": ["US-004-01", "EP-004"]}, {"id": "TC-004-03", "type": "test_plan", "path": "test-plans/TC-004-03.md", "content": "---\nid: TC-004-03\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-11T08:45:38Z\nstory: [[US-004-01]]\nepic: [[EP-004]]\nmodule: log in with their email and password\n---\n\n# TC-004-03: [edge] they submit the form with an empty email or password field\n\n**Story:** [[US-004-01]]\n**Module:** log in with their email and password\n\n## Preconditions\na user on the login screen\n\n## Expected Result\nsubmission is blocked and a required-field validation message is shown\n\n## Acceptance Criteria\nGiven a registered user on the login screen, when they submit a correct email and password, then they are authenticated and redirected to the notes list\nGiven a user on the login screen, when they submit an incorrect email or password, then an inline error message is shown and no session is created\nGiven a user on the login screen, when they submit the form with an empty email or password field, then submission is blocked and a required-field validation message is shown\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Navigate to login screen | navigate | `/login` |  |\n| 2 | Leave password empty, enter email | fill | `[data-testid=\"login-email-input\"]` | member@testmail.com |\n| 3 | Submit login | click | `[data-testid=\"login-submit-button\"]` |  |\n| 4 | Verify required-field validation message shown | assert_visible | `[data-testid=\"login-password-error\"]` |  |\n", "parent_ids": ["US-004-01", "EP-004"]}, {"id": "TC-004-04", "type": "test_plan", "path": "test-plans/TC-004-04.md", "content": "---\nid: TC-004-04\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-11T08:45:38Z\nstory: [[US-004-02]]\nepic: [[EP-004]]\nmodule: have their session checked before viewing any notes page\n---\n\n# TC-004-04: they navigate directly to the notes list URL\n\n**Story:** [[US-004-02]]\n**Module:** have their session checked before viewing any notes page\n\n## Preconditions\na user with a valid active session\n\n## Expected Result\nthe notes list is displayed without re-login\n\n## Acceptance Criteria\nGiven a user with a valid active session, when they navigate directly to the notes list URL, then the notes list is displayed without re-login\nGiven a user with no session or an expired session, when they navigate directly to any notes page URL, then they are redirected to the login screen\nGiven a user who is already logged in, when they navigate to the login page URL, then they are redirected to the notes list instead of seeing the login form\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Log in to establish a valid session | navigate | `/login` |  |\n| 2 | Enter email | fill | `[data-testid=\"login-email-input\"]` | member@testmail.com |\n| 3 | Enter password and submit | fill | `[data-testid=\"login-password-input\"]` | Password123! |\n| 4 | Submit login | click | `[data-testid=\"login-submit-button\"]` |  |\n| 5 | Navigate directly to notes list URL | navigate | `/notes` |  |\n| 6 | Verify notes list displayed without re-login | assert_visible | `[data-testid=\"notes-list-item\"]` |  |\n", "parent_ids": ["US-004-02", "EP-004"]}, {"id": "TC-004-05", "type": "test_plan", "path": "test-plans/TC-004-05.md", "content": "---\nid: TC-004-05\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-11T08:45:38Z\nstory: [[US-004-02]]\nepic: [[EP-004]]\nmodule: have their session checked before viewing any notes page\n---\n\n# TC-004-05: [error] they navigate directly to any notes page URL\n\n**Story:** [[US-004-02]]\n**Module:** have their session checked before viewing any notes page\n\n## Preconditions\na user with no session or an expired session\n\n## Expected Result\nthey are redirected to the login screen\n\n## Acceptance Criteria\nGiven a user with a valid active session, when they navigate directly to the notes list URL, then the notes list is displayed without re-login\nGiven a user with no session or an expired session, when they navigate directly to any notes page URL, then they are redirected to the login screen\nGiven a user who is already logged in, when they navigate to the login page URL, then they are redirected to the notes list instead of seeing the login form\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Clear any session and navigate directly to a protected notes URL | navigate | `/notes` |  |\n| 2 | Verify redirected to login screen | assert_url | `` | /login |\n", "parent_ids": ["US-004-02", "EP-004"]}, {"id": "TC-004-06", "type": "test_plan", "path": "test-plans/TC-004-06.md", "content": "---\nid: TC-004-06\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-11T08:45:38Z\nstory: [[US-004-02]]\nepic: [[EP-004]]\nmodule: have their session checked before viewing any notes page\n---\n\n# TC-004-06: [edge] they navigate to the login page URL\n\n**Story:** [[US-004-02]]\n**Module:** have their session checked before viewing any notes page\n\n## Preconditions\na user who is already logged in\n\n## Expected Result\nthey are redirected to the notes list instead of seeing the login form\n\n## Acceptance Criteria\nGiven a user with a valid active session, when they navigate directly to the notes list URL, then the notes list is displayed without re-login\nGiven a user with no session or an expired session, when they navigate directly to any notes page URL, then they are redirected to the login screen\nGiven a user who is already logged in, when they navigate to the login page URL, then they are redirected to the notes list instead of seeing the login form\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Log in to establish a valid session | navigate | `/login` |  |\n| 2 | Enter email and password and submit | fill | `[data-testid=\"login-email-input\"]` | member@testmail.com |\n| 3 | Submit login | click | `[data-testid=\"login-submit-button\"]` |  |\n| 4 | Navigate to login page URL while already logged in | navigate | `/login` |  |\n| 5 | Verify redirected to notes list | assert_url | `` | /notes |\n", "parent_ids": ["US-004-02", "EP-004"]}, {"id": "TC-004-07", "type": "test_plan", "path": "test-plans/TC-004-07.md", "content": "---\nid: TC-004-07\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-11T08:45:38Z\nstory: [[US-004-03]]\nepic: [[EP-004]]\nmodule: see archive and restore controls only when logged in with the lead role\n---\n\n# TC-004-07: the note list renders\n\n**Story:** [[US-004-03]]\n**Module:** see archive and restore controls only when logged in with the lead role\n\n## Preconditions\na logged-in team lead viewing a note\n\n## Expected Result\nan archive button is visible on active notes and a restore button is visible on archived notes\n\n## Acceptance Criteria\nGiven a logged-in team lead viewing a note, when the note list renders, then an archive button is visible on active notes and a restore button is visible on archived notes\nGiven a logged-in team member viewing the same notes, when the note list renders, then no archive or restore controls are visible anywhere in the UI\nGiven a session where role data is missing or unresolved, when the notes list renders, then archive/restore controls default to hidden until role is confirmed\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Log in as a team lead | navigate | `/notes` |  |\n| 2 | Verify archive button visible on active notes | assert_visible | `[data-testid=\"note-detail-archive-button\"]` |  |\n| 3 | Navigate to archived notes | navigate | `/notes/archived` |  |\n| 4 | Verify restore button visible on archived notes | assert_visible | `[data-testid=\"archived-notes-restore-button\"]` |  |\n", "parent_ids": ["US-004-03", "EP-004"]}, {"id": "TC-004-08", "type": "test_plan", "path": "test-plans/TC-004-08.md", "content": "---\nid: TC-004-08\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-11T08:45:38Z\nstory: [[US-004-03]]\nepic: [[EP-004]]\nmodule: see archive and restore controls only when logged in with the lead role\n---\n\n# TC-004-08: [error] the note list renders\n\n**Story:** [[US-004-03]]\n**Module:** see archive and restore controls only when logged in with the lead role\n\n## Preconditions\na logged-in team member viewing the same notes\n\n## Expected Result\nno archive or restore controls are visible anywhere in the UI\n\n## Acceptance Criteria\nGiven a logged-in team lead viewing a note, when the note list renders, then an archive button is visible on active notes and a restore button is visible on archived notes\nGiven a logged-in team member viewing the same notes, when the note list renders, then no archive or restore controls are visible anywhere in the UI\nGiven a session where role data is missing or unresolved, when the notes list renders, then archive/restore controls default to hidden until role is confirmed\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Log in as a team member (non-lead) | navigate | `/notes` |  |\n| 2 | Verify no archive or restore controls visible | assert_visible | `[data-testid=\"note-detail-archive-button\"]` |  |\n", "parent_ids": ["US-004-03", "EP-004"]}, {"id": "TC-004-09", "type": "test_plan", "path": "test-plans/TC-004-09.md", "content": "---\nid: TC-004-09\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-11T08:45:38Z\nstory: [[US-004-03]]\nepic: [[EP-004]]\nmodule: see archive and restore controls only when logged in with the lead role\n---\n\n# TC-004-09: [edge] the notes list renders\n\n**Story:** [[US-004-03]]\n**Module:** see archive and restore controls only when logged in with the lead role\n\n## Preconditions\na session where role data is missing or unresolved\n\n## Expected Result\narchive/restore controls default to hidden until role is confirmed\n\n## Acceptance Criteria\nGiven a logged-in team lead viewing a note, when the note list renders, then an archive button is visible on active notes and a restore button is visible on archived notes\nGiven a logged-in team member viewing the same notes, when the note list renders, then no archive or restore controls are visible anywhere in the UI\nGiven a session where role data is missing or unresolved, when the notes list renders, then archive/restore controls default to hidden until role is confirmed\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Log in with a session where role data is missing or unresolved | navigate | `/notes` |  |\n| 2 | Verify archive/restore controls default to hidden | assert_visible | `[data-testid=\"note-detail-archive-button\"]` |  |\n", "parent_ids": ["US-004-03", "EP-004"]}, {"id": "TC-004-10", "type": "test_plan", "path": "test-plans/TC-004-10.md", "content": "---\nid: TC-004-10\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-11T08:45:38Z\nstory: [[US-004-04]]\nepic: [[EP-004]]\nmodule: log out to end their session\n---\n\n# TC-004-10: the logout action completes\n\n**Story:** [[US-004-04]]\n**Module:** log out to end their session\n\n## Preconditions\na logged-in user clicks logout\n\n## Expected Result\ntheir session is cleared and they are redirected to the login screen\n\n## Acceptance Criteria\nGiven a logged-in user clicks logout, when the logout action completes, then their session is cleared and they are redirected to the login screen\nGiven a user who has just logged out, when they press the browser back button to the notes page, then they are redirected to the login screen rather than seeing cached notes\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Log in and go to notes list | navigate | `/notes` |  |\n| 2 | Click logout | click | `[data-testid=\"app-nav-logout-button\"]` |  |\n| 3 | Verify session cleared and redirected to login | assert_url | `` | /login |\n", "parent_ids": ["US-004-04", "EP-004"]}, {"id": "TC-004-11", "type": "test_plan", "path": "test-plans/TC-004-11.md", "content": "---\nid: TC-004-11\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-11T08:45:38Z\nstory: [[US-004-04]]\nepic: [[EP-004]]\nmodule: log out to end their session\n---\n\n# TC-004-11: [edge] they press the browser back button to the notes page\n\n**Story:** [[US-004-04]]\n**Module:** log out to end their session\n\n## Preconditions\na user who has just logged out\n\n## Expected Result\nthey are redirected to the login screen rather than seeing cached notes\n\n## Acceptance Criteria\nGiven a logged-in user clicks logout, when the logout action completes, then their session is cleared and they are redirected to the login screen\nGiven a user who has just logged out, when they press the browser back button to the notes page, then they are redirected to the login screen rather than seeing cached notes\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Log in, view notes list, then log out | navigate | `/notes` |  |\n| 2 | Click logout | click | `[data-testid=\"app-nav-logout-button\"]` |  |\n| 3 | Press browser back button | navigate | `back` |  |\n| 4 | Verify redirected to login instead of cached notes | assert_url | `` | /login |\n", "parent_ids": ["US-004-04", "EP-004"]}, {"id": "TC-004-12", "type": "test_plan", "path": "test-plans/TC-004-12.md", "content": "---\nid: TC-004-12\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-11T08:45:38Z\nstory: [[US-004-05]]\nepic: [[EP-004]]\nmodule: receive clear feedback if a role-restricted action they attempt is rejected\n---\n\n# TC-004-12: [error] the archive action is submitted and the API returns 403\n\n**Story:** [[US-004-05]]\n**Module:** receive clear feedback if a role-restricted action they attempt is rejected\n\n## Preconditions\na user whose UI briefly shows an archive control due to stale state\n\n## Expected Result\na not-authorized message is shown and the note's state remains unchanged\n\n## Acceptance Criteria\nGiven a user whose UI briefly shows an archive control due to stale state, when the archive action is submitted and the API returns 403, then a not-authorized message is shown and the note's state remains unchanged\nGiven a logged-in user whose session has expired mid-action, when they submit any note action, then they are redirected to the login screen instead of seeing a generic error\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Simulate stale UI state showing an archive control, then click Archive | click | `[data-testid=\"note-detail-archive-button\"]` |  |\n| 2 | Confirm archive action | click | `[data-testid=\"archive-confirm-button\"]` |  |\n| 3 | Verify not-authorized message shown | assert_visible | `[data-testid=\"role-restricted-feedback-message\"]` |  |\n| 4 | Verify note's state remains unchanged | assert_visible | `[data-testid=\"note-detail-title\"]` |  |\n", "parent_ids": ["US-004-05", "EP-004"]}, {"id": "TC-004-13", "type": "test_plan", "path": "test-plans/TC-004-13.md", "content": "---\nid: TC-004-13\ntype: test_plan\nstatus: planned\nowner_rig: qa\nproject: unknown\ncreated_at: 2026-10-11T08:45:38Z\nstory: [[US-004-05]]\nepic: [[EP-004]]\nmodule: receive clear feedback if a role-restricted action they attempt is rejected\n---\n\n# TC-004-13: [edge] they submit any note action\n\n**Story:** [[US-004-05]]\n**Module:** receive clear feedback if a role-restricted action they attempt is rejected\n\n## Preconditions\na logged-in user whose session has expired mid-action\n\n## Expected Result\nthey are redirected to the login screen instead of seeing a generic error\n\n## Acceptance Criteria\nGiven a user whose UI briefly shows an archive control due to stale state, when the archive action is submitted and the API returns 403, then a not-authorized message is shown and the note's state remains unchanged\nGiven a logged-in user whose session has expired mid-action, when they submit any note action, then they are redirected to the login screen instead of seeing a generic error\n\n## Planned Playwright Steps\n\n| # | Instruction | Action | Selector | Value |\n|---|---|---|---|---|\n| 1 | Log in and open a note with session set to expire mid-action | navigate | `/notes/1/edit` |  |\n| 2 | Submit a note action (Save) | click | `[data-testid=\"note-form-save-button\"]` |  |\n| 3 | Verify redirected to login screen instead of a generic error | assert_url | `` | /login |\n", "parent_ids": ["US-004-05", "EP-004"]}]}
