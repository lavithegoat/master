# 02. Player model

## 2.1 FM's attribute system (reference)

Visible attributes use a 1 to 20 scale. 10 is roughly professional average, 15+ is genuine quality and 17+ is elite [Certain]. There are four groups: Technical, Mental, Physical and Goalkeeping [Certain]. Goalkeepers have their own group of 13 attributes [Certain].

The full list below comes from my knowledge of FM24. I could not open the primary pages (fmscout and passion4fm are blocked by this environment's network policy), so the list is tagged **[Likely]** throughout. Stage 1 should check it against a copy of the game or the SI manual.

**Technical (outfield, 14)** [Likely]
Corners, Crossing, Dribbling, Finishing, First Touch, Free Kick Taking, Heading, Long Shots, Long Throws, Marking, Passing, Penalty Taking, Tackling, Technique.

**Mental (14)** [Likely]
Aggression, Anticipation, Bravery, Composure, Concentration, Decisions, Determination, Flair, Leadership, Off the Ball, Positioning, Teamwork, Vision, Work Rate.

**Physical (8)** [Likely]
Acceleration, Agility, Balance, Jumping Reach, Natural Fitness, Pace, Stamina, Strength.

**Goalkeeping (13)** [Likely]
Aerial Reach, Command of Area, Communication, Eccentricity, First Touch, Handling, Kicking, One on Ones, Passing, Punching (Tendency), Reflexes, Rushing Out (Tendency), Throwing. (First Touch and Passing are shared with the technical set.)

**Footedness**: left foot and right foot strength, 1 to 20. Foot strength counts toward CA [Certain].

**Hidden attributes (6)**: Consistency, Dirtiness, Important Matches, Injury Proneness, Versatility, and Adaptability [Likely]. Sources agree there are six hidden attributes on a 1 to 20 scale [Certain]. They name Consistency, Injury Proneness, Versatility and Adaptability [Certain]. The last two in my list are from memory.

**Personality attributes (hidden)**: Ambition, Controversy, Loyalty, Pressure, Professionalism, Sportsmanship, Temperament [Certain, per the personality-type source]. Personality labels are derived from these plus Determination and Leadership [Certain]. Examples [Certain]:
- Model Citizen: Ambition 12–20, Determination 14–20, Loyalty 15–20, Pressure 14–20, Professionalism 15–20, Sportsmanship 15–20, Temperament 15–20.
- Perfectionist: Ambition 14–20, Determination 14–20, Professionalism 14–20, Temperament 1–9.
- Resolute: Ambition 5–20, Determination 15–17, Pressure 1–16, Professionalism 15–20, Sportsmanship 12–20.
- Model Professional: Professionalism 20, Temperament 10–20.

Scouts only hint at hidden attributes, as pros and cons in reports, and only at their extremes [Certain].

**Positional familiarity**: each position has a 1 to 20 rating (Natural, Accomplished, Competent, Unconvincing, Awkward, Ineffectual). These labels are from memory [Likely].

**Presentation change in FM26**: attributes can be shown as words ("Good", "Excellent") instead of numbers [Certain]. For staff the mapping is 20 Elite, 18–19 Outstanding, 15–17 Very Good, 12–14 Good, 10–11 Average, 7–9 Competent, 4–6 Reasonable, 1–3 Unsuited [Certain]. Our plan: store numbers and offer both displays.

### Decision for our game
- **Keep a 1 to 20 display, but store attributes as floats internally.** Growth from 11.0 to 11.6 then happens smoothly, without random +1 jumps. This is my design choice [Guessing: FM may do something similar internally, but I have no source].
- **Use our own attribute names where FM's wording is distinctive.** Mechanics are not copyrightable, but copying FM's exact text and presentation adds needless risk [Likely, not legal advice]. Generic football terms ("Finishing", "Pace") are fine.
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
- For multi-position players, CA is computed against the most expensive of their natural positions [Guessing about FM; a reasonable default for us].

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
