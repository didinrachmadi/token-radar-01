![Token Radar](https://make.fun/badge/token-radar-01.svg)
![Rank](https://make.fun/badge/token-radar-01/didinrachmadi/token-radar-01/rank.svg)
![Reward](https://make.fun/badge/token-radar-01/didinrachmadi/token-radar-01/reward.svg)

# Token Radar

Which launch is worth farming next? Token Radar ranks every pre-token protocol in the
[make.fun token-radar-01](https://make.fun/c/token-radar-01) snapshot by the implied value of
a launch that has not happened yet — and hands each one a concrete farm plan.

## What it ranks

- **The watchlist, covered whole:** all 40 protocols with at least $50M of TVL and no token
  on record.
- **The early radar:** every other rankable pre-token protocol in the snapshot, at any size —
  156 more rows. The watchlist is the floor, never the ceiling.
- **Set aside, with reasons:** 146 pre-token records that are not launches — exchanges,
  bridges (cross-chain included), staked products, wrappers and versioned pools — shown in
  their own view, not ranked.
- **Live tokens apart:** the 24 largest protocols whose token already trades, shown with
  their market caps. A market cap is a fact; implied value is a model, and the two are
  never mixed.

196 rows in total. The accounting is exact: 342 pre-token protocols = 196 ranked + 146 set
aside.

## The model (radar-2)

One function scores every row, from the snapshot figures alone. No protocol name is
special-cased, and no row is hand-picked.

```
implied = round( tvl_usd × 0.25
               × (1 + clamp(change_7d, −60, +150) / 100 × 0.6)
               × (0.6 + min(age_days, 1095) / 1095 × 0.4)
               × (1 + min(chains, 8) × 0.02) )
```

- **TVL base.** A launch is worth a quarter of the TVL the protocol holds today.
- **Growth.** Seven-day TVL change, clamped, moves the value by up to 60% of itself.
- **Maturity.** Age since first listing — the best record of how long a protocol has run
  with no token — scales the value from 0.6 to 1 over three years.
- **Breadth.** Each chain the protocol runs on adds 2%, to eight chains: a wider launch
  reaches more farmers.

Implied value is this entry's own model, never a price, and never a promise that a token
comes. "No token" means the record carries none.

## The dashboard

`dashboard/` is a single page with no network and no framework:

- a sortable, filterable board (search, category, chain, watchlist-only) with a value bar
  per row;
- a detail view per protocol: the model's factors one by one, the page recomputing the
  row's value and checking it against the board;
- a seven-step farm plan per protocol — capital tiered by TVL, hold tiered by age,
  chain-aware gas, a second chain or a second action, a weekly cadence, a safe-claim step
  and a farm log;
- a model sandbox: move the base multiple, the growth weight or the maturity floor and
  the board re-scores live;
- live-token and set-aside views, so the reader can see what was separated and why.

## Run it

Node 22, standard library only, deterministic — the same snapshot always writes the same
bytes.

```sh
mkdir -p out
MAKEFUN_OUTPUT_DIR=out node main.ts --input <kit>/input/snapshot.json
```

`main.ts` reads the sealed snapshot, writes `board.json` and `data.js`, copies
`dashboard/` and `screenshots/` into the output, and writes `dossier.json` last via the
vendored make.fun library (`vendor/makefun-board.ts`, copied unchanged from the kit).

## Layout

```
main.ts            entrypoint: snapshot in, /output out
lib/model.ts       the radar-2 model, the board, the farm plans
dashboard/         the page: index.html, app.css, app.js
screenshots/       1-board.png (cover), 2-detail.png, 3-filtered.png
vendor/            makefun-board.ts, the kit's dossier library, unchanged
```

## Data

The snapshot is a frozen DefiLlama protocol fetch (`as_of` 2026-11-14): 758 protocols at
or above $5M TVL, floor $50M, watchlist 40. Every figure on the page comes from it.

## License

MIT — see `LICENSE`.
