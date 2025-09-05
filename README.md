# Daily Task Scheduler

A lightweight, single-file, browser-based daily task scheduler. Add tasks with durations, reorder them, lock tasks to fixed times, automatically show gaps, and save a multi-day history to `localStorage`. No build step required — drop `index.html` into any modern browser and you’re ready.

---

## Quick overview

* Single-file app: `index.html`.
* Works on desktop and mobile (responsive layout).
* Persistent multi-day history stored in the browser.
* Import / Export your entire history as JSON.

---

## Features

* **Task management**

  * Add tasks with a name and duration (minutes).
  * Edit task name & duration.
  * Delete tasks instantly.
  * Punt tasks to the next day by clicking the right arrow (→) button next to each task (clears fixed times to keep tasks flexible).
  * Lock tasks to a fixed start time so they don’t move during auto-scheduling.

* **Reordering**

  * Desktop: drag-and-drop to reorder tasks.
  * Mobile: touch-friendly move buttons (move up / move down).

* **Schedule display**

  * Tasks show computed start and end times.
  * Automatic gap rows inserted between tasks (configurable gap).
  * Currently active task (for today) is highlighted live.

* **Persistence & data interchange**

  * Full schedule history saved to `localStorage`.
  * Import / Export the **entire history** as a single JSON file.

* **Responsive UI**

  * Desktop: multi-column table (Time / Task / Duration / Actions).
  * Mobile: compact rows, action buttons and edit via modal to avoid accidental keyboard popups.

---

## Usage

1. Open `index.html` in a modern browser (Chrome, Firefox, Edge, Safari).
2. Pick a date (top-right) to view/edit that day.
3. Set the day’s start time (default 09:00).
4. Add tasks using the form (name + duration in minutes).
5. Reorder:

   * Desktop: drag the rows.
   * Mobile: use the up / down buttons.
6. Lock a task:

   * Click the lock icon and enter a fixed start time (HH\:MM).
7. Export/import history:

   * Export History → downloads a JSON file of the full saved history.
   * Import History → select a previously exported history JSON to replace the current saved history. (Always keep a backup.)

8. Punt tasks: Click the right arrow (→) button next to any task to punt it to the next day

Tip: Press Enter while focused on the task name or duration inputs to quickly add a task.

---

## Mobile-specific behavior

* Mobile layout collapses columns to a compact card-style row.
* Inline editing is avoided on mobile to prevent the keyboard from opening unexpectedly; use the Edit action which opens an in-page modal.
* Drag-and-drop is disabled on touch to avoid accidental dragging; use move buttons instead.
* Active-task highlighting updates during scroll so you can see the currently active task while navigating the schedule.

---

## JSON format (history)

The exported history is a JSON object keyed by ISO date strings (`YYYY-MM-DD`). Each day entry contains `tasks` (array), `gapDuration`, and `startTime`. Minimal example:

```json
{
  "2025-09-01": {
    "startTime": "09:00",
    "gapDuration": 10,
    "tasks": [
      {
        "id": 1693555200123,
        "name": "Morning review",
        "duration": 30,
        "isFixed": false,
        "fixedStartTime": null
      },
      {
        "id": 1693557000456,
        "name": "Deep work",
        "duration": 120,
        "isFixed": true,
        "fixedStartTime": "10:00"
      }
    ]
  }
}
```

When you import a history JSON file, it replaces the saved history in `localStorage`. Export before importing to keep a backup.

---

## Implementation notes (for developers)

* Single-file app: HTML + CSS + JavaScript in `index.html`.
* Storage keys:

  * History stored under `taskSchedulerHistory_v1` in `localStorage`.
  * A legacy single-day key may exist (`taskSchedulerData_v1`) and is migrated on first load if present.
* Key functions:

  * `calculateSchedule()` — computes start/end times and renders tasks and gaps.
  * `saveCurrentDay()` / `saveHistory()` — persist to `localStorage`.
  * `exportHistory()` / `doImport(parsed)` — history import/export handling.
  * `updateActiveIndicator()` — finds current active task for today and highlights it.
  * `puntTask(taskId)` — punts a task to the next day with comprehensive validation, user confirmation, and error handling.
* Mobile UX helpers:

  * In-page modal (Promise-based) used for confirmations and text input (no native `prompt`/`confirm`).
  * Interaction tracking prevents forced scrolls while the user is actively scrolling/touching.

---

## Known limitations & platform caveats

* Some viewport shifts are caused by browser behavior (virtual keyboard opening, address-bar show/hide) and can be difficult to eliminate entirely. The app tries to minimize these by avoiding native dialogs and inline editing on mobile.
* The import action **replaces** the saved history. Keep backups.
* Drag-and-drop on touch devices is intentionally disabled — use the provided move buttons.

---

## Debugging & customization

* Toggle the `DEBUG` constant in the script to enable console logs for pointer, touch, and interaction events.
* Tweak interaction timing and scroll thresholds in the JS if you want a different feel on specific devices (values are top-level numbers in the script).
* The app is intentionally single-file for simplicity; you can split HTML/CSS/JS for a multi-file project if preferred.
