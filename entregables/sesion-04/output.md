# FlowSync MVP — User Story Backlog

> Generated from `docs/PRD.md`. Anything not stated literally in the PRD is marked **(assumed)**. Endpoint paths, status codes, and model relationships are illustrative implementation details **(assumed)** — the PRD explicitly states architecture is not prescribed and is to be defined in OpenSpec during development.

---

## Epic 1 — Authentication & Account Management

### Story 1: Registration
- Story: As a visitor, I want to create an account with my email and password with the aim of accessing FlowSync and starting to manage my tasks.

- Scenario: Successful registration
Given a visitor submits a valid, unregistered email and a password of 8+ characters
When they request POST /api/auth/register (assumed)
Then the response is 201 Created
And a new user account is created with that email
And the user is redirected to the onboarding welcome screen inviting them to create their first task

- Scenario: Registration with an already-registered email
Given the email "john@example.com" is already registered
When a visitor attempts to register with that same email
Then the response is 409 Conflict (assumed status code)
And the message indicates the email is already in use
And the response offers a path to the login screen

- Scenario: Registration with a password shorter than 8 characters
Given a visitor submits a password with fewer than 8 characters
When they request POST /api/auth/register
Then the response is 400 Bad Request
And the message clearly states the minimum password length, not a generic/technical error

- Scenario: Registration with an invalid email format
Given a visitor submits a malformed email (e.g. "not-an-email")
When they request POST /api/auth/register
Then the response is 400 Bad Request
And the message clearly indicates the email format is invalid

- DoD
[ ] Endpoint implemented following the existing pattern
[ ] Functional tests covering all GWT acceptance criteria
[ ] Auto-generated OpenAPI documentation
[ ] OpenSpec change archived after merge

- Technical context
Endpoint (assumed): POST /api/auth/register — validated with VineJS (email format, password min length 8, required fields).
User model (Lucid ORM) persisted in SQLite; password stored hashed, never in plain text (assumed, standard practice, not stated in PRD but implied by "almacenar de forma segura" NFR applied to credentials).
No email verification link required in MVP (literal PRD constraint).
On success, frontend (React Router) navigates to a dedicated onboarding route (assumed route name).

---

### Story 2: Login
- Story: As a registered user, I want to log in with my email and password with the aim of accessing my tasks and calendar sync status.

- Scenario: Successful login
Given a registered user submits their correct email and password
When they request POST /api/auth/login (assumed)
Then the response is 200 OK
And an access token is returned (per `@adonisjs/auth` access-token strategy)
And the token grants access to subsequent authenticated requests

- Scenario: Login with an incorrect password
Given a registered user submits their correct email but a wrong password
When they request POST /api/auth/login
Then the response is 401 Unauthorized
And the message is a generic "invalid credentials" (does not reveal whether the email exists, assumed for basic security hygiene)
And no access token is issued

- Scenario: Login with an unregistered email
Given the submitted email does not correspond to any account
When the login request is made
Then the response is 401 Unauthorized
And the same generic "invalid credentials" message is returned

- Scenario: Login with missing fields
Given a login request is submitted without a password
When it reaches the backend
Then the response is 400 Bad Request
And the message indicates which field is required

- DoD
[ ] Endpoint implemented following the existing pattern
[ ] Functional tests covering all GWT acceptance criteria
[ ] Auto-generated OpenAPI documentation
[ ] OpenSpec change archived after merge

- Technical context
Endpoint (assumed): POST /api/auth/login — VineJS validation, `@adonisjs/auth` access-token guard for issuing tokens.
No "remember me" / password-reset-by-link flow in MVP (literal PRD exclusion).

---

### Story 3: Logout
- Story: As an authenticated user, I want to log out with the aim of ending my session securely, especially on a shared device.

- Scenario: Successful logout
Given an authenticated user has a valid access token
When they request POST /api/auth/logout (assumed)
Then the response is 200 OK
And the access token is revoked/invalidated server-side

- Scenario: Using the API after logout
Given a user has just logged out
When a subsequent request is made with the now-revoked token
Then the response is 401 Unauthorized

- Scenario: Logout without an active session
Given a request to the logout endpoint carries no token or an already-invalid token
When it reaches the backend
Then the response is 401 Unauthorized
And no server error is thrown (assumed graceful handling)

