# Gamification

A productivity system that assigns monetary value to tasks based on project ROI, letting you "earn" points by completing work and spend them on play time.

## Concept

Each project has an **expected ROI** (monthly $ value). Each task represents a % of that project's completion. Completing a task earns you theoretical dollars:

```
task_points ($) = (task_workload_hrs / project_total_hrs) × project_roi ($)
```

Accumulated points can be spent on play time at a chosen rate (e.g. $10 = 1hr).

## Structure

### Notion (source of truth)
- **משימות (Tasks DB)** — all tasks with:
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
