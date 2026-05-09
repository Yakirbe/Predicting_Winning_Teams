# Data sources — per signal

For each signal, query the sources in priority order. Stop once you have a credible answer corroborated by at least one secondary source. Record the URLs you actually used in `sources[]`.

# League-localized sources (use these FIRST per match)

Pick the bundle matching the match's league. Always layer the global sources (Sofascore, Opta, Transfermarkt) on top, plus Polymarket for market-implied odds.

## Israeli Premier League
- ONE — `site:one.co.il`
- Sport5 — `site:sport5.co.il`
- YNet Sport — `site:ynet.co.il/sport`
- Walla Sport — `site:sports.walla.co.il`
- Maariv Sport — `site:sport.maariv.co.il`
- Israeli FA official — `site:football.org.il`
- Reddit — `r/IsraeliFootball` (sentiment only)
- X/Twitter accounts: club official handles, beat reporters in Hebrew

## Premier League (England)
- BBC Sport — `site:bbc.com/sport/football`
- Sky Sports — `site:skysports.com/football`
- The Athletic — `site:theathletic.com`
- The Guardian football — `site:theguardian.com/football`
- Premier League official — `site:premierleague.com`
- Reddit — `r/PremierLeague`, club-specific subs (`r/Gunners`, `r/coys`, `r/MCFC`, `r/LiverpoolFC`, etc.)

## La Liga (Spain)
- Marca — `site:marca.com`
- AS — `site:as.com`
- Mundo Deportivo — `site:mundodeportivo.com`
- Sport (Catalan) — `site:sport.es`
- LaLiga official — `site:laliga.com`
- Reddit — `r/LaLiga`, club subs (`r/realmadrid`, `r/Barca`, `r/atletico`)

## Bundesliga (Germany)
- Kicker — `site:kicker.de`
- Sport1 — `site:sport1.de`
- Bild Sport — `site:bild.de/sport`
- Bundesliga official — `site:bundesliga.com`
- Reddit — `r/Bundesliga`, club subs (`r/borussiadortmund`, `r/fcbayern`)

## Ligue 1 (France)
- L'Équipe — `site:lequipe.fr`
- RMC Sport — `site:rmcsport.bfmtv.com`
- Foot Mercato — `site:footmercato.net`
- Le10Sport — `site:le10sport.com`
- Ligue 1 official — `site:ligue1.com`
- Reddit — `r/ligue1`

# Market-implied probabilities (NEW — use for every match)

After gathering the 8 signals, also pull market-implied probabilities to calibrate confidence:

## Polymarket
- `site:polymarket.com <home> <away>` — look for active markets on this match
- Extract: implied probability for 1 / X / 2 (or moneyline if that's the market shape)
- Note timestamp and liquidity

## Betting odds aggregators (secondary)
- OddsPortal — `site:oddsportal.com <league> <home> <away>`
- 365scores odds tab

**How to use market data:** if your simulation's `predicted_outcome` matches the market favorite within 10 percentage points, that's confirming signal — keep confidence as derived. If they diverge by >15 pp, lower confidence by 5–10 and explain the divergence in `narrative`. Record the market URLs in `sources[]`.

## 1. Predicted XI

**Priority sources:**
1. 365scores — `site:365scores.com "<home> vs <away>" lineup`
2. Sofascore — `site:sofascore.com <home> <away> lineup`
3. WhoScored — `site:whoscored.com <home> <away> preview`
4. Official club site — `site:<club-domain> team news` (search for the club's domain first)
5. Local press — for Israeli matches: ONE / Sport5 / YNet Sport

**Query templates:**
- `"<home> vs <away> predicted lineup <league> 2025/2026"`
- `"<home> probable XI <kickoff date>"`
- `"<away> starting eleven <kickoff date>"`

**What to extract:** formation (e.g., `4-3-3`), 11 names per side. If a slot is uncertain, write `"unknown"`. If a name is partially known (e.g., last name only), record what you have.

## 2. Injuries

**Priority sources:**
1. Transfermarkt — `site:transfermarkt.com <club> injury`
2. Official club site
3. PhysioRoom — `site:physioroom.com <club>`
4. Beat reporters on X/Twitter (last 7 days only)

**What to extract:** player name, position, expected return.

## 3. Suspensions

**Priority sources:**
1. League official site (yellow-card accumulation table, red-card reports)
2. Transfermarkt suspensions table
3. Match preview articles

**What to extract:** player name, position, reason (5 yellows, red card, etc.), how many matches out.

## 4. Momentum

**Priority sources:**
1. League table page (recent form column) — `site:<league-site> standings`
2. Sofascore team page — last 5 matches
3. FBref — `site:fbref.com <club>`

**What to extract:** last 5 results as W/D/L string (e.g., `WWDLW`), goal difference over those 5, any visible streak.

## 5. Fan sentiment

**Priority sources:**
1. Reddit subreddit for the club (e.g., r/Gunners, r/coys, r/MCFC) — sort by "new", read top 5 threads from last 48h
2. Official club X/Twitter mentions
3. Israeli matches: Sport5 + ONE comment sections, fan WhatsApp clips reported in press

**What to extract:** one of `confident` / `split` / `anxious`, plus a 1-line summary in English.

## 6. Expected attendance

**Priority sources:**
1. Wikipedia — stadium capacity
2. Recent league attendance reports (last 3 home games)
3. Local press for sellout/boycott announcements

**What to extract:** numeric estimate or capacity-relative phrase ("near capacity", "~70%", "reduced — fan boycott").

## 7. Stakes

**Priority sources:**
1. Current league standings page
2. Title-race / European-spot / relegation-fight previews

**What to extract:** one sentence per side. Examples: "fighting for direct Champions League spot, 2 pts behind 4th", "already safe, mid-table, no European chase", "must-win to escape relegation playoff".

## 8. H2H / venue context

**Priority sources:**
1. 365scores / Sofascore "head to head" tab
2. Wikipedia rivalry page (if applicable)

**What to extract:** last 5 H2H results, home team's home record this season, away team's away record this season.

# Search hygiene

- Always include the **current season** ("2025/2026") in queries when ambiguity is possible.
- Include the **kickoff date** in queries about lineups and injuries — preview articles rot fast.
- If a result page has no date or is older than 14 days for a time-sensitive signal (XI/injuries/suspensions), set `data_freshness: "stale"`.
- For Israeli teams, prefer Hebrew-language sources (ONE, Sport5, YNet Sport) — translate to English when emitting the row.