- DoD
[ ] Endpoint implemented following the existing pattern
[ ] Functional tests covering all GWT acceptance criteria
[ ] Auto-generated OpenAPI documentation
[ ] OpenSpec change archived after merge

- Technical context
Endpoint (assumed): POST /api/auth/logout — deletes the current access token row via `@adonisjs/auth` token provider.

---

## Epic 2 — Task Management (CRUD)

### Story 4: Create a task
- Story: As a user, I want to create a task with at least a title with the aim of tracking something I need to do.

- Scenario: Create a task with only a title
Given an authenticated user submits a task with title "Buy groceries" and no other fields
When they request POST /api/users/{user_id}/tasks (assumed)
Then the response is 201 Created
And the task is created with status="pending"
And description is empty/null and due date is null

- Scenario: Create a task with title, description, and due date
Given an authenticated user submits title, description, and a valid due date
When the creation request is made
Then the response is 201 Created
And all three fields are persisted exactly as submitted

- Scenario: Create a task without a title
Given an authenticated user submits a task with an empty title
When the creation request is made
Then the response is 400 Bad Request
And the message clearly states the title is required

- Scenario: New task always starts as pending
Given any valid task creation payload, regardless of due date
When the task is created
Then its status is always "pending", never "completed" or "archived"

- DoD
[ ] Endpoint implemented following the existing pattern
[ ] Functional tests covering all GWT acceptance criteria
[ ] Auto-generated OpenAPI documentation
[ ] OpenSpec change archived after merge

- Technical context
Endpoint (assumed): POST /api/users/{user_id}/tasks — VineJS validation (title required, description optional, due date optional ISO date).
Task model (Lucid ORM, assumed): belongsTo User; fields: title, description, status (enum: pending|completed|archived), due_date, google_event_id (nullable, for sync — assumed).
If the user has an active Google connection and a due date is provided, task creation triggers calendar sync (see Story 11).

---

### Story 5: View task list
- Story: As a user, I want to see the list of my tasks with the aim of having an overview of what I need to do.

- Scenario: Listing tasks returns only the requesting user's data
Given a user has 6 tasks and another user has 4 different tasks
When the first user requests GET /api/users/{user_id}/tasks (assumed)
Then the response is 200 OK
And it contains exactly 6 tasks, all owned by the requesting user

- Scenario: Empty state for a new account or all-archived tasks
Given a user has zero tasks (new account, or all tasks archived)
When they view their task list
Then the UI shows an empty state
And it includes an invitation/call-to-action to create the first task

- Scenario: Default ordering surfaces "today"-relevant tasks first (assumed)
Given a user has tasks with mixed due dates (overdue, due today, due later, no due date)
When they view the list without an explicit sort applied
Then tasks due today or overdue are shown before tasks with a later or no due date
(assumed — the PRD explicitly leaves the exact ordering criterion to team refinement; behavior to be confirmed in OpenSpec)

- Scenario: Performance under load
Given a user has up to 200 tasks
When they request the task list
Then the response completes in under 1 second (NFR)

- DoD
[ ] Endpoint implemented following the existing pattern
[ ] Functional tests covering all GWT acceptance criteria
[ ] Auto-generated OpenAPI documentation
[ ] OpenSpec change archived after merge

- Technical context
Endpoint (assumed): GET /api/users/{user_id}/tasks.
User model has a one-to-many relation with Task; queries scoped by authenticated user_id from the access token (never by trusting a client-supplied user_id) to guarantee the privacy NFR.
Default sort criteria (assumed placeholder, pending refinement per PRD §3.3): due_date ASC NULLS LAST.

---

### Story 6: Edit a task (including status changes)
- Story: As a user, I want to edit any field of an existing task — including marking it completed or archived — with the aim of keeping my task information accurate and reflecting my progress.

- Scenario: Edit title, description, or due date
Given a user owns a pending task
When they request PATCH /api/users/{user_id}/tasks/{task_id} (assumed) with updated title/description/due_date
Then the response is 200 OK
And the updated fields are persisted

- Scenario: Change status from pending to completed
Given a user owns a task with status="pending"
When they request PATCH .../tasks/{task_id} with status="completed"
Then the response is 200 OK
And the task's status is now "completed"

