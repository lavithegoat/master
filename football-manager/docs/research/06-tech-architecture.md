# 06. Technical architecture

## 6.1 What the game actually demands

- **Simulation throughput.** FM26's database has 750k+ people across 156 playable leagues [Certain]. We don't need that at launch, but the engine must handle about 50k–100k players and dozens of leagues, and simulate a season in seconds to low minutes [Guessing: target, not sourced].
- **Data-dense UI.** Most screen time is on sortable, filterable tables, profiles and inbox, not on the pitch. FM26's failure was a UI failure [Certain].
- **Determinism and testability.** Saves reproduce exactly from seed + inputs, and seasons can be simulated headlessly in CI.
- **Long-lived saves.** Saves 20+ seasons in can grow large, and a schema version plus migrations are needed from day one.
- **Buildable and testable from cloud sessions,** because that is how we are building it. Chromium and Playwright are available here, and Unity is not.

### Commercial implications (added after confirming the plan is to sell)
- **Steam is the realistic storefront for a PC management sim**, so a desktop build is the shipping target [Likely]. FM27 ships on Steam, Epic and the Xbox app [Certain]. We still develop and test in the browser, because that is fastest here. The engine/UI split means the Tauri build adds no extra game logic.
- **Tauri over Electron for shipping:** smaller downloads and lower memory use [Certain per 2026 framework comparisons], and both are MIT/Apache licensed [Likely].
- **Every dependency needs a licence check.** No GPL/AGPL in shipped code (see 05 §5.3). Add a CI licence scanner in Stage 1.
- **A web demo is a marketing asset.** A browser build of an early season can serve as a playable demo or itch.io page without extra work [Guessing about marketing value].
- **Mobile is P3.** FM's mobile edition is now Netflix-only in FM27 [Certain]. Touch UI for a data-dense game is a separate design project.

## 6.2 Options considered

