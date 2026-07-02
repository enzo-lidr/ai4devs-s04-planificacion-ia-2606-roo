# Context and use case structure

You are a senior Product Owner.

You are in charge of dealing with a new MVP project, for a web application called FlowSync.

FlowSync is a web application for personal task management that keeps a user's tasks synchronized with their Google Calendar. It solves the problem that people typically manage their tasks in one tool (a task list) and their time in another (a calendar), making it tedious and error-prone to keep both in sync manually. FlowSync eliminates this manual effort: tasks with due dates automatically appear as calendar events, and changes are synchronized in both directions.

You must return the possible user stories, taking into consideration the PRD.md, which is the current context of this prompt. Group the stories by module, use case or epic, depending on what you consider convenient for this case.

The format you have to deliver the outcome for each user story is: "As [role], I want to [action] with the aim of [benefit]". The Acceptance Criteria must follow Gherkin format (Given/When/Then).

At the end of each story, include a technical context, where technical requirements should be included.

# Stories examples

Two posible examples are:

## Example 1

- Story: As user, I want to filter pending tasks in order to find my duties today.

- Scenario: Standard filtering by pending status
Given the user has 12 tasks (5 pending, 7 completed)
When they request /api/users/john/tasks?status=pending
Then the response is 200 OK
And it contains exactly 5 tasks
And all of them have status="pending"

- Scenario: Filtering with an invalid status value
Given the user requests /api/users/john/tasks?status=banana
When the request reaches the backend
Then it responds with 400 Bad Request
And the message includes the valid status values: "pending" | "completed" | "archived"

- DoD
[ ] Endpoint implemented following the existing pattern
[ ] Functional tests covering all GWT acceptance criteria
[ ] Auto-generated OpenAPI documentation
[ ] OpenSpec change archived after merge

- Technical context
Current endpoint: GET /api/users/{user_id}/tasks?status=[ "pending" | "completed" | "archived" ]
User model has a @OneToMany relation with Task model.
Task model has a @OneToOne relation with User model.
The user requesting the list of tasks should have one of the following permissions: [ "owner" | "editor" | "reader" ]

## Example 2

- Story: As user, I want to delete a task in order to force that task to be withdrawed from the web application.

- Scenario: Deleting a task, where the user does not have the "owner" or "editor" permission
Given the user requests /api/users/john/tasks/banana
When the request reaches the backend
And the user with ID equal to "John" does not have the "owner" or "editor" permission for that task
Then it responds with 401 Unauthorized
And the message is: The user is not authorized to delete the task

- Scenario: Standard deletion of task
Given the user has clicked on an "archived" task with and ID String equal to "banana"
When they request /api/users/john/tasks/banana?status=archived
Then the response is 200 OK
And the task is deleted from the corresponding "TODO" list
And the task or event is deleted from Google Calendar

- Scenario: Deleting with and invalid ID
Given the user requests /api/users/john/tasks/noexistent
When the request reaches the backend
And the task with ID equal to "noexistent" does not exist in Google Calendar, nor in the corresponding "TODO" list
Then it responds with 404 Not Found
And the message is: The task with ID "noexistent" could not be detected in Google Calendar, nor in the corresponding "TODO" list

- Scenario: Deleting with and invalid status value
Given the user requests /api/users/john/tasks/banana
When the request reaches the backend
And the task with ID equal to "banana" exists but its status is not equal to "archived"
Then it responds with 428 Precondition required
And the message is: The task must be in "archived" status before triggering deletion

- DoD
[ ] Endpoint implemented following the existing pattern
[ ] Functional tests covering all GWT acceptance criteria
[ ] Auto-generated OpenAPI documentation
[ ] OpenSpec change archived after merge

- Technical context
Current endpoint: DELETE /api/users/{user_id}/tasks/{task_id}
User model has a @OneToMany relation with Task model.
Task model has a @OneToOne relation with User model.
User should have the "owner" or "editor" permission for the task.

# Non-goals

- Teams or shared tasks (the FlowSync MVP is strictly single-user).

- Synchronization with calendars other than Google Calendar (e.g., Outlook or iCal): post-MVP.

- Push or email notifications.

- Native mobile application (responsive web only).

- Tags, projects, or task hierarchies (subtasks): post-MVP.

- Configurable reminders beyond those provided by Google Calendar.

- Do not propose or include any other feature not listed or mentioned in the enclosed document.

- I want you to write the AC's in Gherkin format. Each story shoud have 3 to 5 acceptance criteria verifiable, not generic.


# Transparency instruction
Mark with (assumed) what is inferred and what is not literal in the PRD.