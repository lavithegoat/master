# 03. Match engine

## 3.1 How FM's engine works (what is public)

| Fact | Tag |
|---|---|
| Players and officials make a decision every quarter of a second, called a "slice" | [Certain] |
| Since FM21, players can change their decision mid-slice as the situation develops | [Certain] |
| Decisions come from attributes. For example, "Decisions" shapes run timing, "Passing" and "Vision" shape through-ball quality, and "Stamina" shapes late-game output | [Certain] |
| Shot outcomes weigh the shooter's Finishing, Composure and Technique against the keeper's Reflexes, Positioning and Handling, with context such as pressure, fatigue and importance | [Certain, but from a secondary explainer, not SI] |
| Playable leagues use the full engine. Background leagues use a quick match engine | [Certain] |
| FM26 moved rendering to Unity but kept the engine logic proprietary. It added mocap animation, better ball physics and "thousands" of AI changes, mostly to marking and defensive positioning | [Certain] |
| SI has not published the engine's internals | [Certain] |
| FM21 details (official): defenders hand over marking duties when switching to close someone down; the defensive line moves as a unit and jockeys instead of diving in; players block passing lanes instead of following the ball; forwards come short to offer support or drag defenders out of position; shot selection considers the angle and the keeper's position; keepers set themselves when they see a shooter setting up; weather affects the catch-or-punch decision | [Certain] |
| FM26 details (official): a rewritten **pass risk assessment** where players judge a pass's danger from their attributes; dribblers "stand up their marker, bait them into a challenge, then burst past"; refined pressing shapes; defenders "stretch, lunge and improvise" on interceptions; better keeper save logic, cross claiming and distribution; a new "look at" system; better corner defending | [Certain] |

These notes name the behaviours our tier-3 engine must reproduce: **marking handover, the line moving as a unit, lane blocking, forwards coming short, angle-aware shot choice, keeper set position, and pass risk scored from attributes.** They go straight onto the Stage 9 test list.

**Takeaway.** FM's full engine is an **agent-based spatial simulation**: 22 players plus the ball on a continuous pitch, each running a decision loop at about 4 Hz, with outcomes resolved from attributes. FM's 2D view is a rendering of that simulation, not an animation laid over a dice roll [Likely].

## 3.2 Modelling options

### A. Score-level statistical model (Poisson / Dixon–Coles)
Each team gets attack and defence strengths plus home advantage. Goals are Poisson(λ). Dixon–Coles adds a ρ term that corrects the under-prediction of 0-0, 1-0, 0-1 and 1-1 [Certain].
- **Pros:** trivially fast, and realistic scorelines are easy to calibrate.
- **Cons:** no players, events, tactics or stats. Unusable for the user's match.
- **Use:** only for view-only leagues, where we need a result and nothing else.

### B. Minute-by-minute chance model (event-level quick engine)
Each minute, each side rolls for a chance, with probability based on team attack vs opponent defence. A shooter is picked by position and attribute weights, then the finish is resolved against the keeper. One open-source TypeScript engine uses [Certain]:
- `pChance = clamp(0.135 × 2 × att / (att + defOpp), 0.02, 0.45)`, with a home multiplier of 1.08
- shooter weights: forwards 4×, midfielders 2.4×, defenders 0.7×
- base conversion 11.5%, 82% of goals assisted, about 3.6 yellows per match, about 0.32 injuries per match
- a condition factor of `0.72 + 0.28 × condition/100`
- tactical instructions as multiplicative factors in the range 0.76–1.35
- **Pros:** fast (thousands of matches per second), produces player stats, ratings, cards and injuries, and tactics can nudge it.
- **Cons:** tactics are fudge factors. You cannot *see* a high line get exploited.
- **Use:** FM's "quick match engine" equivalent, for every non-user match in playable leagues.

### C. Possession-chain / Markov model (zone-based)
Discretise the pitch into zones (xT uses 16×12 = 192 [Certain]). A possession moves through zones by pass, carry, shot or turnover, with transition probabilities estimated from event data [Certain]. Recent work extends this to semi-Markov models that account for event timing [Certain]. openfootmanager tags itself "markov-chain" and produces a text simulation [Certain].
- **Pros:** richer than B. It produces territory, possession %, pass counts and xG from real locations. Tactics can map onto transition matrices (for example, "direct passing" raises long-transition probabilities). Still fast.
- **Cons:** no off-ball movement, so tactical shape is abstract. Calibrating it needs event data. Use the Wyscout public dataset (CC BY 4.0, top-5 leagues 2017/18, allowed commercially with attribution). StatsBomb's terms forbid commercial use of anything derived from it (see 05).
- **Use:** a strong candidate for the quick engine, and an upgrade path from B.

