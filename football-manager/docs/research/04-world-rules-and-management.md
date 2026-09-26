# 04. World rules and management systems

## 4.1 Competitions

**League structure.** The competition engine must be data-driven. Each competition is config: format, number of teams, promotion and relegation slots, play-offs, tie-breaker order, squad rules, prize money and calendar. Nothing hard-coded per league [design decision].

**Tie-breakers differ by league** [Certain]:
- **Premier League:** goal difference, then goals scored, then head-to-head (only if all of those are level), then a play-off.
- **La Liga:** head-to-head points, then head-to-head goal difference, then overall goal difference.
- **Serie A:** a play-off decides the title if the top two finish level. Head-to-head applies elsewhere in the table.

So a tie-breaker is an ordered list of comparator functions per competition.

**UEFA Champions League (2024/25 onward)** [Certain]:
- 36 teams in a single league phase. Each plays 8 matches (4 home, 4 away) against 8 different opponents.
- Four pots by club coefficient. Each team plays two from each pot, one home and one away. The holder is top seed in pot 1.
- No same-country opponents, and at most 2 opponents from any one other country.
- Positions 1–8 go to the round of 16. Positions 9–24 enter a two-legged play-off, with 9–16 seeded. Positions 25–36 are eliminated.
- Associations ranked 1–4 by coefficient get 4 spots each.

**Implementation notes**
- Round-robin fixtures use the circle (Berger) method [Likely: standard algorithm]. Leagues add constraints: no three home games in a row, paired clubs sharing a stadium, derbies not on certain dates.
- The Swiss-format draw is a **constraint-satisfaction problem**. A naive random draw can dead-end. Use backtracking [Likely].
- Knockouts need legs, away goals (abolished by UEFA from 2021/22 [Likely]), extra time, penalties, and replays for some domestic cups.
- Coefficients (club and nation) drive seeding and slot allocation, so they must be simulated over seasons. ClubElo provides daily Elo rankings back to 1939 for sanity-checking the strength model [Certain].

## 4.2 Squad registration rules (England example)

| Rule | Detail | Tag |
|---|---|---|
| Squad list | Max 25 players, of whom at most 17 can be non-homegrown (so at least 8 homegrown) | [Certain] |
| Homegrown | Registered with an English/Welsh club for 3 full seasons or 36 months before turning 21, regardless of nationality | [Certain] |
| Under-21s | Outside the 25 and freely eligible. For 2025/26 that means born on or after 1 Jan 2004 | [Certain] |
| Work permits | The UK uses a GBE points-based system | [Likely]. Details not researched. P2 |

Rules like these vary by competition (UEFA has a separate "locally trained" list) [Likely], so they are also competition config.

## 4.3 Finances

**Real-world revenue mix (Deloitte Money League 2024/25)** [Certain]:
- Matchday €2.4bn, broadcast €4.7bn, commercial €5.3bn across the Money League clubs.
- In the top 10 clubs, commercial is 48% of revenue. For clubs ranked 11–20, broadcast is 47%.
- Premier League broadcast revenue is £3.4bn (2024/25). The Championship's is £461m.
- Bundesliga wages/revenue fell to 54% (2024/25), the lowest of the big five.

**Financial regulation** [Certain]:
- From 2026/27 the Premier League replaces PSR with the **Squad Cost Ratio (SCR)**. Squad costs (player and head coach wages, amortised transfer fees, agent fees) are capped at **85% of football revenue plus net player-sale profit**, with a multi-year 30% allowance above 85%.
- "Anchoring" (a hard spending cap tied to the bottom club) was voted down.
- UEFA's SCR caps at 85% (70% in its final phase [Likely]).

**Model for our game**
- The club ledger holds income lines (gate, season tickets, broadcast by league position/appearances, prize money, sponsorship tied to reputation, merchandise, player sales) and cost lines (wages, bonuses, amortisation, agent fees, staff, facilities upkeep, loan interest).
- **Amortisation** must be modelled properly: fee ÷ contract years. It is how real clubs, and FM's AI, reason about affordability [Likely].
- Rules such as SCR are optional competition-level constraints on AI and user budgets. P2.

