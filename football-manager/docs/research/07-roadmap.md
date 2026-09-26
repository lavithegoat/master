# 07. Proposed build stages

Each stage ends with something runnable and a test that proves it. The order is set by dependencies, not by what is most fun: the world must simulate on its own before the user gets a pretty match view.

The biggest scheduling risk is the tier-3 spatial match engine (Stage 9) and the AI transfer market (Stage 7) [Guessing, based on the complaint patterns in 01 and 04].

| # | Stage | Delivers | Exit criteria |
|---|---|---|---|
| 0 | **Research** (this) | Docs in `docs/research/` | Decisions in the README accepted or changed |
| 1 | **Foundation** | Monorepo, strict TS, seeded RNG streams, core types, save format with `schemaVersion`, `sim-cli` skeleton, CI | Determinism test passes: the same seed gives byte-identical output |
| 2 | **World generation** | Nations with profiles, name generator (CC0 data), clubs with reputation, finances and facilities, players with attributes, positions, CA/PA and personality, basic staff | Generate a 20-league world in under X s. Attribute and CA distributions look plausible in a CLI report |
| 3 | **Competitions and calendar** | League config format, round-robin fixtures, domestic cups, promotion/relegation, play-offs, per-league tie-breakers, season rollover, event calendar | Sim 10 seasons with random results. Tables, promotions and cups are all valid. Unit tests for each tie-breaker order |
| 4 | **Quick match engine + calibration** | Tier 1 (score model) and tier 2 (minute chance model, per-player stats, cards, injuries, **match ratings**). The `MatchResult` contract. Calibration harness against openfootball results | 50-season sim. Goals/match, home/draw/away split and scoreline distribution sit within tolerance of real data. CI fails on drift |
| 5 | **Player lifecycle** | Weekly development, ageing curves by attribute group, injuries (type and duration table), retirement, youth intake, basic morale and form | 30-season sim. The world does not collapse or inflate: mean CA of top leagues is stable ±Y, and age distributions look realistic |
| 6 | **Playable shell (UI v1)** | Worker bridge, new game, "Continue" loop, inbox, squad table, player profile, league table, fixtures, basic tactic picker, save/load | A human can play a full season in the browser. Playwright smoke test |
| 7 | **Transfers, contracts, finances, AI squad building** | Valuation, AI needs, bids, negotiations, contracts, loans, free agents, club ledger, budgets, board budget allocation | 20-season sim: AI squads stay balanced, no club hoards 60 players, finances don't all bankrupt or all go infinite |
| 8 | **Tactics system** | Formations, **IP/OOP shapes and roles** (FM26 model), mentality, team and player instructions, familiarity, which feed the tier-2 engine | Measurable effects: pressing raises fouls and turnovers, a deep line lowers opposition shot distance, and so on |
| 9 | **Spatial match engine (tier 3) + 2D view** | Agents at 0.25 s decision slices, steering, pitch-control-scored options, contests, set pieces, offside, 2D renderer, commentary, in-match changes | Cross-tier test: tier 3 and tier 2 distributions agree within tolerance for the same fixtures |
| 10 | **Management depth** | Staff hiring and attributes, training schedules, scouting and fog of war, board and club vision, dynamics/hierarchy, team talks, press conferences, news generation | Each system has a visible effect traceable in a test |
| 11 | **Breadth and polish** | Continental competitions (Swiss format + draw solver), international football, awards and history, database editor and import format, Tauri desktop build, performance pass | A 30-season save stays under target size and load time |

## What I'd push back on inside this plan

- **The 2D match view comes late on purpose.** It is tempting to build it early because it's visual. But if it is built before tiers 1 and 2 are calibrated, it becomes a toy disconnected from the season sim.
- **Stage 6 (UI v1) comes before transfers.** Without it, nobody plays the thing for weeks and design problems stay hidden. FM26 shows that UI is where these games live or die [Certain for FM26's reviews].
- **Real player data is not in any stage.** See 05. If you want it, it becomes an import feature in Stage 11, loaded by the user locally.
