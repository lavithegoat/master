# Stage 0 research: foundations for an FM-style football management game

Stage 0 is research only. Nothing here is code yet. These notes pull together what I could find (September 2026) on how Football Manager works, what open-source projects already exist, what data we can legally use, and which technical approach to take.

Confidence tags follow the project convention: **[Certain]** means verified from a source listed in the file. **[Likely]** means corroborated or strongly supported but not checked against a primary source. **[Guessing]** means my own inference, flagged so it can be challenged.

## Files

| File | Contents |
|---|---|
| [01-fm-feature-inventory.md](01-fm-feature-inventory.md) | Every system FM has, the FM24 vs FM26 differences, and what FM26's launch teaches us |
| [02-player-model.md](02-player-model.md) | Attributes, hidden and personality attributes, CA/PA, development, ageing, injuries, traits, match ratings |
| [03-match-engine.md](03-match-engine.md) | How FM's engine works, the modelling options, open-source engines, calibration targets, recommended design |
| [04-world-rules-and-management.md](04-world-rules-and-management.md) | Competitions, tie-breakers, squad rules, finances, transfers, youth, staff, board, dynamics, scouting |
| [05-data-and-licensing.md](05-data-and-licensing.md) | Data sources, their licences, and the legal constraints on real names |
| [06-tech-architecture.md](06-tech-architecture.md) | Stack options, the recommended architecture, and reference repos |
| [07-roadmap.md](07-roadmap.md) | Proposed build stages with exit criteria |

## Headline findings

1. **An exact copy of FM is not achievable, and the target needs redefining.** Sports Interactive have built this series since Championship Manager in 1992. They run a research network of 1,300+ scouts to rate players [Certain]. Even with those resources, they cancelled FM25 for missing their quality bar [Certain], and FM26 fell to about 23% positive on Steam within 24 hours of launch [Certain]. A realistic target is **FM's systems at smaller depth**, built so each system can deepen over later stages.
2. **FM24 is a better reference than FM26.** In an analysis of 36,000 FM26 reviews, the UI came up in 34% of negative reviews and bugs in 33% [Certain]. FM26 also launched without International Management, Create-a-Club, touchline shouts, cup draws and deadline day [Certain]. What FM26 got right was its match engine and the **split In Possession / Out of Possession tactics** [Certain]. The plan: use FM24's feature set and screen flow, plus FM26's IP/OOP tactical model.
3. **For a game we intend to sell, the world must be fictional.** Player names need a FIFPRO licence [Certain]. Manchester United sued SEGA over the club name, and FM renamed it "Manchester UFC" in the 2021 settlement [Certain]. Ship fictional clubs, players and competitions, plus an open database format and editor, so users can build real-name databases themselves. We never host or bundle those files.
4. **StatsBomb data is off-limits.** Its user agreement forbids commercially exploiting "any analysis derived" from it [Certain]. Commercially usable replacements: the **Wyscout public dataset** (CC BY 4.0, events for the top 5 leagues) and **SkillCorner open tracking data** (MIT) [Certain].
5. **Two open-source Rust projects are the closest prior art.** One is [openfootmanager](https://github.com/openfootmanager/openfootmanager): Rust, Tauri, React and SQLite, GPLv3, 1.1k stars, still pre-release [Certain]. The other is [open-football](https://github.com/ZOXEXIVO/open-football): pure Rust with a web UI, Apache-2.0 [Certain]. The GPLv3 licence means we can study openfootmanager but cannot copy its code into a project under a different licence [Likely].
6. **FM runs two match engines, and we should too.** Leagues the user plays in get the full engine. Background leagues run on a quick engine [Certain]. Build the quick statistical engine first, because the whole simulated world depends on it. The spatial 2D engine comes later and gets calibrated against the same statistical targets.
7. **FM's engine has players decide every 0.25 s, and it is attribute-driven** [Certain]. Players make a decision every quarter-second "slice" and can change a decision mid-slice. That gives a concrete tick rate for our spatial engine.

## Recommended stack (details in 06)

**TypeScript monorepo.** A pure, deterministic, headless `engine` package runs in a Web Worker. A React UI sits on top, and a Tauri desktop wrapper comes later.

Alternatives I considered and rejected:
- **Unity (what FM26 uses).** Rejected because Unity's strength is 3D rendering, and our hard problems are simulation and data-dense UI. FM26 itself failed on UI, even after moving to Unity. Unity is also impractical to build and test from these cloud sessions.
- **Rust core + Tauri + React (openfootmanager's choice).** Rust is faster, but it means two languages and slower iteration on a game that is roughly 80% UI screens [Guessing]. Keeping the engine free of UI dependencies leaves a route to port hot paths to Rust/WASM if profiling shows we need it.
- **Godot.** Rejected because its UI toolkit suits game HUDs, not sortable, filterable tables of hundreds of players.

## Decisions taken (26 Sep 2026)

- **Goal:** start as a personal project, publish and sell later. Every data and licensing rule in 05 follows a commercial posture from day one, because retrofitting it later means ripping out tuned engine parameters.
- **Ship target:** desktop (Tauri, Steam). Dev loop: browser.
- **Open item:** move the project to a dedicated private repository before Stage 1 code lands. The current repo is public and shared with an unrelated project (05 §5.4).

## Verification status

The network block is lifted. The attribute lists, hidden attributes, personality thresholds, staff attributes, FM26 team instructions and roles, youth intake mechanics, FM26 review breakdown and FM27 feature list have been checked against primary pages. The SI manual (community.sports-interactive.com) and figshare still return HTTP 403 to automated fetches. That comes from those sites, not our network policy.