## 4.4 Transfers and contracts

**How the FM AI behaves, per the community** [Likely; anecdotal but consistent]:
- The asking price reflects willingness to sell. Desperate buyers pay above value.
- Form and playing time move demand.
- The AI moves about 10–20% in negotiation, then walks away past a hidden threshold.
- Instalments are used when a buyer cannot pay the full fee up front.
- FM24 added intermediaries and a TransferRoom-like marketplace [Certain].

**Components we need**
1. **Valuation model.** Value = f(CA, PA, age, contract years left, position, reputation, form, league strength, club finances). Validate against Transfermarkt value distributions from the CC0 dataset (see 05), for R&D only.
2. **Squad-need model per AI club.** For each position group, compare depth and quality with the club's reputation tier and flag needs.
3. **Shortlist → bid → negotiate → contract → medical** state machine, with deadlines and competing bids.
4. **Contract terms:** wage, length, signing fee, appearance and goal bonuses, release clause, sell-on %, loan terms (wage split, option or obligation to buy), squad-status promise.
5. **Global market pass.** A weekly job in the transfer window where AI clubs act. Ordering and fairness matter, or the richest club hoovers everything [Guessing].

This is the most error-prone AI system after the match engine. FM players' most common complaints are about AI transfer behaviour [Likely].

## 4.5 Youth intake

| Fact | Tag |
|---|---|
| Each nation has a Youth Rating that sets the size and quality of its pre-intake pool. It is dynamic in recent versions | [Certain] |
| Club factors: Youth Facilities, Junior Coaching (both raise CA/PA of intake), Youth Recruitment (reach for home and foreign players) | [Certain] |
| Nationality flavours attributes (e.g. Brazilian technical/creative, Scandinavian hard-working) | [Certain] |
| Community tests dispute that Youth Facilities affect newgen PA | [Certain] that the claim exists; the effect itself is disputed |
| Intake happens on a fixed date per nation | [Likely] |

**For us:** generate players from (nation profile × club academy profile × position archetype). Names come from per-nation name lists (see 05). PA follows a heavy-tailed distribution where almost all are journeymen and rare wonderkids appear.

## 4.6 Staff

Staff attributes are rated 1 to 20 [Certain]. Coaching: Attacking, Defending, Fitness, Goalkeeping (shot stopping, handling, distribution), Mental, Tactical, Technical, Working with Youngsters. Mental: Adaptability, Determination, Level of Discipline, Man Management, Motivating. Knowledge: Judging Player Ability, Judging Player Potential, Tactical Knowledge. Medical: Physiotherapy, Sports Science. Parts of this list are [Certain] per sources (Attacking, Defending, Fitness, GK coaching, Motivating, Mental, Judging Ability/Potential, Working with Youngsters). The rest comes from memory [Likely].

Training quality per category = f(best coaches assigned to that category, their workload, facilities).

## 4.7 Squad dynamics and morale

Per the FM24 manual summaries [Certain]:
- **Hierarchy:** Team Leaders → Highly Influential → Influential → Other. Leadership and tenure drive a player's place in it.
- **Social groups:** unhappiness spreads within a group, and faster from leaders.
- **Happiness facets:** promises, morale, training, treatment (praise, criticism, fines), and the club's direction.
- **Interactions:** praising or criticising form, training and conduct changes morale immediately.
- A happy team leader can resolve other players' issues on your behalf.

**Model:** a graph of players with influence weights, a morale scalar with decay toward a baseline, and a promise ledger with deadlines. Morale feeds a performance modifier in both match tiers.

## 4.8 Board and club vision

- **Required objectives** are strict. Failing them causes immediate confidence drops, then ultimatums, then the sack. **Desired objectives** build trust when met [Certain].
- At about 6/20 or below on the comparative scale, the manager is close to being sacked [Certain].
- FM26 has a "club philosophy" layer [Certain].

