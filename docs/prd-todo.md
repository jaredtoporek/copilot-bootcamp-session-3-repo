# Product Requirements Document (PRD) - Todo App Upgrade

## 1. Overview

We are upgrading the basic Todo app so users can organize work with due dates, priorities, and simple date-based filters without increasing implementation complexity. The goal is a small, teachable MVP that stays frontend-focused, keeps storage local, and improves task visibility while deferring richer presentation and sorting behavior to a later phase.

---

## 2. MVP Scope

- Add an optional `dueDate` field for each task using ISO `YYYY-MM-DD` format.
- Treat invalid `dueDate` values as absent rather than failing the task.
- Add a required `priority` field with allowed values `P1`, `P2`, and `P3`.
- Default `priority` to `P3` when a value is not provided.
- Keep `title` as a required task field.
- Add filter views for `All`, `Today`, and `Overdue`.
- Show completed tasks in the `All` view.
- Hide completed tasks in the `Today` and `Overdue` views.
- Keep persistence local only with no backend or external storage changes.
- Keep the MVP simple and suitable for teaching and demonstration purposes.

---

## 3. Post-MVP Scope

- Visually highlight overdue tasks so they stand out from other incomplete tasks.
- Add explicit sorting rules so tasks are ordered by overdue status first, then by priority from `P1` to `P3`, then by ascending due date, with undated tasks last.

---

## 4. Out of Scope

- Notifications or reminders.
- Recurring tasks.
- Multi-user collaboration or shared task data.
- Keyboard navigation or additional accessibility enhancements beyond the current baseline.
- Backend changes.
- External or cloud-based storage.