# Output schema — strict JSON row

Emit ONLY a fenced ```json block matching this schema. No prose before or after the block. The orchestrator parses the block; extra text breaks parsing.

# Schema

```json
{
  "match_id": "string — slug like 'hapoel-ta-vs-maccabi-ta-2026-05-09'",
  "league": "string — one of: Israeli Premier League | Premier League | La Liga | Bundesliga | Ligue 1",
  "home": "string — full team name",
  "away": "string — full team name",
  "kickoff_local": "string — ISO datetime in local TZ, or 'unknown'",

  "predicted_xi_home": {
    "formation": "string — e.g. '4-3-3'",
    "players": ["GK name", "RB name", "CB name", "CB name", "LB name", "CM", "CM", "CM", "RW", "ST", "LW"]
  },
  "predicted_xi_away": {
    "formation": "string",
    "players": ["...11 names or 'unknown' per slot..."]
  },

  "key_absences": {
    "home_injuries": [{"name": "string", "position": "string", "expected_return": "string|unknown"}],
    "home_suspensions": [{"name": "string", "position": "string", "reason": "string"}],
    "away_injuries": [{"name": "string", "position": "string", "expected_return": "string|unknown"}],
    "away_suspensions": [{"name": "string", "position": "string", "reason": "string"}]
  },

  "momentum_home": {"last_5": "string — e.g. 'WWDLW'", "summary": "string — 1 sentence"},
  "momentum_away": {"last_5": "string", "summary": "string"},

  "fan_sentiment": {
    "home": {"label": "confident|split|anxious", "summary": "string — 1 sentence"},
    "away": {"label": "confident|split|anxious", "summary": "string"}
  },

  "expected_attendance": "string — numeric or relative, e.g. '32000' or 'near capacity'",

  "stakes": {
    "home": "string — 1 sentence",
    "away": "string — 1 sentence"
  },

  "h2h_and_venue": {
    "last_5_h2h": "string — e.g. 'H WWDLW' compact or short prose",
    "home_form_at_home": "string",
    "away_form_on_road": "string"
  },

  "market_odds": {
    "polymarket": {
      "home_win_pct": 0,
      "draw_pct": 0,
      "away_win_pct": 0,
      "url": "string|unknown",
      "liquidity_note": "string|unknown"
    },
    "aggregator_implied_pct": {"home": 0, "draw": 0, "away": 0, "source": "string|unknown"},
    "divergence_vs_sim": "string — 1 sentence on whether market and sim agree"
  },

  "simulation_phases": {
    "p1_0_15": "string — 2-4 sentences",
    "p2_15_45": "string",
    "p3_45_60": "string",
    "p4_60_75": "string",
    "p5_75_90": "string"
  },

  "sim_score_modal": "string — e.g. '1-1', '2-1'",
  "sim_score_distribution": {
    "scoreline_1": 0.0,
    "scoreline_2": 0.0,
    "scoreline_3": 0.0,
    "scoreline_4": 0.0,
    "scoreline_5": 0.0,
    "other": 0.0
  },

  "predicted_outcome": "1|X|2",
  "confidence_pct": 0,

  "key_factors": ["string — bullet, English, max 10 words", "...", "..."],
  "narrative": "string — 3-5 sentence English summary",

  "data_freshness": "fresh|stale",
  "sources": ["url1", "url2", "url3", "..."]
}
```

# Required fields

All top-level keys are required. Use `"unknown"` for any individual string field you can't fill. Use empty arrays for the `home_injuries` / `home_suspensions` / etc. arrays only if you confirmed the team is at full strength — never use `[]` to hide a data gap.

# Validation rules

1. `predicted_outcome` ∈ `{"1", "X", "2"}`.
2. `confidence_pct` is an integer 0–100.
3. `sim_score_distribution` values sum to ≤ 1.0 with `other` capturing residual.
4. `sources` has ≥ 3 distinct URLs.
5. `data_freshness` is `"fresh"` only if all time-sensitive signals (XI / injuries / suspensions) come from articles dated within 14 days of 2026-05-09.
6. `key_factors` has 3–6 bullets.
