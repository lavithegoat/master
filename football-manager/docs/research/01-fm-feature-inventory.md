# 01. FM feature inventory

This is a scope checklist of the systems a player expects from an FM-style game. Each system has a priority for our build. The priorities are my judgement, not sourced facts.

- **P0**: needed for the first playable season loop.
- **P1**: needed for the game to feel like FM.
- **P2**: depth and polish.
- **P3**: optional, or later.

## Current state of the real series

| Fact | Tag |
|---|---|
| FM25 was cancelled in Feb 2025. SI said they were "too far away from the standards you deserve" after moving to Unity | [Certain] |
| FM26 launched 4 Nov 2025 on Unity, with a Premier League licence and women's football (36,000+ players, 4,000+ clubs) | [Certain] |
| FM26's match engine, tactics and player decision-making are still SI's proprietary code. Unity handles presentation | [Certain] |
| FM26 replaced the sidebar with a top navigation bar, a "Portal" hub and a tile/card system | [Certain] |
| FM26 dropped to about 23% positive on Steam in its first 24 h. In 36k reviews, UI was cited in 34% of negatives and bugs in 33% | [Certain] |
| Even negative reviews rate the new match engine well | [Certain] |
| FM26 launched without: touchline shouts, Create-a-Club, International Management, Challenge Mode, social media screen, Versus mode, Fantasy Draft, cup draws, deadline day | [Certain] |
| FM27 is due November 2026. Top menus stay, the sidebar toggle returns, and it adds a "Continue Flow", a Tactical Effectiveness system, cup draws and deadline day | [Certain] |
| FM26 database: 750,000+ people, 58 playable nations, 156 playable leagues | [Certain] |

### What the FM26 reviews tell us (FM Scout, all 36,004 reviews, read directly) [Certain]

| Complaint (negative reviews, all languages) | Share |
|---|---|
| Interface and navigation | 34.0% |
| Bugs and crashes | 32.9% |
| "Worse than older FM games" | 17.8% |
| Missing features | 8.8% |
| Match engine | 8.3% |

- Between launch and June 2026, patches cut complaints about missing features by 4.9 points, the match engine by 4.0 and bugs by 2.9. **Interface complaints fell only 1.3 points.**
- **19.5% of players who *recommend* FM26 still complain about the interface** (23.8% in English reviews).
- The specific UI complaints: too much empty space, lost information density, missing match analytics (pass networks, heat maps), and a controller-first design that feels wrong on desktop.

### What FM27 changes, and so what "fixed" looks like (FM Scout, read directly) [Certain]
- A top menu (Portal, Squad, Tactics, Recruitment, Training, Club), a control panel on each screen, the toggle button back, **editable keyboard shortcuts**, named bookmarks.
- **Three scaling modes (Default, Detailed, Condensed)** and ultrawide support.
- **Continue Flow modes:** Streamlined, Balanced, Comprehensive, Custom, which control how often the game stops you.
- Cup draws (one at a time, all at once, or automatic), deadline day with agent offers building through the window.
- Heat maps back, an expanded Data Hub, full-screen career history, an attribute polygon, a Competitions Hub.
- Smarter markets: young players prioritise game time, AI values dressing-room leadership, **Squad Cost Ratio rules globally**.
- **Tactical Effectiveness:** Tactical Understanding, Player Ability, Player Buy-In and Next Match Info, with each tactic rated for Intensity, Complexity and Effectiveness.
- Not returning: end-of-season review, player data export.

**Design rules for our UI, derived from the above:** information-dense by default, with a "condensed" density option. Keyboard shortcuts from v1. No screen reachable only through a hub. Match analytics (heat maps, pass networks, xG timeline) count as core, not extras. A configurable Continue Flow. Desktop mouse and keyboard first.

**Takeaway.** The series' own history shows that removing expected features and making navigation harder is punished harder than a weaker engine [Likely]. Our UI must stay dense and keyboard-friendly, with fast table navigation. FM24's flow is the model.

## System inventory

