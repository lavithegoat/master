# 05. Data sources and licensing (commercial posture)

**Updated 26 Sep 2026.** You intend to publish and sell the game eventually, so every rule below is written for a commercial product. This is not legal advice. Before launch, a UK/EU games lawyer should review the final data and naming choices, likely a few hours of work [Guessing on effort].

## 5.1 Names, clubs and likeness

| Fact | Tag |
|---|---|
| Player names, image and likeness in games are licensed through FIFPRO Commercial Enterprises. A competition licence does not cover player names | [Certain] |
| Manchester United sued SEGA/SI in 2020 over unlicensed use of the club name in FM, nearly 30 years after the series first used it. The case settled on 6 Aug 2021 "on a no admissions basis", and from FM22 the club appears as "Manchester UFC" | [Certain] |
| SEGA/SI maintained they did not need a licence, and a law-firm analysis argued FM might be an exception *because* of its 30-year history of unlicensed use | [Certain] |
| Indie management games on Steam (Whistle1, Footy Manager) ship fully fictional clubs, players and competitions | [Certain] |
| FD 27 ships fictional clubs but supports user-made databases that swap in real ones | [Certain] |

**What this means for us.** SEGA, with 30 years of precedent and a legal department, still ended up renaming a club. A new indie title has neither [Likely]. So:

1. **Ship a fully fictional world.** That means fictional clubs, players, stadiums, competition names and awards. Real nations, cities and geography are fine [Likely].
2. **Ship an open database format and an editor.** Users can build or download real-name databases themselves, as with FM's "real name fix" files and FD 27's custom databases [Certain for both]. **We must not host, bundle or link real-name databases from the store page or official channels** [Likely: distribution is where liability concentrates].
3. **No real crests, kits or likenesses.** Crests and kits are trademarks and designs. Generate procedural badges and kits instead.
4. **Choose a name that stays clear of SEGA's "Football Manager" and Square Enix's "Championship Manager" marks.** Run a trademark search (UKIPO, EUIPO, USPTO) before building a brand [Likely].
5. **Don't copy SI's expressive content:** personality labels, role names, attribute descriptions, UI layouts or text. Mechanics are free to reuse, but specific expression is not [Likely].

## 5.2 Data sources, re-rated for commercial use

