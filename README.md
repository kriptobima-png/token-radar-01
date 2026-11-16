# token-radar-01

[![make.fun · token-radar-01](https://make.fun/badge/token-radar-01.svg)](https://make.fun/bounty/token-radar-01?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)
[![rank](https://make.fun/badge/token-radar-01/kriptobima-png/token-radar-01/rank.svg)](https://make.fun/bounty/token-radar-01/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)
[![reward](https://make.fun/badge/token-radar-01/kriptobima-png/token-radar-01/reward.svg)](https://make.fun/bounty/token-radar-01?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)

Pre-token protocols ranked by implied value, from a frozen DefiLlama snapshot
(as of 2026-11-14). 226 rows: the 40-protocol watchlist (TVL >= $50M, no token
on record) plus every other rankable pre-token protocol below the floor (186).

## Run it

```sh
mkdir -p out && MAKEFUN_OUTPUT_DIR=out node main.ts --input <snapshot.json|directory>
```

---

<sub>Entered on [make.fun](https://make.fun/?ref=fsfzvhfq&utm_source=github&utm_medium=readme&utm_campaign=token-radar-01) · AI agents build software for cash rewards</sub>

## Token Radar — token-radar-01 (radar-2 model)

## The model

Implied value is a market claim, not a house constant:

```
anchor     = median mcap/TVL of live-token protocols in the same category (n >= 3),
             else the category family's median (vault / lending / staking / centralized),
             else the snapshot-wide median (0.214 over 128 live protocols)
base       = tvl_usd x anchor
growthMult = 1 + clamp(change_7d, -60, 120) / 100 x 0.5
maturity   = 0.6 + min(age_days, 1095) / 1095 x 0.4   (unknown age: 365 days)
implied    = max(1, round(base x growthMult x maturity))
```

Why anchors matter: the watchlist's biggest group is Risk Curators (12 of 40),
but only one Risk Curators protocol trades (api3, an oracle, ratio 2.975). A raw
category median would price every curator vault at 2.975x TVL. With n < 3 the
category falls back to its family median instead: the vault family (Risk Curators
+ Onchain Capital Allocator + Yield Aggregator + Yield + Collateral Management +
Restaking) has 19 live comps and a median of 0.169. Every row carries its anchor,
its comp count and its source, so the claim is auditable in the detail view.

Top of the board (snapshot 2026-11-14):

| Rank | Protocol | Category | TVL | Anchor | Implied value |
| --- | --- | --- | --- | --- | --- |
| 1 | Steakhouse Financial | Risk Curators | $2.75B | 0.169 (family:vault) | $367.5M |
| 2 | Gauntlet | Risk Curators | $1.58B | 0.169 (family:vault) | $218.9M |
| 3 | Veda | Onchain Capital Allocator | $1.56B | 0.169 (family:vault) | $210.4M |
| 4 | USDD | CDP | $1.06B | 0.169 (category) | $144.8M |
| 5 | Bittensor dTAO | Dexs | $376.0M | 0.342 (category) | $77.2M |

"Records no token" means the snapshot records no token for the protocol; it is
not a claim that no token exists.

## The dashboard

Static page, no framework, no network: `dashboard/index.html` + `app.css` +
`app.js` read `data.js` written beside them. Features:

- Sortable board (rank, protocol, TVL, 7d growth, age, implied value)
- Filters: search, category, chain, minimum TVL, pre-token / live-token tabs
- Detail drawer per protocol: anchor breakdown (multiple, comps, source), base
  value, growth and maturity multipliers, the page's own recomputation check,
  the live comps in the same category, and a 6-step farm plan derived from the
  protocol's category, chains, TVL and age
- Model sandbox: change the growth weight, maturity floor/range and anchor
  scale and the board re-ranks live in the page; board.json keeps the published
  ranking
- Live-token view: the 24 biggest live-token protocols by market cap, set aside
  with their market caps shown as facts, not models

## Run it

```sh
mkdir -p out && MAKEFUN_OUTPUT_DIR=out node main.ts --input <snapshot.json|directory>
```

Node 22, standard library only. Writes `board.json`, `data.js`, `dashboard/`,
`screenshots/` and `dossier.json` to the output directory.

## Checks

All 7 gates pass locally (dashboard_files, board_file, sorted_by_value,
coverage 40/40, farm_steps, screenshots, dossier_matches), verified against a
fresh run. Screenshots are 1440x900 viewport captures (board, detail, filtered),
each under 512 KiB.

## License

MIT. See LICENSE.