### D. Agent-based spatial simulation (FM's full engine)
Continuous 2D pitch (for example 105×68 m). A fixed timestep for physics, perhaps 10–20 Hz, plus a decision tick every 0.25 s per FM [Certain for FM's slice]. Each player:
1. **Perceives** within a vision cone, limited by Anticipation, Vision and Concentration.
2. **Evaluates options**: pass targets, dribble lanes, shots, runs into space, pressing, cover, marking. Each option is scored by expected value (xT/EPV-style) with noise tied to Decisions and Composure.
3. **Acts** through steering behaviours (seek, arrive, pursue, intercept, separation) [Certain as technique; Reynolds 1999].
4. **Resolves** contests (tackles, headers, first touches, shots) from attribute comparisons.

Positioning targets come from the tactic: the formation shape shifts with ball position, IP vs OOP shape, defensive line, width and line of engagement. Pitch-control models (Spearman: time-to-intercept from each player's max speed and acceleration [Certain]) give a principled way to score "space" for pass and run decisions.
- **Pros:** tactics have visible, emergent effects, and it drives the 2D view directly. This is what makes FM feel like FM.
- **Cons:** by far the most expensive system to build and tune. Unrealistic emergent behaviour is common: everyone chasing the ball, no one covering. Too slow to run for every background match.
- **Calibration data:** SkillCorner open tracking data (MIT, 10 A-League matches) gives real player speeds, team shape width and depth, and pressing distances [Certain for licence and content]. It is the only commercially usable tracking set found.
- **References:** Buckland, *Programming Game AI by Example*, ch. 3 (steering) and the "Simple Soccer" chapter (FSM-driven team AI) [Certain]. Reynolds, "Steering Behaviors for Autonomous Characters" (GDC 1999) [Certain]. Google Research Football, an open-source C++ physics-based 11v11 engine with full rules (offside, cards, set pieces) [Certain], worth reading for rules handling.

## 3.3 Open-source engines to study

| Project | Lang | License | Approach | Notes |
|---|---|---|---|---|
| [openfootmanager](https://github.com/openfootmanager/openfootmanager) | Rust (+ Tauri/React) | GPLv3 | Text sim, Markov-flavoured, event commentary | 1.1k stars, active (updated 2026-09-25), early development [Certain] |
| [open-football](https://github.com/ZOXEXIVO/open-football) | Rust | Apache-2.0 | Autonomous sim. Modules include `engine/`, `state.rs`, `dispatch.rs`, `rules.rs`, `calibration.rs` | 219 stars, 1.6k commits, multi-core, web UI [Certain] |
| [footballSimulationEngine](https://github.com/GallagherAiden/footballSimulationEngine) | JS (Node) | MIT | Spatial iterations: ball move, then player move, then one ball interaction. JSON in/out | 178 stars. Ball has x, y, z. Actions: shoot, pass, cross, tackle, intercept, slide, run, sprint [Certain] |
| [openengine](https://github.com/atas76/openengine) | Java | MIT | Data-driven, tracking-data-inspired, "pluggable" | Moved to private development [Certain] |
| [JorgeQuijano/football-manager](https://github.com/JorgeQuijano/football-manager) | TS/React | none stated | Minute-by-minute chance model (option B), seeded mulberry32 PRNG, pure functions | Small, but a clean example of determinism discipline [Certain] |
| [Google Research Football](https://github.com/google-research/football) | C++/Python | Apache-2.0 [Likely] | Full physics sim for RL | Too heavy to embed. Useful for rules edge cases [Certain for sim scope] |

MIT/Apache code can be reused with attribution. GPLv3 code cannot go into a non-GPL project [Likely, not legal advice].

## 3.4 Calibration targets

The engine is only credible if long-run distributions match real football. Collected targets:

| Metric | Target | Tag |
|---|---|---|
| Goals per match (PL) | 2.85 (2022/23), 3.28 (2023/24 record). Aim for about 2.7–3.0 as a cross-league mean | [Certain] for PL figures |
| Shots per match (both teams) | About 24.9 (2017–23 average), 27.6 (2023/24) | [Certain] |
| Goals per match, GD-tiebreak leagues vs H2H leagues | 2.97 vs 2.66 (i.e. league variance exists) | [Certain] |
| Home advantage | About 1.08 multiplier in one open engine. Real-world home win share in top leagues is about 43–46% | Multiplier [Certain]; share [Guessing] |
| Yellow cards per match | About 3.6 in one open engine | [Certain] (engine value, not a real stat) |
| Injuries | See 02 §2.4 | |
| Draw share | About 24–27% in top leagues | [Guessing]. Must be verified against football-data.co.uk results in Stage 4 |

**Stage 4 must include a calibration harness.** It simulates N seasons headlessly and compares the distributions (goals, scorelines, home/draw/away split, shots, cards, points of champion and relegated sides) against real results from openfootball data (CC0). It fails CI if the numbers drift.

## 3.5 Recommended design

1. **Tier 1, view-only leagues:** option A (Dixon–Coles-style), results only.
2. **Tier 2, background matches in playable leagues:** option B first, since it can be built in days, then C (zone-based possession chains), which gives better stats and more meaningful tactics. The tier must output a full `MatchResult`: score, events, per-player stats, ratings, injuries, cards and fatigue.
3. **Tier 3, the user's matches:** option D, the spatial agent engine, emitting the **same `MatchResult` contract** plus a positional replay stream for the 2D view.
4. **One contract for all tiers.** Everything downstream (morale, development, ratings, finances) consumes `MatchResult` and never knows which tier produced it. That lets us build the season loop long before tier 3 exists.
5. **Determinism.** Every match takes an explicit seed derived as `hash(worldSeed, "match", season, round, homeId, awayId)` [Certain as a pattern from the TS reference]. No `Math.random()` anywhere in the engine. A replay is then just seed + inputs, which makes bugs reproducible.
6. **Cross-tier consistency test.** Run the same fixture 1,000 times on tier 2 and tier 3. The goal and shot distributions must agree within tolerance. Otherwise the user's matches will play differently from the world's, which players notice [Guessing about perception, but a cheap safeguard].

## Sources
- [FM21 match engine AI (official)](https://www.footballmanager.com/news/match-engine-ai-fm21)
- [FM26 IP/OOP tactical evolution (official)](https://www.footballmanager.com/fm26/features/possession-out-possession-fm26s-new-tactical-evolution)
- [Pappalardo et al. 2019 Wyscout dataset](https://www.nature.com/articles/s41597-019-0247-7)
- [SkillCorner opendata](https://github.com/SkillCorner/opendata)
- [Football Manager Guru: how the ME works](https://www.footballmanagerguru.com/how-football-manager-match-engine-works/)
- [Passion4FM: database size, quick match engine](https://www.passion4fm.com/how-to-set-up-your-database-size-in-football-manager/)
- [GamerBraves: Miles Jacobson TGS 2025](https://www.gamerbraves.com/tgs2025-studio-director-miles-jacobson-on-rebuilding-football-manager-26-from-the-ground-up/)
- [dashee87: Dixon–Coles and time weighting](https://dashee87.github.io/football/python/predicting-football-results-with-statistical-modelling-dixon-coles-and-time-weighting/)
- [penaltyblog: Dixon–Coles in Python](https://pena.lt/y/2021/06/24/predicting-football-results-using-python-and-dixon-and-coles/)
- [Hudl: possession value models explained](https://www.hudl.com/blog/possession-value-models-explained)
- [Scientific Reports: semi-Markov possessions, temporal xT](https://www.nature.com/articles/s41598-026-52938-1)
- [mplsoccer: expected threat tutorial](https://mplsoccer.readthedocs.io/en/latest/gallery/tutorials/plot_xt.html)
- [Training Ground Guru: Spearman pitch control](https://archive.trainingground.guru/articles/william-spearman-how-liverpool-create-pitch-control)
- [PMC: fine-grained EPV framework](https://pmc.ncbi.nlm.nih.gov/articles/PMC8570314/)
- [Reynolds: Steering Behaviors (GDC 99)](https://www.red3d.com/cwr/papers/1999/gdc99steer.pdf)
- [Buckland: Programming Game AI by Example](https://books.google.com/books/about/Programming_Game_AI_by_Example.html?id=gDLpyWtFacYC)
- [Google Research Football paper](https://arxiv.org/pdf/1907.11180)
- [JorgeQuijano/football-manager ENGINE.md](https://github.com/JorgeQuijano/football-manager/blob/main/docs/ENGINE.md)
- [Premier League: season trends, goals record](https://www.premierleague.com/en/news/4027257)
- [StatsUltra: GD vs H2H tiebreakers and goals](https://statsultra.com/goal-difference-vs-head-to-head-tiebreaker/)