- Scenario: Editing a task owned by another user
Given task "banana" belongs to user "jane", not the requesting user "john"
When "john" requests PATCH /api/users/john/tasks/banana
Then the response is 401 Unauthorized (assumed status code, MVP has no shared-permission roles)
And the message states the user is not authorized to edit the task

- Scenario: Editing a non-existent task
Given no task with ID "noexistent" exists for the requesting user
When they request PATCH .../tasks/noexistent
Then the response is 404 Not Found

- Scenario: Clearing the required title field
Given a user submits an edit with title set to an empty string
When the request reaches the backend
Then the response is 400 Bad Request
And the message states the title cannot be empty

- DoD
[ ] Endpoint implemented following the existing pattern
[ ] Functional tests covering all GWT acceptance criteria
[ ] Auto-generated OpenAPI documentation
[ ] OpenSpec change archived after merge

- Technical context
Endpoint (assumed): PATCH /api/users/{user_id}/tasks/{task_id}.
Ownership check: task.user_id must equal the authenticated user's ID (no "owner/editor/reader" roles exist in MVP — single-user scope per Non-goals).
If due_date changes and the user has an active Google connection, the linked calendar event is updated (see Story 11).
If status changes to "completed", the linked calendar event is deleted or marked, per Story 11.

---

### Story 7: Delete a task
- Story: As a user, I want to delete a task with the aim of removing something I no longer need to track.

- Scenario: Successful deletion of an own task
Given a user owns a task with ID "banana"
When they request DELETE /api/users/{user_id}/tasks/banana (assumed)
Then the response is 200 OK
And the task no longer appears in the user's task list

- Scenario: Deleting a task owned by another user
Given task "banana" belongs to a different user
When the requesting user attempts DELETE .../tasks/banana
Then the response is 401 Unauthorized
And the message states the user is not authorized to delete the task

- Scenario: Deleting a non-existent task
Given no task with ID "noexistent" exists for the requesting user
When they request DELETE .../tasks/noexistent
Then the response is 404 Not Found
And the message states the task could not be found

- Scenario: Deleting a task synced to Google Calendar
Given a task has an associated Google Calendar event (google_event_id is set) and the user is connected
When the task is deleted
Then the task is removed from FlowSync
And the corresponding event is deleted or marked accordingly in Google Calendar

- DoD
[ ] Endpoint implemented following the existing pattern
[ ] Functional tests covering all GWT acceptance criteria
[ ] Auto-generated OpenAPI documentation
[ ] OpenSpec change archived after merge

