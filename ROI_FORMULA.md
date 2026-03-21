# ROI Formula — Design Discussion

This doc captures the design space for the ROI formula used to rank projects and weight task points.

---

## The Core Question

How do you combine financial return, probability of success, effort required, and enjoyment of the work into a single number that tells you what to work on next?

---

## Approaches Considered

### 1. 1–5 Log Scale Scoring (original)

**Formula:**
```
$ = 10^(Impact × 2)          # expected monetary value
% = 10^((Feasible − 5) × 2)  # probability of success
R = $ × %
Time = 10^(5 − Speed)         # time investment (inverted)
Quality = (Productive + Fun) / 5
ROI = (R + Quality × Time × 100) / (Time × 100)
```

All inputs are 1–5 scores. The formula is fully automatic once scores are set.

**Pros:**
- Single number, comparable across wildly different scale projects
- Captures both financial and quality-of-time dimensions
- Handles orders-of-magnitude differences naturally (log scale)

**Cons:**
- Abstract — Impact=4 means "$100M potential", hard to intuit
- Log scale makes it hard to know what score to assign
- Financial and enjoyment terms are combined in an arbitrary way
- Intermediate formula columns ($, %, Time, R) add noise

---

### 2. Raw Numbers, Quality as Filter

**Formula:**
```
ROI = Yearly Revenue × Probability / Hours
```

Fun (Type 1 + Type 2) used as a hard filter — discard any project below a minimum threshold before ranking.

**Pros:**
- ROI is pure financial, easy to interpret ($/hr of expected annual return)
- Fun as filter mirrors good decision-making ("culture fit is a filter, comp is what you optimize")
- No need to convert enjoyment into dollars

**Cons:**
- Loses nuance — a project just above the fun threshold ranks the same as one you love
- Financial ROI doesn't capture the value of working on something enjoyable right now
- Two separate systems to maintain

---

### 3. Raw Numbers, Quality in Formula

**Formula:**
```
ROI = (Yearly Revenue × Probability + Quality × Hours × Hourly Rate) / (Hours × Hourly Rate)
    = (Yearly Revenue × Probability / (Hours × Hourly Rate)) + Quality
```

Quality of time (fun/learning) adds directly to the financial return, both in dollars.

**Pros:**
- Single metric
- Principled: enjoyment has real dollar value (it saves you from having to buy leisure)
- Consistent with the original formula's spirit, just with clearer units

**Cons:**
- Requires choosing an hourly rate as the conversion factor
- Still combines two different things into one number, making it harder to sanity-check

---

### 4. Unified Time-as-Currency (philosophical foundation)

**Insight:** Money and time are the same thing. Money buys time — free time (you don't need to work) and quality time (entertainment, location, food). Therefore fun and revenue are both returns, just paid at different times.

**Conversion factors:**
- `N` = annual cost of living (e.g. $50k/year) — 1 year of revenue = 1 year free
- `F` = cost per hour of leisure (e.g. $10/hr) — $1 = 1/F hours of fun

**Formula:**
```
Value = fun_score × hours                        # quality time earned while working
      + revenue × probability × years / N × 8760 # free hours bought by revenue
```

Both terms are in "hours of quality time." Since N, F, and 8760 are constants, rankings don't change if you drop them — which collapses back to:

```
ROI = fun_score + revenue × probability × years / hours
```

**Pros:**
- Philosophically unified — everything is the same currency
- Justifies why fun belongs in the same formula as revenue
- No arbitrary weighting needed

**Cons:**
- More abstract to explain to others
- Requires agreeing on N and F (personal constants)
- "Years" parameter still needed to convert recurring revenue to a total

---

## Summary

| Approach | Formula complexity | Intuitive? | Fun included? | Recommended for |
|---|---|---|---|---|
| 1–5 log scale | High | No | Yes (baked in) | Comparing wildly different scale ideas |
| Raw + filter | Low | Yes | As threshold | Pure financial prioritization |
| Raw + quality in formula | Medium | Mostly | Yes (additive) | Balanced single metric |
| Time-as-currency | Medium | Philosophically yes | Yes (principled) | Full unified model |

---

## Chosen Formula

```
ROI = (total_revenue × probability) / (work_weeks × (1 - fun_score) × work_week_value)
```

Where `work_week_value = $4,000` and `work_weeks = work_hours / 40`.

**Intuition:** ROI is expected $ return divided by $ cost of unfun work invested. A pure monetary ratio — everything stays in the same currency.
- **Numerator** — total expected dollar value over the project's lifetime, scaled by probability
- **Denominator** — dollar cost of the unfun work to reach steady state (fun hours are free; only unfun ones cost you)

**Variables:**

| Variable | Description |
|---|---|
| `total_revenue` | Total expected revenue over project lifetime ($) |
| `probability` | 0–1, estimated chance the project succeeds |
| `work_weeks` | Estimated weeks to reach steady state (= work_hours / 40) |
| `fun_score` | 0–1, combined Type 1 (enjoyment now) + Type 2 (rewarding after) |
| `work_week_value` | Dollar value of one work week (default: $4,000) |

**Special case:** if `fun_score = 1`, don't calculate — just do it. The formula doesn't apply.

**Notes:**
- Using money as the common unit means ROI can be interpreted directly: ROI > 1 means the project pays for itself in under a year; ROI = 7 means $7 expected return per $1 of unfun work invested
- `work_week_value` is a personal constant — changing it scales all ROIs equally, so rankings are unaffected; it only matters for absolute interpretation
- Fun reduces the *cost* of work rather than adding to the *return* — more truthful than additive approaches
- Rankings are stable under linear transformations (e.g. multiplying revenue by a valuation multiple for exit value)
