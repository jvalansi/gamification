# Gamification

A productivity system that assigns monetary value to tasks based on project ROI, letting you "earn" points by completing work and spend them on play time.

## Concept

> For a detailed discussion of ROI formula approaches and design tradeoffs, see [ROI_FORMULA.md](ROI_FORMULA.md).

Each task earns points based on how long it takes and the ROI of its project:

```
task_points = task_hours × project_roi
```

Where project ROI is calculated as:

```
project_roi = (total_revenue × probability) / (work_weeks × (1 - fun_score) × work_week_value)
```

- `total_revenue` — total expected revenue over project lifetime ($)
- `probability` — 0–1, estimated chance the project succeeds
- `work_weeks` — estimated weeks to reach steady state (= work hours / 40)
- `fun_score` — 0–1, combined Type 1 (enjoyable now) + Type 2 (rewarding after) fun
- `work_week_value` — dollar value of one work week (default: $4,000)

ROI is a pure ratio: **expected $ return per $ of unfun work invested.**

**Special case:** if `fun_score = 1`, the project is intrinsically worth doing — no ROI calculation needed.

See [ROI_FORMULA.md](ROI_FORMULA.md) for the full design discussion.

Accumulated points can be spent on play time at a chosen rate (e.g. $10 = 1hr).

## Structure

### Notion (source of truth)
- **Tasks DB (משימות)** — all tasks with:
  - `Project` tag (multi-select)
  - `Workload (hrs)` — estimated hours
  - `Status` — To Do / Doing / Done
  - `Top Level` tag — marks epics/parent tasks
- **Projects page (פרויקטים)** — one row per project with:
  - `Yearly Revenue ($)` — expected annual revenue at steady state
  - `Probability` — 0–1, estimated success probability
  - `Work Hours` — estimated hours to reach steady state
  - `Fun Score` — 0–1, Type 1 + Type 2 fun combined
  - `ROI` — calculated automatically from the above

### Views
- **All active** — Status ≠ Done, no Top Level filter
- **Top level** — filtered by `Top Level` tag
- **Per project** — filtered by project tag
- **Per project top level** — project tag + `Top Level` tag

## Projects & ROI Estimates

| Project | Total Revenue | Probability | Work Weeks | Fun | ROI | Status |
|---|---|---|---|---|---|---|
| earnings-trader | $240,000 | 35% | 0.75 | 0.6 | 70 | Active — paper trading |
| slack-claude-bot | $60,000 | 40% | 1.5 | 0.5 | 8.0 | Active — personal use |
| twilio-claude-bot | $36,000 | 30% | 1.25 | 0.55 | 4.8 | Active — prototype |
| video-ai-bot | $360,000 | 35% | 3.75 | 0.75 | 33.6 | In progress |
| gamification | $36,000 | 80% | 0.5 | 0.75 | 57.6 | Meta |

## Play Time Rate

**$10 earned = 1hr play time** (adjust to taste)

## Roadmap to Profitability

**Current state:** Concept defined, Notion DB structured, no automation yet.

| Milestone | Description | Expected Monthly ROI |
|---|---|---|
| **Close the feedback loop** | Weight points by ROI field so high-value tasks earn more | $300/mo indirect |
| **Automation** | Script to sync completed Notion tasks → points balance | — |
| **Slack integration** | `/points` command, auto-post on task completion, weekly summary | — |
| **Productize** | SaaS for indie hackers / solo founders with their own Notion | $50–100/mo |

**Next step (Notion task):** Connect point values to project ROI metrics — align incentives with actual business value.

**Notion project page:** [gamification — Strategy & Profitability](https://www.notion.so/gamification-Strategy-Profitability-32505a1b5e01819b9b62cb1825f09a54)

---

## Roadmap

- [x] Add `Workload (hrs)` column to Notion tasks DB
- [ ] Confirm `ROI Multiplier` column exists on Notion projects page
- [ ] Script to calculate total points earned from completed tasks
- [ ] Slack command `/points` — show current balance
- [ ] Auto-post points earned when a task is marked done
- [ ] Weekly summary posted to `#proj-gamification`
