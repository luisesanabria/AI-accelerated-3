# Epics and Stories - TODO App Upgrade

## MVP Requirements

### Epic: Enrich Task Data Model

#### Story: Add optional due date to task create/edit flows

##### Acceptance Criteria
- A user can set a due date when creating a task.
- A user can update or remove a due date when editing a task.
- Due dates are stored and returned in ISO `YYYY-MM-DD` format.
- If no due date is provided, the task is stored without a due date.

##### Technical Requirements
- Frontend: Add or keep a date input in `TaskForm` and send `due_date` in create/edit payloads.
- Frontend: Normalize incoming date values for edit mode so existing tasks display correctly in the date control.
- Backend: Ensure `tasks` table supports nullable `due_date` and accepts null/ISO date values in `POST /api/tasks` and `PUT /api/tasks/:id`.
- Backend: Preserve existing response shape from `/api/tasks` and `/api/tasks/:id` so current list/detail rendering remains compatible.

#### Story: Add task priority field with default P3

##### Acceptance Criteria
- A task has a priority value constrained to `P1`, `P2`, or `P3`.
- New tasks default to `P3` when priority is not explicitly selected.
- Editing a task allows changing priority among valid values only.
- Existing tasks without priority are treated as `P3` in the UI.

##### Technical Requirements
- Frontend: Add a priority selector in `TaskForm` limited to `P1|P2|P3`.
- Frontend: Include `priority` in create/edit requests and default to `P3` if missing.
- Frontend: Update `TaskList` rendering to surface priority consistently.
- Backend: Add `priority` column with default `P3` and enforce valid values at API validation level.
- Backend: Return `priority` in all task payloads.

### Epic: Deliver Date-Based Task Filtering

#### Story: Add filter controls for All, Today, and Overdue

##### Acceptance Criteria
- The UI includes filter options: `All`, `Today`, and `Overdue`.
- `All` shows all tasks regardless of due date.
- `Today` shows tasks with due date equal to the current local date.
- `Overdue` shows tasks with due date earlier than the current local date.

##### Technical Requirements
- Frontend: Add filter state and controls in `App` or `TaskList` with clear visual selected state.
- Frontend: Update task-fetch strategy to either request filtered data from API or filter client-side from `/api/tasks` results.
- Frontend: Centralize date comparison logic to avoid timezone drift for `Today` and `Overdue` checks.
- Backend (if server-side filtering selected): Extend `GET /api/tasks` query handling to support `filter=today|overdue|all`.

#### Story: Exclude completed tasks from Today and Overdue views

##### Acceptance Criteria
- In `Today` and `Overdue` views, completed tasks are not displayed.
- In `All` view, completed tasks remain visible.
- Toggling task completion updates visibility immediately according to active filter.

##### Technical Requirements
- Frontend: Apply completion-state filtering after completion toggle without full-page reload.
- Frontend: Reuse existing `PATCH /api/tasks/:id` flow and refresh list state after updates.
- Backend (if query-based filtering selected): Support combining completion criteria with date filter logic.

### Epic: Enforce MVP Data Validation Rules

#### Story: Validate title, priority, and due-date inputs

##### Acceptance Criteria
- Title is required for create and edit actions.
- Priority accepts only `P1`, `P2`, `P3`; invalid values are rejected.
- Due date is optional; invalid date values are ignored and treated as no due date.
- Validation failures return user-visible errors and do not create/update data.

##### Technical Requirements
- Frontend: Keep required validation for title and show inline validation feedback.
- Frontend: Prevent invalid priority options by constraining UI inputs.
- Frontend: Sanitize due-date values before submission and clear invalid entries.
- Backend: Validate request payloads in `POST /api/tasks` and `PUT /api/tasks/:id`, returning `400` for invalid title/priority.
- Backend: Coerce invalid due-date input to null (absent) per product rule.

## Post-MVP Requirements

### Epic: Improve Visual Prioritization and Urgency Cues

#### Story: Highlight overdue tasks with a distinct visual treatment

##### Acceptance Criteria
- Overdue tasks are visually highlighted in task list rows.
- Highlighting is applied only to incomplete overdue tasks.
- Highlight style is consistent across desktop and mobile layouts.

##### Technical Requirements
- Frontend: Add conditional styling in `TaskList` based on computed overdue status and completion state.
- Frontend: Keep color contrast compliant with existing UI guidelines and do not break current completed styling.
- Frontend: Add component tests covering overdue highlight rendering.

#### Story: Show color-coded priority badges in the list

##### Acceptance Criteria
- Task rows display priority badges for `P1`, `P2`, and `P3`.
- Badge colors follow agreed mapping and remain readable.
- Badge rendering updates immediately after editing priority.

##### Technical Requirements
- Frontend: Add badge UI element in `TaskList` and map priority values to theme colors.
- Frontend: Reuse existing MUI components (`Chip`/`Box`) to stay consistent with current design system.
- Frontend: Add tests for priority badge rendering and updates.

### Epic: Implement Advanced Sort Order

#### Story: Sort tasks as overdue first, then priority, then due date, undated last

##### Acceptance Criteria
- Overdue tasks appear before non-overdue tasks.
- Within each group, `P1` appears before `P2`, and `P2` before `P3`.
- For same priority, tasks are sorted by due date ascending.
- Tasks without due date are shown after dated tasks.

##### Technical Requirements
- Frontend: Implement deterministic comparator utility for advanced sorting.
- Frontend: Apply sorting before rendering so behavior is consistent across filters.
- Backend (optional optimization): Add equivalent ORDER BY logic when requesting pre-sorted lists.
- Testing: Add unit tests for comparator edge cases (missing dates, equal priorities, mixed completion states).

#### Story: Preserve stable ordering for ties using creation timestamp

##### Acceptance Criteria
- Tasks with identical overdue state, priority, and due date keep predictable relative order.
- The tie-break behavior is documented and test-covered.

##### Technical Requirements
- Backend: Ensure `created_at` is returned and available as tie-break metadata.
- Frontend: Use `created_at` as final tie-break in sorting utility.
- Testing: Add integration tests verifying stable order with tied sort attributes.