| Option | Verdict | Why |
|---|---|---|
| **TypeScript monorepo: pure engine + React UI in the browser, Tauri desktop later** | **Recommended** | One language end to end. The engine runs in a Web Worker, so the UI never blocks. I can run, test and screenshot it in this environment. ZenGM (Basketball GM etc.) shows that a full sports-management sim works client-side, with the core in a Shared/Web Worker and IndexedDB behind an in-memory cache [Certain] |
| Rust core (native or WASM) + Tauri + React/TS UI | Strong runner-up, and a fallback for hot paths | openfootmanager and open-football chose Rust for sim speed [Certain]. The cost is two languages, FFI/serialization boundaries, and slower iteration on an UI-heavy game. We keep the engine free of UI dependencies, so profiled hot loops (the match engine) can move to Rust→WASM later without touching the UI |
| Unity (C#) | Rejected | Strong at 3D rendering, which we don't need. Weak for dense data tables. Not buildable here. FM26 used Unity only for presentation and still failed on UI [Certain] |
| Godot | Rejected | Great for 2D games, but its UI suits game HUDs, not spreadsheets of players [Likely] |
| Electron | Rejected in favour of Tauri | Tauri has smaller bundles and uses the system webview [Certain], and ships SQLite via plugin [Likely]. Electron is the fallback if webview inconsistencies bite |
| Server/online backend | Rejected for now | FM is single-player. A server adds cost and ops for no gameplay gain. Revisit only for online modes (P3) |

## 6.3 Recommended architecture

```
football-manager/
  packages/
    engine/        # pure TS, zero DOM/React deps, deterministic
      rng/         # seeded PRNG + hashSeed(streams)
      model/       # types: Nation, Club, Player, Staff, Competition, Contract...
      world/       # world generation (nations, names, clubs, players)
      competition/ # fixtures, tables, tie-breakers, draws, season rollover
      match/       # tier1 (score model), tier2 (quick engine), tier3 (spatial)
      lifecycle/   # development, ageing, injuries, retirement, youth intake
      market/      # valuation, AI squad needs, transfers, contracts
      club/        # finances, board, staff, training, dynamics
      calendar/    # game clock, scheduled jobs, "continue" loop
      save/        # serialization, schema versioning, migrations
    sim-cli/       # headless: generate world, sim N seasons, print stats/calibration
    ui/            # React app; talks to engine via worker messages
    desktop/       # Tauri wrapper (later)
  data/            # bundled CC0 data: name lists, nation profiles, league templates
  docs/
```

**Principles**
1. **The engine is a library, not an app.** UI and CLI are both clients. The same engine code runs in CI, in a Worker and in Tauri.
2. **Determinism.** No `Math.random`, no `Date.now()` inside the engine. Every stochastic process draws from a named RNG stream (`hashSeed(worldSeed, "match", season, round, home, away)`) [pattern: Certain, from the TS reference repo]. The test: sim seed 7 twice and compare byte-identical output.
3. **Data-oriented storage.** The live world sits in memory as typed arrays or struct-of-arrays for hot data (attributes, fitness, morale). History (old seasons' stats, match logs) lives in the persistence layer and loads on demand. This is the same split ZenGM makes with an in-memory cache over IndexedDB [Certain].
4. **Persistence:** browser uses IndexedDB, desktop uses SQLite through Tauri. Both sit behind one `Storage` interface. Saves carry a `schemaVersion`, and migrations are forward-only.
5. **Event-driven time.** A calendar holds scheduled jobs (matchdays, window open/close, youth intake, contract expiries, board reviews). "Continue" runs jobs until the next *user-stop* event. The inbox is just the user-facing projection of events.
6. **Level of detail.** Tier 1/2/3 match engines (see 03). Player-level processing (weekly development, morale) runs fully for playable leagues and in coarse batches for view-only ones.
7. **Worker boundary.** The UI sends commands (`continue`, `makeBid`, `setTactic`) and receives view-models. The UI never mutates world state directly. This also gives an audit log of user actions for free.

**Tooling (proposed)**: pnpm workspaces, TypeScript strict mode, Vitest for unit and property tests, Playwright for UI smoke tests, ESLint and Prettier, Vite for the UI, and React with TanStack Table for the data grids.

## 6.4 Reference implementations to study, not copy

| Repo | Why look | Licence caution |
|---|---|---|
| [openfootmanager](https://github.com/openfootmanager/openfootmanager) | Closest feature scope (squad, transfers, contracts, training, inbox, scouting, i18n). Shows which pieces teams under-build (issue #607: ratings never computed) | **GPLv3**: read for ideas only [Likely] |
| [open-football](https://github.com/ZOXEXIVO/open-football) | Engine module split, calibration module, separate database repo | Apache-2.0: reusable with notice |
| [zengm](https://github.com/zengm-games/zengm) | Proven client-side sim architecture (worker + IndexedDB + cache + React) | **Not open source.** Contributor licence required [Certain]. Ideas only |
| [footballSimulationEngine](https://github.com/GallagherAiden/footballSimulationEngine) | A small spatial iteration loop and JSON contract | MIT |
| [JorgeQuijano/football-manager](https://github.com/JorgeQuijano/football-manager) | Deterministic seeded TS engine, pure functions, headless `npm run sim` | No licence stated, so treat as all rights reserved |
| [Bygfoot](https://sourceforge.net/projects/bygfoot/) | A long-lived simple manager with country and team definition files | GPLv2 |

## Sources
- [ZenGM architecture (README)](https://github.com/zengm-games/zengm)
- [PkgPulse: desktop frameworks 2026](https://www.pkgpulse.com/guides/best-desktop-app-frameworks-2026)
- [DEV: Unity vs Unreal vs Godot 2026](https://dev.to/oceanviewgames/which-game-engine-should-you-use-unity-vs-unreal-vs-godot-2026-4ndj)
- [openfootmanager](https://github.com/openfootmanager/openfootmanager)
- [open-football](https://github.com/ZOXEXIVO/open-football)
- [Bygfoot (FSF directory)](https://directory.fsf.org/wiki/Bygfoot_Football_Manager)