### Career and game shell
| System | Priority | Notes |
|---|---|---|
| New game: pick nations/leagues, database size, manager profile | P0 | FM has playable, view-only and inactive leagues [Certain] |
| Time advance: "Continue", with stop events | P0 | The core loop |
| Inbox/news: messages with actions | P0 | Main UI for events |
| Save/load, autosave | P0 | |
| Manager profile: reputation, coaching badges, preferred style | P1 | |
| Job market: sacked, applying, interviews | P1 | |
| International management | P2 | Cut from FM26, returning in FM27 [Certain] |
| Create-a-Club | P2 | |
| Challenge modes, Versus, Fantasy Draft | P3 | |
| In-game and pre-game database editor | P2 | High value for a fictional-data game |

### Squad and players
| System | Priority | Notes |
|---|---|---|
| Player profile: attributes, positions, personality, contract, history | P0 | See 02 |
| Squad list, filters, custom views | P0 | Heaviest UI surface |
| Squad status: key player, rotation, backup, prospect | P1 | Drives happiness |
| Player interactions: praise, criticise, promises | P1 | |
| Dynamics: hierarchy, social groups, morale, happiness | P1 | See 04 |
| Mentoring groups | P2 | |
| Player traits (preferred moves) | P1 | |
| Individual position/role/trait training | P1 | |

### Tactics
| System | Priority | Notes |
|---|---|---|
| Formation plus 11 positions | P0 | |
| **IP and OOP shapes and roles** (FM26 model) | P1 | P0 needs one shape; the dual shape comes in the tactics stage |
| Mentality and team instructions (in possession, in transition, out of possession) | P1 | List in 04 |
| Player instructions | P1 | |
| Set-piece creator with set-piece coach | P2 | Added in FM24 [Certain] |
| Tactical familiarity | P1 | |
| Opposition instructions | P2 | |
| Touchline shouts | P2 | Cut from FM26 and not returning. Players missed them [Certain] |
| Tactical Effectiveness analysis | P2 | New in FM27 [Certain] |

### Match day
| System | Priority | Notes |
|---|---|---|
| Quick-sim result with stats | P0 | |
| Text commentary with key highlights | P1 | |
| 2D top-down match view | P1 | Our "match engine view" |
| 3D match view | P3 | Probably never needed. 2D is the sim-purist favourite [Guessing] |
| Team talks (pre-match, half-time, full-time) | P1 | |
| In-match substitutions, tactic changes | P1 | |
| Match stats, player ratings, heat maps, pass maps, xG | P1 | |
| Analyst reports, opposition scouting | P2 | |

### Transfers and contracts
| System | Priority | Notes |
|---|---|---|
| Transfer windows, bids (fee, instalments, add-ons, sell-on, loans) | P1 | |
| Contract negotiation (wage, length, bonuses, release clauses, squad status) | P1 | |
| Agents, intermediaries, TransferRoom-style marketplace | P2 | FM24 added intermediaries and TransferRoom [Certain] |
| AI clubs buying and selling realistically | P1 | The hardest AI problem in the game [Guessing] |
| Deadline day | P2 | |
| Free agents, released players, retirements | P1 | |

### Staff, training, medical
| System | Priority | Notes |
|---|---|---|
| Staff roles: assistant, coaches, scouts, analysts, physios, sports scientists, DoF, set-piece coach | P1 | |
| Staff attributes (see 04) | P1 | FM26 shows them as words, not numbers [Certain] |
| Training schedules, intensity, unit and individual focus | P1 | |
| Injuries: type, duration, re-injury, risk | P1 | See 02 |
| Medical centre / injury risk | P2 | |

### Scouting and recruitment
| System | Priority | Notes |
|---|---|---|
| Fog of war / attribute masking | P1 | Knowledge levels from none to extensive [Certain] |
| Scout assignments, recruitment focuses | P1 | |
| Scout accuracy tied to Judging Ability/Potential | P1 | A bad scout is biased, not just vague [Certain] |
| Data analysts (stats-based) | P2 | |