**Model:** the board has confidence scores across areas (results, finances, style, youth, transfers), with an objectives list per season and per 5-year vision.

## 4.9 Scouting and fog of war

- Unscouted data is masked. Knowledge moves from none, to brief (stars, wide value range), to detailed (attribute ranges), to extensive (exact figures, traits) [Certain].
- Initial knowledge depends on manager reputation, experience and nationality [Certain].
- Scout accuracy depends on Judging Player Ability, Judging Player Potential and Adaptability. **A poor scout is biased, not just vague** [Certain].
- Recruitment focuses filter by position, max age and min potential [Certain]. Scouts observe, and analysts look at stats [Certain].

**Model:** each (club, player) pair has a knowledge level plus a *perceived* attribute vector, generated as true value + scout bias + noise that shrinks with knowledge. Store perceived values, not re-rolls, so reports stay consistent over time.

## Sources
- [NBC Sports: PL tie-breakers 2026](https://www.nbcsports.com/soccer/news/premier-league-tiebreaker-rules-2026-goal-difference-head-to-head-record-how-it-works)
- [StatsUltra: GD vs H2H by league](https://statsultra.com/goal-difference-vs-head-to-head-tiebreaker/)
- [ESPN: Serie A title play-off](https://www.espn.com/soccer/story/_/id/37629671/serie-decide-scudetto-winner-playoff-case-tie-table)
- [Olympics.com: UCL new format explained](https://www.olympics.com/en/news/uefa-champions-league-2024-25-new-format-explained)
- [CBS Sports: UCL 36-team format](https://www.cbssports.com/soccer/news/uefa-champions-leagues-new-format-everything-to-know-about-how-the-36-team-competition-will-operate)
- [ClubElo API](http://clubelo.com/API)
- [Premier League: squad lists explained](https://www.premierleague.com/en/news/4097471/premier-league-squad-lists-explained)
- [Wikipedia: Homegrown Player Rule (England)](https://en.wikipedia.org/wiki/Homegrown_Player_Rule_(England))
- [Deloitte Football Money League](https://www.deloitte.com/uk/en/services/consulting-financial/analysis/deloitte-football-money-league.html)
- [Deloitte Annual Review of Football Finance](https://www.deloitte.com/uk/en/services/consulting/research/annual-review-of-football-finance-europe-premier-league.html)
- [Premier League: new financial system explained](https://www.premierleague.com/en/news/4467022/new-premier-league-financial-system-explained)
- [Sky Sports: SCR approved, anchoring rejected](https://www.skysports.com/football/news/11095/13473576/premier-league-clubs-approve-new-squad-cost-ratio-rules-to-replace-psr-but-vote-against-a-proposal-that-could-have-introduced-a-salary-cap)
- [Passion4FM: youth intake guide](https://www.passion4fm.com/youth-intake-guide-how-clubs-produce-newgens/)
- [FM-Arena: youth facilities and PA](https://fm-arena.com/thread/15782-newgen-mechanics-revisited-youth-facilities-still-don-t-affect-pa/)
- [Passion4FM: staff attributes](https://www.passion4fm.com/football-manager-staff-attributes/)
- [FM24 manual: squad, team report and dynamics](https://community.sports-interactive.com/sigames-manual/football-manager-2024/your-squad-team-report-and-dynamics-r4957/)
- [Passion4FM: morale and happiness](https://www.passion4fm.com/how-to-improve-players-morale-happiness-in-football-manager/)
- [FM24 manual: club details and board performance](https://community.sports-interactive.com/sigames-manual/football-manager-2024/club-details-and-board-performance-r4963/)
- [sortitoutsi: FM26 club philosophy](https://sortitoutsi.net/content/76097/fm26-club-philosophy-guide)
- [Passion4FM: scouting in FM24](https://www.passion4fm.com/scouting-in-football-manager/)
- [FM Dossier: building a scouting network](https://fmdossier.dev/guides/building-a-scouting-network)
- [Steam discussion: AI transfer negotiation behaviour](https://steamcommunity.com/app/2252570/discussions/0/603020257754360005/)
