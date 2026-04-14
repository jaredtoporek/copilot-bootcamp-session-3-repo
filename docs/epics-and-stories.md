# Epics and Stories

## MVP

- Epic: Task Data Model Updates
  - Story: Add due date field to task model
    - Technical Requirements:
      - Keep task due dates as optional ISO `YYYY-MM-DD` strings throughout the task API and frontend form state.
      - Return `null` or an empty value when a task has no due date instead of inventing placeholder dates.
  - Story: Add priority field to task model
    - Technical Requirements:
      - Add `priority` to the task shape used by the existing `/api/tasks` create, update, and list flows.
      - Constrain stored priority values to `P1`, `P2`, or `P3`.
  - Story: Default priority to P3
    - Technical Requirements:
      - Initialize new tasks with `priority: 'P3'` when no explicit priority is supplied.
      - Ensure edit flows preserve an existing priority value instead of resetting it during save.
  - Story: Preserve title as required field
    - Technical Requirements:
      - Keep frontend submit validation that blocks empty or whitespace-only titles.
      - Keep API-side validation that rejects invalid create and update requests with a clear error response.
  - Story: Ignore invalid due date values
    - Technical Requirements:
      - Validate due date input against `YYYY-MM-DD` before persistence.
      - Treat invalid due date values as absent so they are not used for filters or rendering.

- Epic: Task Creation and Editing
  - Story: Add due date input to task form
    - Technical Requirements:
      - Continue using a date input control bound to a normalized `YYYY-MM-DD` value.
      - Populate the due date field correctly when editing an existing task.
  - Story: Add priority selector to task form
    - Technical Requirements:
      - Add a form control that only allows `P1`, `P2`, and `P3` selections.
      - Default the selector to `P3` for new tasks and prefill the saved value for edits.
  - Story: Save tasks with due date and priority
    - Technical Requirements:
      - Include `due_date` and `priority` in `POST /api/tasks` and `PUT /api/tasks/:id` request payloads.
      - Refresh the task list after save so the new values appear immediately in the UI.

- Epic: Date-Based Task Filters
  - Story: Add All tasks filter
    - Technical Requirements:
      - Add filter state to the task list view with `All` as a selectable option.
      - Render every task in the `All` view without excluding completed items.
  - Story: Add Today tasks filter
    - Technical Requirements:
      - Add a `Today` filter that matches incomplete tasks whose due date equals the current local date.
      - Base comparisons on normalized date-only values so filtering is not affected by timezone offsets.
  - Story: Add Overdue tasks filter
    - Technical Requirements:
      - Add an `Overdue` filter that matches incomplete tasks with due dates earlier than the current local date.
      - Exclude tasks with missing or invalid due dates from the overdue result set.
  - Story: Show completed tasks in All filter
    - Technical Requirements:
      - Do not apply completed-task exclusion when the `All` filter is active.
  - Story: Hide completed tasks in Today filter
    - Technical Requirements:
      - Exclude tasks where `completed` is true when the `Today` filter is active.
  - Story: Hide completed tasks in Overdue filter
    - Technical Requirements:
      - Exclude tasks where `completed` is true when the `Overdue` filter is active.

- Epic: Local Persistence
  - Story: Store due date in local task data
    - Technical Requirements:
      - Persist due date values using the app's existing local task storage path and `/api/tasks` contract.
      - Do not introduce any external persistence dependency for due dates.
  - Story: Store priority in local task data
    - Technical Requirements:
      - Persist priority values using the same task storage path as other task fields.
      - Include priority in task reads so reloads and edits retain the saved value.
  - Story: Keep task persistence local only
    - Technical Requirements:
      - Reuse the existing local application data flow and do not add cloud sync, third-party services, or multi-user storage.
      - Limit implementation changes to the current app stack and task endpoints.

## Post-MVP

- Epic: Overdue Task Visibility
  - Story: Highlight overdue tasks visually
    - Technical Requirements:
      - Derive overdue state from incomplete tasks whose due date is before the current local date.
      - Apply a dedicated visual treatment to overdue tasks without changing task completion behavior.

- Epic: Task Sorting
  - Story: Sort overdue tasks before other tasks
    - Technical Requirements:
      - Rank incomplete overdue tasks ahead of all non-overdue tasks in the rendered list order.
  - Story: Sort tasks by priority from P1 to P3
    - Technical Requirements:
      - Use a deterministic priority ranking where `P1` sorts before `P2`, and `P2` sorts before `P3`.
  - Story: Sort tasks by ascending due date
    - Technical Requirements:
      - Compare valid due dates as date-only values and place earlier dates first.
  - Story: Place undated tasks last
    - Technical Requirements:
      - Treat missing or invalid due dates as undated and place them after dated tasks.