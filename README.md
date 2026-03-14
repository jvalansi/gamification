# Gamification

A productivity system that assigns monetary value to tasks based on project ROI, letting you "earn" points by completing work and spend them on play time.

## Concept

Each task earns points based on how long it takes, your hourly rate, and the ROI multiplier of its project:

```
task_points ($) = task_hours × hourly_rate ($) × roi_multiplier
```

**Example:** 2hr task, $100/hr rate, 3× ROI project → **$600 in points**

- `task_hours` — estimated duration, set per task in Notion
- `hourly_rate` — your baseline time value (default: $100/hr)
- `roi_multiplier` — set per project in the Notion Projects page (e.g. 1× = break-even, 5× = high-leverage)

Accumulated points can be spent on play time at a chosen rate (e.g. $10 = 1hr).

## Structure

### Notion (source of truth)
- **Tasks DB (משימות)** — all tasks with:
  - `Project` tag (multi-select)
  - `Workload (hrs)` — estimated hours
  - `Status` — To Do / Doing / Done
  - `Top Level` tag — marks epics/parent tasks
- **Projects page** — one row per project with:
  - `ROI Multiplier` — leverage factor (e.g. 3× means the project returns 3× your time investment)
  - `% Complete` — derived from done tasks

### Views
- **All active** — Status ≠ Done, no Top Level filter
- **Top level** — filtered by `Top Level` tag
- **Per project** — filtered by project tag
- **Per project top level** — project tag + `Top Level` tag

## Projects & ROI Estimates

| Project | ROI Multiplier | Status |
|---|---|---|
| earnings-trader | TBD | Active |
| twilio-claude-bot | TBD | Active |
| video-ai-bot | TBD | On hold |
| gamification | — | Meta |

## Play Time Rate

**$10 earned = 1hr play time** (adjust to taste)

## Roadmap

- [ ] Add `Workload (hrs)` column to Notion tasks DB
- [ ] Confirm `ROI Multiplier` column exists on Notion projects page
- [ ] Script to calculate total points earned from completed tasks
- [ ] Slack command `/points` — show current balance
- [ ] Auto-post points earned when a task is marked done
- [ ] Weekly summary posted to `#proj-gamification`
