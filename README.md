# Token Radar — make.fun token-radar-01 entry

A program (Node 22, standard library only) that reads a sealed DefiLlama snapshot of
758 protocols and writes a web dashboard ranking the pre-token ones by what their
token launch would be worth, each with the steps to farm it.

## Run

```sh
mkdir -p out && MAKEFUN_OUTPUT_DIR=out node main.ts --input <snapshot.json>
```

Writes `out/board.json` (178 ranked rows: the whole 40-protocol watchlist plus 138
pre-token protocols below the $50M floor), `out/data.js`, `out/dashboard/`,
`out/screenshots/` and `out/dossier.json`.

## Model (radar-2)

```
implied = round( tvl_usd x 0.25
  x (1 + clamp(change_7d, -60, 120) / 100 x 0.5)
  x (0.6 + min(age_days, 1095) / 1095 x 0.4)
  x (1 + 0.03 x min(chains - 1, 5)) )
```

Age is the time since the protocol was first listed; an unlisted age uses 365 days.
No protocol name is special-cased. Wrapped, staked and bridged products are left out
of the board entirely, not discounted: Bridge, Canonical Bridge, Liquid Staking,
Liquid Restaking, Restaked BTC, exchanges, names carrying wrapped/staked/staking and
names beginning with an exchange's name never rank — a wrapped product of a token that
already trades is not a launch.

Live-token protocols are set aside with their market caps, never mixed into the ranked
board. Every board row carries an 8-step farm plan (action, cost, time) derived from the
protocol's own category, chains, TVL and age.

## Dashboard

Static page, no network: search, category and chain filters, a watchlist-only toggle,
sortable columns, a detail view per protocol with the model's breakdown and its farm
plan, and a model sandbox that re-ranks the board live with your own constants.

## License

MIT — see `LICENSE`.
