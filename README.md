# Gamification

A productivity system that assigns monetary value to tasks based on project ROI, letting you "earn" points by completing work and spend them on play time.

## Concept

Each task earns points based on its ROI per hour:

```
task_points ($) = task_workload_hrs × roi_per_hour ($/hr)
```

Accumulated points can be spent on play time at a chosen rate (e.g. $10 = 1hr).

### How ROI/hr is estimated

**Option A — Direct (default):** Assign `roi_per_hour` directly to each task based on gut feel or market rate.

**Option B — Project-anchored (preferred):** Derive ROI/hr from a project-level goal:

```
roi_per_hour = project_roi ($) / project_total_hrs
task_points  = task_workload_hrs × roi_per_hour
```

This makes estimates principled — each task's value flows from a well-defined project goal rather than being guessed in isolation. The hierarchy is intentionally shallow (project → task, no intermediate steps layer) to keep overhead low.

## Structure

### Notion (source of truth)
- **Tasks DB (משימות)** — all tasks with:
  - `Project` tag (multi-select)
  - `Workload (hrs)` — estimated hours
  - `Status` — To Do / Doing / Done
  - `Top Level` tag — marks epics/parent tasks
- **Projects page** — one row per project with:
  - `Expected ROI ($)` — monthly value if complete
  - `Total workload (hrs)` — sum of all task hours
  - `% Complete` — derived from done tasks
  - `Points Earned ($)` — formula: `% complete × ROI`

### Views
- **All active** — Status ≠ Done, no Top Level filter
- **Top level** — filtered by `Top Level` tag
- **Per project** — filtered by project tag
- **Per project top level** — project tag + `Top Level` tag

## Projects & ROI Estimates

| Project | Expected ROI ($/mo) | Status |
|---|---|---|
| earnings-trader | TBD | Active |
| twilio-claude-bot | TBD | Active |
| video-ai-bot | TBD | On hold |
| gamification | — | Meta |

## Play Time Rate

**$10 earned = 1hr play time** (adjust to taste)

## Roadmap

- [ ] Add `Workload (hrs)` column to Notion tasks DB
- [ ] Add ROI columns to Notion projects page
- [ ] Script to calculate total points earned from completed tasks
- [ ] Slack command `/points` — show current balance
- [ ] Auto-post points earned when a task is marked done
- [ ] Weekly summary posted to `#proj-gamification`