| Source | What | Licence / terms | Commercial verdict |
|---|---|---|---|
| [openfootball](https://github.com/openfootball) | Fixtures, results, clubs, league structures | **CC0** [Certain] | **Allowed.** Use real results to calibrate the tier-1/2 engines. League *formats* are facts |
| [Wyscout public dataset (Pappalardo et al., 2019)](https://www.nature.com/articles/s41597-019-0247-7) | All events (passes, shots, fouls, with x/y) for 2017/18 in the top 5 leagues, plus WC 2018 and Euro 2016 | **CC BY 4.0** [Certain] | **Allowed with attribution.** This replaces StatsBomb as our source for zone transition matrices (xT-style), an xG model, and event-rate calibration. Ship derived parameters and credit "Pappalardo et al. 2019 / Wyscout" in the credits |
| [SkillCorner open data](https://github.com/SkillCorner/opendata) | Broadcast tracking (player and ball positions) for 10 A-League matches, dynamic events, phases of play, body pose, season aggregates | **MIT**, © 2020 SkillCorner [Certain] | **Allowed with the MIT notice.** Useful for calibrating the tier-3 spatial engine: sprint speeds, team shape widths and depths, pressing distances |
| [StatsBomb open data](https://github.com/statsbomb/open-data) | Events, lineups, 360 frames | User agreement §1.2.2: the user may not "commercially exploit the data **or any analysis derived from the use of the Service**". §7 says all data is StatsBomb's property [Certain, read from LICENSE.pdf] | **Forbidden for a commercial game**, even as derived engine parameters. **This corrects the earlier draft**, which said derived use was "unknown". Don't use it, even for R&D on this codebase, so nothing contaminates the shipped parameters |
| [Metrica sample data](https://github.com/metrica-sports/sample-data) | Tracking + events, 3 anonymised games | No licence file, just "please be responsible" and "acknowledge the source" [Certain] | **Avoid** without written permission. No licence means no rights granted [Likely] |
| [dcaribou/transfermarkt-datasets](https://github.com/dcaribou/transfermarkt-datasets) | Valuations, transfers, appearances | Repo is CC0, but it is scraped Transfermarkt data and Transfermarkt's terms are unaddressed [Certain]. Updates paused since July 2026 [Certain] | **Avoid for shipped parameters.** Hand-tune the valuation model against published aggregate facts instead (e.g. Deloitte revenue figures, see 04). Aggregate facts and statistics are generally not copyrightable [Likely] |
| [ClubElo](http://clubelo.com/API) | Daily club Elo | Free API, no stated licence [Certain] | Personal sanity checks only. Don't ship its data |
| FBref | Basic stats. Opta advanced stats removed 20 Jan 2026 [Certain] | Site ToS | Avoid |
| EA FC / sofifa dumps | Player ratings | Scraped, "research only" [Certain] | **Forbidden** |
| [popular-names-by-country-dataset](https://github.com/sigpwned/popular-names-by-country-dataset) | Popular forenames and surnames by country | **CC0** [Certain] | **Allowed.** Primary name source for generation |
| [full-name-generator](https://github.com/ozdemirburak/full-name-generator) | Government-sourced names, 25 countries | Code MIT, data under each government's open licence, attribution required [Certain] | Allowed with per-country attribution. Secondary source |
| [philipperemy/name-dataset](https://github.com/philipperemy/name-dataset) | 491M real-person records | Provenance questionable [Guessing: I believe it comes from a social-media leak] | **Avoid** |

**Generated-name risk.** A generator can accidentally produce a real footballer's full name. Add a blocklist check against a list of notable current players' names, built from public squad lists at build time and not shipped [Guessing: cheap insurance; not a known legal requirement].

## 5.3 Code licences

- Our game will be proprietary, so **no GPL/AGPL code or dependencies in anything we ship.** That rules out copying from openfootmanager (GPLv3) and Bygfoot (GPLv2) [Likely].
- MIT, Apache-2.0, BSD, ISC and CC0 are fine with notices. Generate a third-party notices file at build time.
- ZenGM is source-available, not open source [Certain]. Read it for ideas only.
- The JorgeQuijano repo has no licence, so treat it as all rights reserved.
- Fonts, icons and sound must be commercially licensed: OFL fonts, MIT or Apache icon sets.

## 5.4 Repository visibility

`lavithegoat/master` is **public** and shares a repository with an unrelated motion-graphics project [Certain, from the GitHub API]. For a commercial game I recommend a **dedicated private repository** before real code lands. Moving code later is easy, but anything published in the meantime stays in forks and caches.

## Sources
- [VGC: FM drops Manchester United name](https://www.videogameschronicle.com/news/football-manager-will-no-longer-use-the-manchester-united-name-following-a-trademark-dispute/)
- [Mishcon de Reya: brands in sports video games](https://www.mishcon.com/news/brands-in-sports-video-games-whyfootball-managermight-be-the-exception)
- [LawInSport: SEGA v Man Utd settlement](https://www.lawinsport.com/topics/item/sega-s-battle-against-man-utd-in-football-manager-trade-mark-case-ends-in-settlement)
- [Business Wire: FIFPRO Commercial Enterprises](https://www.businesswire.com/news/home/20211012005904/en)
- [FM Scout: FM26 real names licence fix](https://www.fmscout.com/a-fm26-real-names-license-fix.html)
- [Steam: Whistle1](https://store.steampowered.com/app/4245520/Whistle1__Retro_Football_Manager/), [Footy Manager](https://store.steampowered.com/app/5096190/Footy_Manager/), [FD 27](https://steamcommunity.com/app/4529440)
- [StatsBomb open-data LICENSE.pdf](https://github.com/statsbomb/open-data/blob/master/LICENSE.pdf)
- [Pappalardo et al. 2019, Scientific Data](https://www.nature.com/articles/s41597-019-0247-7) and [figshare collection](https://figshare.com/collections/Soccer_match_event_dataset/4415000)
- [SkillCorner opendata (MIT)](https://github.com/SkillCorner/opendata)
- [Metrica sample data](https://github.com/metrica-sports/sample-data)
- [openfootball](https://github.com/openfootball)
- [dcaribou/transfermarkt-datasets](https://github.com/dcaribou/transfermarkt-datasets)
- [Awful Announcing: FBref advanced data pulled](https://awfulannouncing.com/soccer/sports-reference-pulls-advanced-data-agreement-violation-dispute.html)
- [sigpwned/popular-names-by-country-dataset](https://github.com/sigpwned/popular-names-by-country-dataset)
- [ozdemirburak/full-name-generator](https://github.com/ozdemirburak/full-name-generator)
