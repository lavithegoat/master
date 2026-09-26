# 02. Player model

## 2.1 FM's attribute system (reference)

Visible attributes use a 1 to 20 scale. 10 is roughly professional average, 15+ is genuine quality and 17+ is elite [Certain]. There are four groups: Technical, Mental, Physical and Goalkeeping [Certain]. Goalkeepers have their own group of 13 attributes [Certain].

The lists below were checked against FM Scout's attribute guide, which gives the in-game grouping, and Passion4FM's definitions (both read directly on 26 Sep 2026).

**Technical (outfield, 14)** [Certain]
Corners, Crossing, Dribbling, Finishing, First Touch, Free Kick Taking, Heading, Long Shots, Long Throws, Marking, Passing, Penalty Taking, Tackling, Technique.
(FM Scout's list leaves out First Touch, but Passion4FM defines it as a technical attribute. It is on every outfield profile [Certain].)

**Mental (14)** [Certain]
Aggression, Anticipation, Bravery, Composure, Concentration, Decisions, Determination, Flair, Leadership, Off the Ball, Positioning, Teamwork, Vision, Work Rate.

**Physical (8)** [Certain]
Acceleration, Agility, Balance, Jumping Reach, Natural Fitness, Pace, Stamina, Strength.

**Goalkeeping** [Certain]
Aerial Reach, Command of Area, Communication, Eccentricity, Handling, Kicking, One on Ones, Reflexes, Rushing Out (Tendency), Punching (Tendency), Throwing. A goalkeeper's profile also shows First Touch and Passing [Likely], which makes up the "13" figure some guides quote.

Useful definitions, because they show how narrowly SI scopes each attribute:
- **Finishing** measures accuracy toward the intended target, not shot power or shot selection [Certain].
- **Passing** is execution quality. Pass *choice* comes from Decisions and Vision [Certain].
- **Eccentricity** is a keeper's tendency to do unconventional things, such as dribbling out of the area [Certain].
- **Natural Fitness** is both how well fitness is kept up and how fast a player recovers from injury [Certain].

**Footedness**: left foot and right foot strength, 1 to 20. Foot strength counts toward CA [Certain].

**Hidden attributes (6)** [Certain]
| Attribute | Effect |
|---|---|
| Adaptability | How fast a player settles in a new country, culture or club |
| Consistency | How often he plays to his actual ability instead of fluctuating |
| Dirtiness | Tendency to bend the rules and commit fouls. More cards |
| Important Matches | Performance lift or drop in derbies, cups and finals |
| Injury Proneness | How often he gets injured |
| Versatility | How quickly he learns new positions |

**Personality attributes (hidden, 7)** [Certain]: Ambition, Controversy, Loyalty, Pressure, Professionalism, Sportsmanship, Temperament. The personality *label* is derived from these plus Determination and Leadership. Media handling style is derived from Controversy, Pressure, Professionalism, Temperament, Sportsmanship and Loyalty [Certain].

**Personality types and thresholds** (FM Scout, read directly) [Certain]. These are a lookup table from attribute ranges to a label. Several types are generated only for newgens.

| Type | Requirements |
|---|---|
| Model Citizen | Amb 12–20, Det 14–20, Loy 15–20, Pres 14–20, Prof 15–20, Spo 15–20, Temp 15–20 |
| Perfectionist | Amb 14–20, Det 14–20, Prof 14–20, Temp 1–9 |
| Resolute | Amb 5–20, Det 15–17, Pres 1–16, Prof 15–20, Spo 12–20 |
| Model Professional | Prof 20, Temp 10–20 |
| Professional | Prof 18–20, Temp 10–20 |
| Fairly Professional | Det 1–14, Prof 15–20 |
| Spirited | Det 1–17, Pres 15–20, Prof 11–17, Spo 1–14, Temp 10–20 |
| Driven | Amb 12–20, Det 17–20 |
| Determined | Amb 1–11, Det 17–20 |
| Fairly Determined | Det 15–17, Pres 1–16, Prof 1–14, Spo 5–20 |
| Iron Willed | Det 15–17, Pres 20, Spo 5–20 |
| Resilient | Amb 8–20, Det 15–17, Pres 17–20, Spo 5–20 |
| Charismatic Leader | Lead 18–20, Spo 18–20, Temp 18–20 |
| Born Leader | Det 20, Lead 20 |
| Leader | Lead 19–20 |
| Very Ambitious | Amb 20, Det 1–17, Loy 1–10 |
| Ambitious | Amb 16–20, Det 1–17, Loy 1–10 |
| Fairly Ambitious | Amb 15–20, Det 1–14, Prof 1–14 |
| Fickle (newgen) | Amb 15–20, Det 1–14, Loy 1–14, Prof 1–14 |
| Mercenary (newgen) | Amb 16–20, Det 1–17, Loy 1–3 |
| Slack (newgen) | Det 1–9, Prof 1, Temp 5–20 |
| Casual (newgen) | Det 1–9, Prof 1–4, Temp 5–20 |
| Temperamental (newgen) | Prof 1–10, Temp 1–4 |
| Easily Discouraged (newgen) | Amb 1–10, Det 1, Prof 5–20, Spo 1–17 |
| Low Determination (newgen) | Amb 1–10, Det 1–5, Prof 5–20, Spo 1–17 |
| Spineless (newgen) | Det 1–10, Pres 1, Prof 5–20, Spo 1–17 |
| Low Self-Belief (newgen) | Det 1–10, Pres 1–3, Prof 5–20, Spo 1–17 |
| Unambitious | Amb 1–5, Det 1–17, Loy 11–20 |
| Jovial | Det 1–17, Pres 15–20, Prof 1–10, Spo 1–14, Temp 10–20 |
| Light-Hearted | Det 1–17, Pres 15–20, Prof 1–17, Spo 15–20, Temp 10–20 |
| Devoted / Very Loyal | Amb 5–8, Det 18–20, Loy 20 |
| Loyal | Amb 1–7, Det 6–17, Loy 17–19 |
| Fairly Loyal | Amb 6–14, Det 1–14, Loy 15–20, Prof 1–14 |
| Honest | Det 1–10, Prof 5–20, Spo 20 |
| Sporting | Det 1–10, Prof 8–20, Spo 18–20 |
| Fairly Sporting | Amb 1–14, Det 1–14, Loy 1–14, Prof 1–14, Spo 15–20 |
| Unsporting (newgen) | Det 10–17, Spo 1 |
| Realist (newgen) | Det 10–17, Spo 1–4 |
| Balanced | Amb, Det, Loy, Prof, Spo all 1–14 |

Media handling styles [Certain]: Confrontational (Spo 1–4, Temp 1–4), Evasive (Cont 6–14, Pres 15–20, Prof 15–20, Temp 7–14), Level-Headed (Cont 1–14, Temp 7–20, Loy 11–20), Media-Friendly (Cont 1–14), Outspoken (Cont 15–20), Reserved (Cont 1–5, Prof 15–20, Temp 7–20), Short-Tempered (Temp 1–4), Unflappable (Pres 15–20, Temp 15–20), Volatile (Temp 1–6).

**For our game:** the ranges overlap, so the label needs a priority order when several match. FM's order isn't published [Certain]. We will write our own personality taxonomy with our own names and thresholds rather than copy this table. It is SI's creative expression, and we are going commercial (see 05). The *concept* of deriving a label from hidden traits is fine to reuse [Likely, not legal advice].

Scouts only hint at hidden attributes, as pros and cons in reports, and only at their extremes [Certain].

**Positional familiarity**: each position has a 1 to 20 rating (Natural, Accomplished, Competent, Unconvincing, Awkward, Ineffectual). These labels are from memory [Likely].

**Presentation change in FM26**: attributes can be shown as words ("Good", "Excellent") instead of numbers [Certain]. For staff the mapping is 20 Elite, 18–19 Outstanding, 15–17 Very Good, 12–14 Good, 10–11 Average, 7–9 Competent, 4–6 Reasonable, 1–3 Unsuited [Certain]. Our plan: store numbers and offer both displays.

### Decision for our game
- **Keep a 1 to 20 display, but store attributes as floats internally.** Growth from 11.0 to 11.6 then happens smoothly, without random +1 jumps. This is my design choice [Guessing: FM may do something similar internally, but I have no source].
- **Use our own attribute names where FM's wording is distinctive.** Mechanics are not copyrightable, but copying FM's exact text and presentation adds needless risk for a game we intend to sell [Likely, not legal advice]. Generic football terms ("Finishing", "Pace") are fine. FM-coined terms ("Eccentricity", "Important Matches", the personality labels) get our own names.
- Start with the FM set so the match engine has the inputs it expects. Trim later only if an attribute has no mechanical effect.

## 2.2 Current Ability / Potential Ability

| Fact | Tag |
|---|---|
| CA and PA range from 1 to 200. About 170 is world class | [Certain] |
| CA is a **weighted sum of attributes**, and the weights depend on position. Finishing weighs more for a striker than a defender | [Certain] |
| Consistency and Important Matches do not count toward CA. Visible attributes and foot strength do | [Certain] |
| PA is fixed at database creation and never changes in a save | [Certain] |
| CA can go up and down with game time, training and age | [Certain] |
| The real database can set PA as a negative code standing for a range, resolved when a new game starts. I don't know the exact code-to-range table | [Likely] |

**Why CA matters architecturally.** CA works as a **budget**. When a player gains CA, the points are distributed across attributes according to position weights, training focus and age. The match engine itself reads attributes, not CA [Likely]. Our plan:
- `CA = Σ(attribute × weight[position][attribute]) + footWeight`. Weights are our own, tuned so a 20-in-everything player lands near 200.
- Growth events add CA budget, and a distribution step turns it into attribute deltas.
- For multi-position players, CA is computed against the most expensive of their natural positions. The FM Scout guide's author thinks FM "likely uses the highest weight for those positions" [Likely].
- **CA is non-linear in attributes.** FM Scout's reverse-engineering found a player with 16 in every attribute (weaker foot included) already sits at CA 199, with diminishing returns above about 17 [Certain, per their tests against 30+ in-game players]. That means attributes of 17–20 are expensive to "buy", which caps how many elite attributes one player can have. Our formula needs the same property, or the world fills with 20-in-everything players.
- FM Scout's CA benchmarks: 130 decent Premier League player, 140 good, 150 leading, 170 world class [Certain].

## 2.3 Development and ageing

**FM behaviour (sourced)**
- At about 18, training and match experience matter roughly equally. By about 23, match experience dominates [Certain, community research].
- Growth largely stops around 27–28 [Certain, community research].
- Professionalism has the biggest effect on closing the CA→PA gap. In one test, max Professionalism gave about 46 CA over 3 years, max Ambition about 26 and max Determination about 22 [Certain, per FM-Arena-style research summarised by Passion4FM].
- Coach quality ("Working with Youngsters" for under-23s) and facilities speed up growth [Certain].

**Real-world ageing curves (for calibrating decline)**
- Overall peak is about 27.4 years. The range is roughly 25–28 [Certain].
- Wingers peak youngest (about 26.1) and decline fastest. Forwards peak around 25, central defenders around 27.5 and decline slowest. Goalkeepers peak around 30 [Certain].
- Speed peaks around 25.7, endurance around 24.8, explosiveness around 26. Acceleration can peak as early as 20–24 [Certain].
- Decision-making, positioning and composure hold up into the early 30s. Over 32, high-intensity output declines while endurance stays relatively stable [Certain].

**Model for our game**
- Per-attribute ageing: physical attributes (Pace, Acceleration, Agility) peak early and fall first. Mental attributes (Decisions, Positioning, Anticipation, Composure) keep rising to about 30–32.
- Growth rate each week = f(age, PA − CA gap, Professionalism, Determination, Ambition, match minutes at the right level, training quality, coach quality, injury status) + noise.
- Decline starts from about 29–31, adjusted by position group and Natural Fitness.

## 2.4 Injuries (real-world calibration)

| Metric | Value | Tag |
|---|---|---|
| Overall incidence, elite men's football | 8.0 per 1,000 h | [Certain] |
| Match incidence vs training incidence | 27.5 vs 4.1 per 1,000 h | [Certain] |
| Hamstring share of all injuries | Rose from 12% (2001/02) to 24% (2021/22) | [Certain] |
| Hamstring match rate vs training rate | 4.77 vs 0.51 per 1,000 h | [Certain] |
| Re-injuries | 12% of injuries, with longer absences (24 vs 18 days) | [Certain] |

**What this means for a match.** A match is 22 players × about 1.5 h = 33 player-hours. At 27.5 per 1,000 h that gives **about 0.9 injuries per match**, most of them minor (under a week) [Guessing: my arithmetic on the sourced rate]. One open-source engine uses 0.32 per match [Certain], probably counting only injuries that cost a game. We need an injury table with type, body area, duration distribution and re-injury multiplier. Injury Proneness, fatigue, Natural Fitness, training intensity and medical staff should modify the risk.

## 2.5 Player traits

FM traits are behaviour biases such as "Cuts Inside", "Dives Into Tackles" and "Tries Killer Balls Often" [Certain]. They are learnable through individual training [Likely]. In our engine a trait is a **weight modifier on decision options** in the spatial engine, and a small modifier on event rates in the quick engine.

## 2.6 Match ratings

FM ratings use a 10-point scale that starts at 6.0. Most ratings fall between 6 and 8 [Certain]. The exact formula is not public [Certain]. We will build our own: event-based additions (goal, key pass, tackle won, xG added, errors), scaled by position and minutes played.

A warning from openfootmanager, issue #607: "Match rating is never computed, and morale, ageing and awards all read it" [Certain]. Ratings feed morale, development, awards and transfer valuation. **Match ratings are a P0 dependency, not polish.**

## Sources
- [FM Scout: understanding player attributes (grouping)](https://www.fmscout.com/a-understanding-player-attributes-fm-guide.html)
- [FM Scout: definitive CA guide p.3 (non-linearity)](https://www.fmscout.com/a-guide-to-current-ability-in-football-manager.html?pg=3)
- [Passion4FM: player attributes](https://www.passion4fm.com/football-manager-player-attributes/)
- [FM Dossier: 1–20 scale](https://fmdossier.dev/guides/player-attributes-explained)
- [Passion4FM: goalkeeper attributes](https://www.passion4fm.com/football-manager-goalkeeper-attributes/)
- [Passion4FM: hidden attributes](https://www.passion4fm.com/football-manager-guide-to-hidden-attributes/)
- [FM Scout: personalities](https://www.fmscout.com/a-guide-to-player-personalities-football-manager.html)
- [sortitoutsi: FM26 personality guide](https://sortitoutsi.net/content/75646/fm26-player-personality-guide)
- [FM Scout: CA/PA guide](https://www.fmscout.com/a-football-manager-current-and-potential-ability-guide.html)
- [FM Scout: definitive CA guide (position weights)](https://www.fmscout.com/a-guide-to-current-ability-in-football-manager.html)
- [FM Dossier: CA vs PA](https://fmdossier.dev/guides/current-vs-potential-ability)
- [FM-Arena: player development and training](https://fm-arena.com/thread/2671-player-development-and-training/)
- [Passion4FM: youth development](https://www.passion4fm.com/football-manager-youth-development-guide/)
- [ESPN: age curves by position](https://www.espn.com/soccer/story/_/id/37467220/soccer-age-curves-show-goalkeepers-central-defenders-peak-latest)
- [PMC: ageing curve of physical performance](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC12551122/)
- [Michael Caley: age curves](https://www.expectinggoals.com/p/building-marcel-part-ii-age-curves)
- [PubMed: UEFA ECIS hamstring 2001–2022](https://pubmed.ncbi.nlm.nih.gov/36588400/)
- [PubMed: injury rates 18-year cohort](https://pubmed.ncbi.nlm.nih.gov/33547038/)
- [UEFA injury study (ResearchGate)](https://www.researchgate.net/publication/26317624_Injury_incidence_and_injury_patterns_in_professional_football_The_UEFA_injury_study)
- [Passion4FM: player traits](https://www.passion4fm.com/football-manager-player-traits/)
- [Operation Sports: FM26 match ratings](https://www.operationsports.com/how-do-match-ratings-in-football-manager-26-work/)
- [openfootmanager issue #607](https://github.com/openfootmanager/openfootmanager/issues/607)
- [FM Scout: FM26 staff attributes as words](https://www.fmscout.com/q-34043-Staff-attributes-in-FM26.html)
