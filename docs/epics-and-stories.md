# Epics and Stories

## MVP

### Epics

- Task Data Model Updates
- Task Creation and Editing
- Date-Based Task Filters
- Local Persistence

### Stories

- Task Data Model Updates
  - Add due date field to task model
  - Add priority field to task model
  - Default priority to P3
  - Preserve title as required field
  - Ignore invalid due date values

- Task Creation and Editing
  - Add due date input to task form
  - Add priority selector to task form
  - Save tasks with due date and priority

- Date-Based Task Filters
  - Add All tasks filter
  - Add Today tasks filter
  - Add Overdue tasks filter
  - Show completed tasks in All filter
  - Hide completed tasks in Today filter
  - Hide completed tasks in Overdue filter

- Local Persistence
  - Store due date in local task data
  - Store priority in local task data
  - Keep task persistence local only

### Acceptance Criteria

- Add due date field to task model
  - Tasks may be saved without a due date.
  - Tasks with a due date use the `YYYY-MM-DD` format.

- Add priority field to task model
  - Each task stores a priority value.
  - Priority accepts only `P1`, `P2`, or `P3`.

- Default priority to P3
  - New tasks without an explicit priority are saved with `P3`.
  - Existing task priority values are preserved during edits unless changed.

- Preserve title as required field
  - Users cannot save a task with an empty title.
  - Invalid create and update requests without a title are rejected.

- Ignore invalid due date values
  - Invalid due date values are treated as missing.
  - Invalid due dates are not used in filtering or display logic.

- Add due date input to task form
  - The task form includes a due date input.
  - The due date input is populated when editing a task with a saved due date.

- Add priority selector to task form
  - The task form includes a priority selector.
  - The selector offers only `P1`, `P2`, and `P3`.

- Save tasks with due date and priority
  - Creating a task saves both due date and priority when provided.
  - Editing a task updates the saved due date and priority values.

- Add All tasks filter
  - Users can switch to an `All` filter view.
  - The `All` filter displays every task regardless of completion state.

- Add Today tasks filter
  - Users can switch to a `Today` filter view.
  - The `Today` filter shows only incomplete tasks due on the current date.

- Add Overdue tasks filter
  - Users can switch to an `Overdue` filter view.
  - The `Overdue` filter shows only incomplete tasks with due dates earlier than the current date.

- Show completed tasks in All filter
  - Completed tasks remain visible when `All` is selected.

- Hide completed tasks in Today filter
  - Completed tasks do not appear when `Today` is selected.

- Hide completed tasks in Overdue filter
  - Completed tasks do not appear when `Overdue` is selected.

- Store due date in local task data
  - Saved tasks retain due date values after reload.

- Store priority in local task data
  - Saved tasks retain priority values after reload.

- Keep task persistence local only
  - The implementation uses only the current local app storage path.
  - No backend expansion or external storage integration is introduced.

### Technical Requirements

- Add due date field to task model
  - Keep task due dates as optional ISO `YYYY-MM-DD` strings throughout the task API and frontend form state.
  - Return `null` or an empty value when a task has no due date instead of inventing placeholder dates.

- Add priority field to task model
  - Add `priority` to the task shape used by the existing `/api/tasks` create, update, and list flows.
  - Constrain stored priority values to `P1`, `P2`, or `P3`.

- Default priority to P3
  - Initialize new tasks with `priority: 'P3'` when no explicit priority is supplied.
  - Ensure edit flows preserve an existing priority value instead of resetting it during save.

- Preserve title as required field
  - Keep frontend submit validation that blocks empty or whitespace-only titles.
  - Keep API-side validation that rejects invalid create and update requests with a clear error response.

- Ignore invalid due date values
  - Validate due date input against `YYYY-MM-DD` before persistence.
  - Treat invalid due date values as absent so they are not used for filters or rendering.

- Add due date input to task form
  - Continue using a date input control bound to a normalized `YYYY-MM-DD` value.
  - Populate the due date field correctly when editing an existing task.

- Add priority selector to task form
  - Add a form control that only allows `P1`, `P2`, and `P3` selections.
  - Default the selector to `P3` for new tasks and prefill the saved value for edits.

- Save tasks with due date and priority
  - Include `due_date` and `priority` in `POST /api/tasks` and `PUT /api/tasks/:id` request payloads.
  - Refresh the task list after save so the new values appear immediately in the UI.

- Add All tasks filter
  - Add filter state to the task list view with `All` as a selectable option.
  - Render every task in the `All` view without excluding completed items.

- Add Today tasks filter
  - Add a `Today` filter that matches incomplete tasks whose due date equals the current local date.
  - Base comparisons on normalized date-only values so filtering is not affected by timezone offsets.

- Add Overdue tasks filter
  - Add an `Overdue` filter that matches incomplete tasks with due dates earlier than the current local date.
  - Exclude tasks with missing or invalid due dates from the overdue result set.

- Show completed tasks in All filter
  - Do not apply completed-task exclusion when the `All` filter is active.

- Hide completed tasks in Today filter
  - Exclude tasks where `completed` is true when the `Today` filter is active.

- Hide completed tasks in Overdue filter
  - Exclude tasks where `completed` is true when the `Overdue` filter is active.

- Store due date in local task data
  - Persist due date values using the app's existing local task storage path and `/api/tasks` contract.
  - Do not introduce any external persistence dependency for due dates.

- Store priority in local task data
  - Persist priority values using the same task storage path as other task fields.
  - Include priority in task reads so reloads and edits retain the saved value.

- Keep task persistence local only
  - Reuse the existing local application data flow and do not add cloud sync, third-party services, or multi-user storage.
  - Limit implementation changes to the current app stack and task endpoints.

## Post-MVP

### Epics

- Overdue Task Visibility
- Task Sorting

### Stories

- Overdue Task Visibility
  - Highlight overdue tasks visually

- Task Sorting
  - Sort overdue tasks before other tasks
  - Sort tasks by priority from P1 to P3
  - Sort tasks by ascending due date
  - Place undated tasks last

### Acceptance Criteria

- Highlight overdue tasks visually
  - Incomplete overdue tasks use a distinct visual style.
  - Non-overdue tasks do not receive the overdue styling.

- Sort overdue tasks before other tasks
  - Overdue tasks appear before non-overdue tasks.

- Sort tasks by priority from P1 to P3
  - Tasks of the same overdue state sort by priority with `P1` first and `P3` last.

- Sort tasks by ascending due date
  - Tasks with valid due dates sort from earliest to latest when earlier sort rules are equal.

- Place undated tasks last
  - Tasks without valid due dates appear after tasks with valid due dates.

### Technical Requirements

- Highlight overdue tasks visually
  - Derive overdue state from incomplete tasks whose due date is before the current local date.
  - Apply a dedicated visual treatment to overdue tasks without changing task completion behavior.

- Sort overdue tasks before other tasks
  - Rank incomplete overdue tasks ahead of all non-overdue tasks in the rendered list order.

- Sort tasks by priority from P1 to P3
  - Use a deterministic priority ranking where `P1` sorts before `P2`, and `P2` sorts before `P3`.

- Sort tasks by ascending due date
  - Compare valid due dates as date-only values and place earlier dates first.

- Place undated tasks last
  - Treat missing or invalid due dates as undated and place them after dated tasks.