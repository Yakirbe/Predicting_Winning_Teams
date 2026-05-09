# Manager-style simulation — 5 phases

Run this AFTER you have gathered the 8 signals. The simulation is a structured think-through, not a stochastic engine — the goal is a calibrated final scoreline + 1/X/2 distribution + confidence.

# Phase structure

Each phase covers a window of the match and produces three outputs.

| Phase | Window  | Focus                                             |
|-------|---------|---------------------------------------------------|
| P1    | 0–15    | Tactical setup, who imposes the early game        |
| P2    | 15–45   | Patterns settle, set pieces, first-half goals     |
| P3    | 45–60   | Adjustments at HT, who comes out sharper          |
| P4    | 60–75   | Substitutions, fitness, momentum swings           |
| P5    | 75–90+  | Game-state effects (chasing, killing it off)      |

For each phase, write 2–4 sentences describing:
- **xG delta** — qualitative (e.g., "Home edge ~0.3 xG vs 0.1") or quantitative if you have data
- **Key events** — goals, red cards, tactical changes
- **Post-phase momentum** — who's on top heading into the next phase

# Inputs that bias each phase

- **Predicted XI quality gap** → P1 strongly, P2 moderately
- **Injuries to creators / finishers** → P2, P3, P4
- **Suspensions in defense** → all phases (set-piece risk)
- **Momentum (last-5 form)** → P1, P5 (confidence in tight moments)
- **Fan sentiment & attendance** → P1, P5 (home crowd effect)
- **Stakes** → P5 most heavily (does a side need a win?)
- **H2H pattern** → tiebreak when phases are even

# Deriving the final outputs

After P5, produce:

## 1. `sim_score_modal`
The single most-likely final scoreline. Examples: `"1-1"`, `"2-0"`, `"2-1"`.

## 2. `sim_score_distribution`
Probability mass over the 4–5 most likely scorelines. Must sum to 1.0 (or close — `0.95+` is acceptable, the residual is "other"). Example:
```json
{
  "1-1": 0.22,
  "2-1": 0.18,
  "1-0": 0.15,
  "2-2": 0.12,
  "0-1": 0.10,
  "other": 0.23
}
```

## 3. `predicted_outcome`
One of `"1"`, `"X"`, `"2"`. Derive by summing the distribution:
- Home wins → all scorelines where home > away
- Draws → all where home == away
- Away wins → all where home < away
The largest of those three sums is your `predicted_outcome`.

## 4. `confidence_pct`
Integer 0–100. Use this rubric:
- **75–90**: clear favorite, fresh data, ≥3 sources, no critical injuries on the favored side
- **60–74**: edge but plausible counter-cases, fresh data
- **45–59**: real coin-flip, or favorite has a key absence
- **30–44**: leaning but stale data or major data gaps
- **<30**: don't emit — return `"unknown"` predictions and explain in `narrative`

If `data_freshness == "stale"`, **subtract 10** from your confidence.

# Quality checks before emitting

1. Does the modal scoreline match the highest single-cell probability in the distribution? It should.
2. Does `predicted_outcome` match the largest of the three summed buckets (1 / X / 2)?
3. Are key absences from signals 2 + 3 reflected in the narrative?
4. Did stakes (signal 7) influence P5? If a relegation-threatened side is at home, that should show.
5. Three or more distinct sources cited?

If any check fails, fix before emitting.
