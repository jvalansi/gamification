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

## Current Direction

The time-as-currency framing (Approach 4) provides the best theoretical justification for including fun in the formula. In practice it simplifies to:

```
ROI = fun_score + (yearly_revenue × probability × years) / hours
```

Where `years` is a fixed horizon (e.g. 3 years) applied consistently across all projects. Since it's a constant multiplier, it doesn't affect rankings — so in practice:

```
ROI = fun_score + yearly_revenue × probability / hours
```

**Open question:** what is the right weight between the fun term and the financial term? Currently they add with equal weight, but an hour of highly fun work (fun_score=1.0) contributing the same as one unit of financial ROI may or may not feel right.