### Club, board, finances
| System | Priority | Notes |
|---|---|---|
| Finances: income (matchday, broadcast, commercial, prize money, sales) vs costs (wages, amortisation, facilities) | P1 | |
| Budgets: transfer/wage split | P1 | |
| Board confidence, club vision, objectives | P1 | Required vs desired objectives [Certain] |
| Supporter confidence | P2 | |
| Facilities: training, youth, stadium expansion | P2 | |
| Financial rules (PSR / Squad Cost Ratio) | P2 | See 04 |
| Affiliate clubs | P3 | |

### Youth
| System | Priority | Notes |
|---|---|---|
| Annual youth intake ("newgens") | P0 | Needed to sustain the world past about 10 seasons |
| Youth facilities, junior coaching, youth recruitment, nation youth rating | P1 | See 04 |
| U18/U21/B teams, loans for development | P2 | |

### Competitions and world
| System | Priority | Notes |
|---|---|---|
| Leagues with promotion/relegation, play-offs | P0 | |
| Domestic cups (knockout, replays, extra time, penalties) | P0 | |
| Continental competitions (Swiss league phase) | P1 | See 04 |
| International football (qualifiers, tournaments) | P2 | |
| Cup draws shown to the user | P2 | |
| Awards, records, club history, hall of fame | P2 | Missed in FM26 [Certain] |
| Reputation systems (club, league, nation coefficients) | P1 | |

### Media
| System | Priority | Notes |
|---|---|---|
| News generation | P1 | |
| Press conferences | P2 | |
| Social media feed | P3 | Dropped by FM26 [Certain] |

## Sources
- [FM Scout: FM27 new features guide](https://www.fmscout.com/a-football-manager-2027-new-features.html)
- [PC Gamer: FM25 cancellation](https://www.pcgamer.com/games/sports/sports-interactive-announces-the-shock-cancellation-of-football-manager-25-but-fans-are-weirdly-pleased-better-to-be-honest-and-admit-a-mistake/)
- [SI statement on FM25](https://www.sports-interactive.com/news/message-sports-interactive-update-football-manager-25)
- [GamingBolt: FM26 what's new](https://gamingbolt.com/football-manager-26-whats-new-whats-changed-and-what-to-expect)
- [Ingenuity Fantasy: FM26 move to Unity](https://ingenuityfantasy.com/feature-articles/football-manager-2026-move-to-the-unity-engine/)
- [G2A: FM26 vs FM24](https://www.g2a.com/news/features/fm26-vs-fm24-the-core-changes-that-impact-your-tactics-scouting-and-matchday/)
- [PC Gamer: FM26 Steam reviews](https://www.pcgamer.com/games/sim/football-manager-26-launches-straight-into-a-relegation-battle-as-steam-reviews-plummet-to-mostly-negative-been-playing-since-1993-and-this-is-the-worst-one/)
- [FM Scout: What went wrong with FM26 (36k reviews)](https://www.fmscout.com/a-fm26-reviews-what-went-wrong.html)
- [Football Gaming Zone: every feature removed from FM26](https://footballgamingzone.com/football-manager/football-manager-26-every-removed-feature/)
- [FM Scout: FM27 new features](https://www.fmscout.com/a-football-manager-2027-new-features.html)
- [UnGeek: FM27 launches November 2026](https://www.ungeek.ph/2026/09/football-manager-27-launches-in-november-2026/)
- [Passion4FM: FM26 database size](https://www.passion4fm.com/how-to-set-up-your-database-size-in-football-manager/)
- [SEGA support: playable leagues FM26](https://support.sega.com/hc/en-us/articles/31629731464593-Which-leagues-are-playable-and-how-can-I-add-them-to-my-Football-Manager-26-save-game)
- [FootballManagerBlog: FM24 set pieces](https://www.footballmanagerblog.org/2023/09/fm24-revamped-set-pieces-and-set-piece-coaches.html)
- [RealSport101: FM24 new features](https://realsport101.com/article/football-manager-2024-new-features)
- [FM Scout: FM26 staff attributes as words](https://www.fmscout.com/q-34043-Staff-attributes-in-FM26.html)