- Technical context
Endpoint (assumed): DELETE /api/users/{user_id}/tasks/{task_id}.
Ownership check identical to Story 6 (no roles in MVP).
Deletion cascades to calendar cleanup only if `google_event_id` is present and the Google connection is active; if the sync call fails, the local deletion still succeeds and the cleanup is retried later (per Story 11's resilience requirement).

---

## Epic 3 — Organization & Filtering

### Story 8: Filter tasks by status
- Story: As a user, I want to filter my tasks by status with the aim of quickly finding my pending, completed, or archived tasks.

- Scenario: Filtering by pending status
Given the user has 12 tasks (5 pending, 4 completed, 3 archived)
When they request GET /api/users/{user_id}/tasks?status=pending
Then the response is 200 OK
And it contains exactly 5 tasks, all with status="pending"

- Scenario: Filtering by archived status
Given the same 12 tasks
When they request GET /api/users/{user_id}/tasks?status=archived
Then the response is 200 OK
And it contains exactly 3 tasks, all with status="archived"

- Scenario: Filtering with an invalid status value
Given the user requests GET /api/users/{user_id}/tasks?status=banana
When the request reaches the backend
Then the response is 400 Bad Request
And the message lists the valid values: "pending" | "completed" | "archived"

- Scenario: No filter applied returns all statuses in default order
Given the user requests the list with no `status` query parameter
When the request reaches the backend
Then the response is 200 OK
And it contains tasks of all statuses, ordered per Story 5's default ordering (assumed)

- DoD
[ ] Endpoint implemented following the existing pattern
[ ] Functional tests covering all GWT acceptance criteria
[ ] Auto-generated OpenAPI documentation
[ ] OpenSpec change archived after merge

- Technical context
Endpoint (assumed): GET /api/users/{user_id}/tasks?status=[pending|completed|archived], reusing the listing endpoint from Story 5.
Validated via VineJS enum rule against the three allowed status values (literal PRD enum, §3.2 and Glossary).

---

## Epic 4 — Export

### Story 9: Export tasks to CSV
- Story: As a user, I want to export my tasks to a CSV file with the aim of keeping a personal backup or using my data in another tool.

- Scenario: Export with existing tasks
Given a user has 8 tasks with varying statuses and due dates
When they request GET /api/users/{user_id}/tasks/export (assumed)
Then the response is 200 OK with Content-Type text/csv
And the file contains one row per task with at least: title, description, status, due date

- Scenario: Export with zero tasks
Given a user has no tasks
When they request the export
Then the response is 200 OK
And the CSV contains only the header row, no data rows (assumed)

- Scenario: Export only includes the requesting user's tasks
Given user "john" has 8 tasks and user "jane" has 5 different tasks
When "john" requests his export
Then the CSV contains exactly 8 rows, none belonging to "jane"

- Scenario: Fields containing commas or quotes are properly escaped
Given a task has the title `Buy milk, eggs, "bread"`
When it is included in the export
Then the CSV row escapes the field per RFC 4180 so the file remains valid and parses back to the original value
(assumed technical detail required for a correct CSV, not stated explicitly in the PRD)

- DoD
[ ] Endpoint implemented following the existing pattern
[ ] Functional tests covering all GWT acceptance criteria
[ ] Auto-generated OpenAPI documentation
[ ] OpenSpec change archived after merge

- Technical context
Endpoint (assumed): GET /api/users/{user_id}/tasks/export, streaming/generating a CSV from the user's Task records.
Minimum required columns per PRD §3.4 (literal): title, description, status, due date.

---

## Epic 5 — Google Calendar Synchronization

### Story 10: Connect Google account
- Story: As a user, I want to connect my Google account via OAuth with the aim of enabling automatic synchronization between my tasks and my calendar.

- Scenario: Initiating the connection
Given an authenticated user with no active Google connection
When they click "Connect Google Calendar" and request GET /api/users/{user_id}/google/connect (assumed)
Then they are redirected to Google's OAuth 2.0 consent screen requesting Calendar read/write scope

- Scenario: Granting permission
Given the user approves the OAuth consent screen
When Google redirects back with an authorization code
Then FlowSync exchanges it for access/refresh tokens
And stores them securely, associated with the user (NFR: tokens never accessible to other users, stored securely)
And the UI shows the connection status as "connected"

- Scenario: Denying or cancelling permission
Given the user cancels or denies the consent screen
When Google redirects back without an authorization code
Then FlowSync shows the connection as "not connected"
And no error crash occurs, and no partial/invalid tokens are stored (assumed graceful handling)

- Scenario: Existing tasks with due dates sync retroactively after connecting
Given a user already has 3 pending tasks with due dates before connecting Google
When the connection completes successfully
Then calendar events are created for those 3 tasks
(assumed — the PRD states connected tasks with due dates "appear" as events but does not explicitly address the pre-existing-tasks case; to be confirmed in the recommended technical spike per PRD §7)

- DoD
[ ] Endpoint implemented following the existing pattern
[ ] Functional tests covering all GWT acceptance criteria
[ ] Auto-generated OpenAPI documentation
[ ] OpenSpec change archived after merge
[ ] Google Cloud Console project & OAuth consent screen configured (PRD §7 known setup risk)

- Technical context
OAuth 2.0 Authorization Code flow against Google Calendar API (literal, PRD §3.5 and §5).
GoogleConnection model (assumed): belongsTo User (one-to-one), stores encrypted access_token, refresh_token, scope, expiry.
Token encryption at rest required by the privacy NFR (literal requirement, encryption mechanism assumed).

---

### Story 11: Automatic sync of tasks to calendar events
- Story: As a user, I want my tasks with due dates to automatically stay in sync with Google Calendar events with the aim of seeing my tasks and my time in one place, without copying anything by hand.

- Scenario: Creating a task with a due date creates a calendar event
Given a user has an active Google connection
When they create or edit a task to have a due date
Then a corresponding event is created in the user's Google Calendar
And the task stores the resulting google_event_id (assumed)

- Scenario: Changing a task's due date updates the event
Given a task is already synced with an existing calendar event
When the user changes that task's due date
Then the corresponding Google Calendar event's date/time is updated to match

- Scenario: Completing or deleting a task removes/updates the event
Given a synced task exists
When the user marks it "completed" or deletes it
Then the corresponding Google Calendar event is deleted, or marked accordingly
(exact treatment — delete vs. mark — is assumed to be "delete", since PRD §3.5 states "se elimina o se marca según corresponda" without specifying which case applies to which action; to be clarified in OpenSpec)

- Scenario: Google API unavailable during sync
Given the Google Calendar API returns an error or is unreachable when a sync operation is attempted
When the user creates/edits/completes/deletes a task
Then the task change is still saved successfully in FlowSync
And the sync operation is queued for retry rather than lost
And the failure is logged for diagnosis (NFR: Observability)

- Scenario: Sync direction is FlowSync → Google Calendar only
Given a user edits the corresponding event directly in Google Calendar (e.g. changes its time)
When no action is taken in FlowSync
Then the task in FlowSync is not automatically updated in the MVP
(assumed/explicit per PRD's product note: reverse sync is "desirable" but its scope must be validated via a technical spike before being committed — treated as out of scope for this story)

- DoD
[ ] Endpoint/job implemented following the existing pattern
[ ] Functional tests covering all GWT acceptance criteria
[ ] Retry mechanism covered by tests (simulated API failure)
[ ] Sync operations logged and visible for diagnostics
[ ] OpenSpec change archived after merge

- Technical context
Sync direction: FlowSync → Google Calendar (literal, primary direction per PRD product note in §3.5); reverse sync excluded from MVP pending a spike (literal).
Task model gains google_event_id (assumed) to map a task to its calendar event.
Failed sync operations are queued and retried asynchronously (assumed mechanism: background job/queue) rather than blocking the task-mutation request — literal requirement is only that "the task is saved even if sync fails, and retried later."
Time zone handling between task due_date and the event's start/end time must be defined carefully (PRD §7 known risk) — exact mapping (e.g. all-day event vs. timed event) is assumed to require its own spike, not fully specified in the PRD.

---

### Story 12: Disconnect Google account
- Story: As a user, I want to disconnect my Google account at any time with the aim of stopping synchronization while keeping the tasks I already created.

- Scenario: Successful disconnection
Given a user has an active Google connection
When they request DELETE /api/users/{user_id}/google/connection (assumed)
Then the response is 200 OK
And FlowSync's stored tokens for that user are deleted
And the connection status shown in the UI becomes "not connected"

- Scenario: Existing tasks are preserved after disconnecting
Given a user disconnects Google after having synced tasks
When they view their task list afterward
Then all previously created tasks are still present in FlowSync, unchanged

- Scenario: No further sync occurs after disconnecting
Given a user has disconnected Google
When they create, edit, complete, or delete a task afterward
Then no calendar API calls are attempted for that user
And no error is shown related to a missing connection

- Scenario: Previously created calendar events are left untouched
Given a user disconnects Google
When the disconnection completes
Then events already created in Google Calendar are not deleted by FlowSync
(assumed — the PRD only states that FlowSync tasks are not deleted on disconnect; it does not specify the fate of already-created Calendar-side events, so "leave as-is" is the conservative reading)

- DoD
[ ] Endpoint implemented following the existing pattern
[ ] Functional tests covering all GWT acceptance criteria
[ ] Auto-generated OpenAPI documentation
[ ] OpenSpec change archived after merge

- Technical context
Endpoint (assumed): DELETE /api/users/{user_id}/google/connection — removes the GoogleConnection record (or clears tokens) for the user.
All task-mutation flows (Stories 4, 6, 7) must check for an active, valid connection before attempting any Google API call, and no-op silently if disconnected (assumed guard condition).

---

## Non-goals reaffirmed (explicitly excluded from this backlog)
- Shared/team tasks — FlowSync MVP is single-user only.
- Sync with calendars other than Google Calendar.
- Push/email notifications.
- Native mobile app.
- Tags, projects, or subtasks/hierarchies.
- Configurable reminders beyond Google Calendar's own.
- Full bidirectional sync (Google → FlowSync) — PRD explicitly defers this to a technical spike; not committed as a story here.
