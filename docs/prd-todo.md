# Product Requirements Document (PRD) - TODO App Upgrade

## 1. Overview

We are upgrading the basic TODO app (currently supporting only `title` and `completed`) to support due dates, priority levels, and filters so users can better organize tasks and quickly identify what needs attention. The goal is a simple, teachable enhancement with no backend changes — all data remains in local storage.

---

## 2. MVP Scope

- **Due Date field**: Add an optional `dueDate` field (ISO format `YYYY-MM-DD`) to each task
  - Invalid values should be ignored and treated as absent
- **Priority field**: Add a `priority` field with enum values `P1 | P2 | P3` (default: `P3`)
  - Display as color-coded badges: Red for P1, Orange for P2, Gray for P3
- **Filters**: Provide three filter views — **All**, **Today**, **Overdue**
  - **All**: Shows all tasks including completed ones
  - **Today**: Shows only incomplete tasks due today
  - **Overdue**: Shows only incomplete tasks past their due date
- **Data model & validation**:
  - `title`: required
  - `priority`: `"P1" | "P2" | "P3"`, default `"P3"`
  - `dueDate`: optional ISO `YYYY-MM-DD`; invalid values treated as absent
- **Storage**: Local storage only — no backend or external storage

---

## 3. Post-MVP Scope

- **Overdue highlighting**: Visually highlight overdue tasks in red so they stand out at a glance
- **Smart sorting**: Sort tasks by:
  1. Overdue first
  2. Then by priority (P1 → P2 → P3)
  3. Then by due date ascending
  4. Tasks without a due date go last

---

## 4. Out of Scope

- Notifications or reminders
- Recurring tasks
- Multi-user support
- Keyboard navigation / accessibility features
- External storage or backend integration
