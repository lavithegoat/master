# 05. Data sources and licensing

This is not legal advice. It is a map of where the risk sits, so we can make decisions before any code depends on a data source.

## 5.1 The legal constraint

| Fact | Tag |
|---|---|
| Player names, image and likeness in games are licensed through FIFPRO Commercial Enterprises | [Certain] |
| A FIFA/competition licence does not include player names. Those need FIFPRO | [Certain] |
| FM itself ships fake names for some competitions, clubs and awards, and fans patch them with "real name fix" files | [Certain] |
| Some players and national teams sit outside FIFPRO's collective deals | [Certain] |
| Club names, crests and competition names are trademarks | [Likely] |
| Game *mechanics* are generally not protected by copyright, but specific expression (text, UI art, names like "Football Manager") is | [Likely] |

**Decision (recommended):**
1. Ship a **generated fictional world**: fictional clubs, players and competition names. Nations and real geography are fine [Likely].
2. Define an open, documented **database format** (JSON/SQLite) and ship an **editor**. What a user loads locally is their business. This is FM's own real-name-fix model and openfootmanager's philosophy [Certain for both].
3. Never bundle scraped player ratings (EA FC, sofifa, FM data) in the repository.

## 5.2 Data sources

| Source | What | Licence / terms | Use for us |
|---|---|---|---|
| [openfootball](https://github.com/openfootball) (football.json, clubs, leagues, world, europe) | Fixtures, results, clubs, stadiums, league structures | **CC0 / public domain** [Certain] | League structures and formats. Historical results for calibrating the match engine. Safe to bundle |
| [dcaribou/transfermarkt-datasets](https://github.com/dcaribou/transfermarkt-datasets) | 12 tables: competitions, clubs, players, games, appearances, valuations, transfers, lineups, events, countries, national teams | Repo **CC0** [Certain], but the data is scraped from Transfermarkt, whose ToS is unaddressed [Certain]. **Updates paused since July 2026** [Certain] | R&D only: calibrate valuation, wage and transfer-fee distributions and age/value curves. Do not bundle |
| [StatsBomb open data](https://github.com/statsbomb/open-data) | Event data, lineups, 360 freeze frames for selected competitions | Free for **non-commercial** use under a user agreement. Attribution and logo required [Certain] | R&D: fit xT / zone transition matrices and an xG model for the match engine. Ship derived parameters (a few hundred numbers), not raw data. Whether derived parameters are permitted commercially is **unknown** [Guessing] |
| [ClubElo](http://clubelo.com/API) | Daily club Elo since 1939 (pre-1960 provisional) | Free CSV API. Formal licence not found [Certain for API; licence unknown] | Sanity-check the relative strength model |
| FBref | Basic stats remain. **Opta advanced stats removed on 20 Jan 2026** [Certain] | Scraping ToS applies | Historical context only |
| EA FC / sofifa Kaggle dumps | Player ratings | Scraped. "Educational/research only" disclaimers [Certain] | **Avoid.** Proprietary and high legal risk |
| [popular-names-by-country-dataset](https://github.com/sigpwned/popular-names-by-country-dataset) | Popular forenames and surnames by country | **CC0** [Certain] | Newgen name generation. Safe to bundle |
| [full-name-generator](https://github.com/ozdemirburak/full-name-generator) | Real names from government stats, 25 countries | Code MIT, data requires attribution under each government's licence [Certain] | Alternative name source with attribution |
| [philipperemy/name-dataset](https://github.com/philipperemy/name-dataset) | 491M records, 106 countries, from a leak of real people | Licence and ethics questionable [Guessing: sourced from a Facebook leak, per my memory] | **Avoid** |
| [awesome-football](https://github.com/openfootball/awesome-football) | Index of datasets and tools | n/a | Discovery |
| [soccerdata](https://github.com/probberechts/soccerdata) / worldfootballR | Scraper wrappers for ClubElo, FBref, Understat, etc. | Code open, but data terms vary by site | R&D tooling |

## 5.3 Research blockers hit in this session

This environment's network policy blocked direct page reads from fmscout.com, passion4fm.com, footballmanager.com, community.sports-interactive.com and en.wikipedia.org. Search summaries of those sites were still available and are cited. Details marked [Likely] in 02 and 04 (exact attribute lists, role lists, staff attributes) should be verified if these domains are allowed later.

## Sources
- [Wikipedia: Soccer Manager 2024 (FIFPRO licensing context)](https://en.wikipedia.org/wiki/Soccer_Manager_2024)
- [FM Scout: FM26 real names licence fix](https://www.fmscout.com/a-fm26-real-names-license-fix.html)
- [Business Wire: FIFPRO Commercial Enterprises](https://www.businesswire.com/news/home/20211012005904/en)
- [openfootball/football.json](https://github.com/openfootball/football.json)
- [dcaribou/transfermarkt-datasets](https://github.com/dcaribou/transfermarkt-datasets)
- [StatsBomb open-data](https://github.com/statsbomb/open-data) and [socceraction docs on StatsBomb terms](https://socceraction.readthedocs.io/en/latest/documentation/data/statsbomb.html)
- [ClubElo API](http://clubelo.com/API)
- [Awful Announcing: FBref advanced data pulled](https://awfulannouncing.com/soccer/sports-reference-pulls-advanced-data-agreement-violation-dispute.html)
- [Liam Henshaw: where to find football data in 2026](https://www.liamhenshaw.com/writing/where-to-find-football-data)
- [Kaggle: EA FC players data (sofifa)](https://www.kaggle.com/datasets/ghimireprashant/fifa-players-data)
- [sigpwned/popular-names-by-country-dataset](https://github.com/sigpwned/popular-names-by-country-dataset)
- [ozdemirburak/full-name-generator](https://github.com/ozdemirburak/full-name-generator)
- [openfootmanager README (authored-world philosophy)](https://github.com/openfootmanager/openfootmanager)
