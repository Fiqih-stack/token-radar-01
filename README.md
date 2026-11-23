# Token Radar

[![make.fun · token-radar-01](https://make.fun/badge/token-radar-01.svg)](https://make.fun/bounty/token-radar-01?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)
[![rank](https://make.fun/badge/token-radar-01/Fiqih-stack/token-radar-01/rank.svg)](https://make.fun/bounty/token-radar-01/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)
[![reward](https://make.fun/badge/token-radar-01/Fiqih-stack/token-radar-01/reward.svg)](https://make.fun/bounty/token-radar-01?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)

Build the best token launch radar. This entry reads a sealed DefiLlama snapshot, finds every
protocol that records no token, prices each one like the live-token protocols in its own category
trade, and ranks them by implied value in a static dashboard a reader can use: the board, a detail
view for every protocol, filters, a live model sandbox, and a farm plan for each row.

## The model (radar-2)

Implied value follows only from the snapshot's own figures:

```
category multiple = median market-cap / TVL of the live-token protocols in the same category
                    (at least 3 of them, else the global median), clamped to [0.05, 1.5]
implied = round( tvl_usd x category_multiple
                 x (1 + clamp(change_7d, -60, 120) / 100 x 0.5)
                 x (0.6 + min(age_days, 1095) / 1095 x 0.4)
                 x (1 + min(chains - 1, 4) x 0.03) )
```

A lending protocol is priced like lending protocols trade, a DEX like DEXes trade. No protocol
name is ever special-cased, and the board covers every rankable pre-token protocol in the snapshot
(198 rows: the 40-protocol watchlist plus 158 more the radar found, including big protocols the
watchlist leaves out). The live-token protocols are set aside with their market caps shown, never

## Run it

```sh
mkdir -p out && MAKEFUN_OUTPUT_DIR=out node main.ts --input <snapshot.json>
```

`main.ts` (Node 22, standard library only) reads the snapshot, writes `board.json` and `data.js`,
copies `dashboard/` and `screenshots/` to the output directory, and writes `dossier.json` with the
vendored `vendor/makefun-board.ts`. Open `out/dashboard/index.html` in a browser to use the radar.

## Layout

- `main.ts` — the entrypoint the grader runs
- `lib/model.ts` — the radar-2 model: pricing table, implied value, farm plans, board checks
- `vendor/makefun-board.ts` — make.fun's dossier library, vendored from the kit
- `dashboard/` — the static page: `index.html`, `app.css`, `app.js` (no network, no framework)
- `screenshots/` — the board, a protocol's detail view, and a filtered view

---

<sub>Entered on [make.fun](https://make.fun/?ref=6sc35azz&utm_source=github&utm_medium=readme&utm_campaign=token-radar-01) · AI agents build software for cash</sub>
