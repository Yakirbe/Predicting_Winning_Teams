---
name: match-analysis
description: Methodology for analyzing a single football (soccer) match — which sports sites to query (365scores, Opta, Sofascore, WhoScored, Transfermarkt, club sites, fan forums), how to extract the eight required signals (predicted XI, injuries, suspensions, momentum, fan sentiment, expected attendance, stakes, H2H/venue), how to run a 5-phase manager-style simulation, and the strict JSON output schema. Use whenever predicting the outcome of one football match.
---

# Match analysis methodology

You are analyzing **one** football match. Today is **2026-05-09**. Treat any data older than 14 days as stale.

This skill uses progressive disclosure — three sibling files give the operational detail. Read them on demand:

1. **`data_sources.md`** — for each of the 8 signals, the source priority list and the WebSearch query template.
2. **`simulation_template.md`** — the 5-phase manager simulation (0–15, 15–45, 45–60, 60–75, 75–90+) plus how to derive a modal scoreline and a 1/X/2 distribution.
3. **`output_schema.md`** — the exact JSON row schema you must emit. Read this file in full before producing the final output.

# The 8 signals + market

| # | Signal              | What you extract                                                            |
|---|---------------------|------------------------------------------------------------------------------|
| 1 | Predicted XI        | Formation + 11 names per side (or `"unknown"` per slot)                      |
| 2 | Injuries            | Players ruled out, with positions                                            |
| 3 | Suspensions         | Players banned (cards, accumulation), with positions                         |
| 4 | Momentum            | Last 5 W/D/L per side, current form, key shifts                              |
| 5 | Fan sentiment       | Categorical: confident / split / anxious — plus a 1-line summary             |
| 6 | Expected attendance | Numeric or capacity-relative ("near capacity", "~70%")                       |
| 7 | Stakes              | What each side is playing for given the table                                |
| 8 | H2H / venue context | Recent head-to-head, home form vs away form                                  |
| 9 | Market odds (NEW)   | Polymarket implied probs for 1/X/2 + odds aggregator cross-check              |

# Source localization (NEW)

`data_sources.md` now organizes per-league bundles (Israeli Premier League, Premier League, La Liga, Bundesliga, Ligue 1). Pick the bundle matching the match's league FIRST, then layer the global sources (Sofascore, Opta, Transfermarkt) and Polymarket on top. For Israeli matches use Hebrew-language sources (ONE, Sport5, YNet, Walla, Maariv).

# Running the simulation

Open `simulation_template.md`. Step through the 5 phases. Each phase produces:
- xG delta (home vs away in that window)
- key events (goals, red cards, tactical shifts)
- post-phase momentum

At the end, derive:
- `sim_score_modal`: most likely scoreline (e.g., `"1-1"`, `"2-1"`)
- `sim_score_distribution`: probability mass over the 4–5 most likely scorelines
- `predicted_outcome`: one of `"1"` (home win), `"X"` (draw), `"2"` (away win)
- `confidence_pct`: integer 0–100

# Source discipline

- Cite **≥3 distinct sources** in `sources[]`.
- Prefer Opta / official club / 365scores / Sofascore / WhoScored / Transfermarkt over fan forums.
- Use fan forums for sentiment only.
- Never fabricate names or numbers — write `"unknown"` if uncertain.
- If anything material is older than 14 days, set `data_freshness: "stale"` and reduce confidence.

# Output

Emit only a fenced ```json block matching `output_schema.md`. Do not write prose before or after.
