## Poke-holes: Story 6 — Edit a task

### Edge cases not covered
- **Status transitions beyond pending→completed**: no scenario for `archived` at all, nor for reopening (`completed→pending`, `archived→pending`). The story title claims "including marking it completed or archived" but only tests the completed path.
- **Editing an already-archived or already-completed task**: is that even allowed? Can you change the title of a completed task? Nothing says archived/completed tasks become read-only.
- **Removing a due date** (`due_date: null`) on a task that has a synced calendar event — not tested.
- **Adding a due date for the first time** to a task that previously had none, while Google is connected — this is a *creation* of a calendar event, not an "update," but the story only discusses updating an existing link.
- **Multi-field edit in one request** (e.g., status + due_date changed simultaneously) — ordering/interaction of the two side-effects (calendar update + calendar delete/mark) is undefined.
- Invalid `status` value in the payload (e.g., `status="banana"`) — no validation scenario, only the empty-title case is tested.
- Malformed or past-dated `due_date` — no validation scenario.
- Empty PATCH body / no-op request.
- Field length limits (title/description) — undefined, no scenario.

### Implicit assumptions
- A single generic PATCH endpoint handles both field edits and status transitions. Given the example patterns already used elsewhere in this backlog (status-scoped operations), a separate status-transition endpoint/rule set is at least as plausible, and would change how invalid transitions are validated.
- **401 vs 403 conflation**: "task owned by another user" is called 401 Unauthorized, but 401 conventionally means *not authenticated* — an authenticated user who simply lacks permission is normally 403 Forbidden. This was inherited unmodified from the earlier delete-story example and never re-examined.
- Calendar sync on edit is implied to happen **synchronously** within the PATCH request/response cycle. This isn't stated explicitly, but if true it contradicts Story 11's resilience requirement (task saved even if Google API sync fails, retried later) — nothing in Story 6 says what HTTP response the client gets if the sync call fails mid-request.
- Assumes description has no validation while title does — never confirmed either way.
- Assumes request/response are JSON — never stated.
- Assumes the response body shape (full updated task object vs. partial diff) without specifying it.

### Lacking scenarios
- Editing a task while **not connected to Google** (confirms no sync side-effects/errors occur).
- Edit that **adds** a due date for the first time on a connected user's task → should this create an event? Not covered by the "linked calendar event is updated" wording, which presumes a link already exists.
- Edit that **clears** a due date on a synced task → should delete/unlink the calendar event.
- Status changed to `archived` specifically → calendar event behavior undefined (Technical Context only addresses "completed").
- Sync failure during edit (Google API down) → does PATCH still return 200 with the local change persisted and sync queued, per Story 11? No scenario confirms this consistency between stories.
- Invalid `status` enum value.

### Dependencies / risks not mentioned
- **Coupling to Story 11's retry queue**: if calendar sync is invoked inline during the edit request, a slow or failing Google API call could either block the PATCH response or silently succeed locally while failing remotely — this data-drift risk (FlowSync says one due date, Calendar shows another) isn't addressed by either story.
- **google_event_id lifecycle** isn't owned by any single story: Story 4 (create) doesn't mention setting it when there's no due date yet, Story 6 assumes it may already exist, and there's no scenario for the "task has no event yet, edit adds a due date" transition — this is a real gap between stories, not just within Story 6.
- **Concurrency/race conditions**: no optimistic locking (e.g., version or `updated_at` check) — two simultaneous edits (or an edit racing a delete) could produce lost updates or a PATCH succeeding against an already-deleted task.
- **Rate limiting**: frequent edits (e.g., a user rapidly changing a due date) could trigger Google Calendar API rate limits, a risk flagged generally in the PRD (§7) but never connected to this specific high-frequency-edit path.
- **Auth token expiry mid-edit** is a distinct case from "wrong owner" but both currently map to the same 401 — no differentiation, which will make client-side error handling ambiguous (retry-login vs. permanent-denied).
