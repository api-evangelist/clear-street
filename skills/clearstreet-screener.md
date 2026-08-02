---
name: clearstreet-screener
description: >
  Run a fundamentals-and-analyst-aware stock screener across ~11,000 US instruments.
  Filter by sector, industry, country, market-cap range, P/E (TTM), debt/equity, dividend
  yield, consensus rating (BUY/HOLD/SELL), and price-target upside. Use when the user asks
  "find me stocks where…", "screen for…", or "what names look cheap/undervalued". For a
  single-ticker drill-down, chain to clearstreet-fundamentals-deep-dive.
---

# Screener

Filter the full Clear Street instrument universe by fundamentals and analyst metrics, then return a ranked markdown table.

## Prerequisites

- Python 3.9+ (standard library only)
- `CLEARSTREET_API_KEY` set in the environment — see the [repo README](../../README.md#authentication)

## Quick start

```bash
# Large-cap US tech with consensus BUY and P/E under 30, sorted by upside
python3 scripts/screen.py \
  --sector Technology --country US \
  --market-cap-min 50000000000 --pe-max 30 \
  --rating BUY --sort upside_pct --top 20
```

## Examples

| Ask | Command |
|---|---|
| "US tech, BUY rating, P/E under 25" | `screen.py --sector Technology --country US --rating BUY --pe-max 25` |
| "Large-cap dividend payers, yield > 3%" | `screen.py --market-cap-min 10000000000 --div-yield-min 0.03 --sort div_yield` |
| "Healthcare names with biggest upside" | `screen.py --sector Healthcare --rating BUY --sort upside_pct --top 15` |
| "Small-caps under $2B, P/E under 10" | `screen.py --market-cap-max 2000000000 --pe-max 10 --pe-min 0` |
| "ETFs only" | `screen.py --instrument-type ETF` |

## Common flags

| Flag | Notes |
|---|---|
| `--sector`, `--industry`, `--country`, `--exchange` | Categorical filters |
| `--instrument-type` | `COMMON_STOCK`, `EXCHANGE_TRADED_FUND`, `MUTUAL_FUND` (aliases accepted: `stock`, `etf`, `fund`) |
| `--market-cap-min` / `-max` | Dollars (10B = `10000000000`) |
| `--pe-min` / `-max` | Trailing P/E (TTM) |
| `--div-yield-min` / `-max` | Fraction (`0.02` = 2%) |
| `--rating` | `BUY`, `HOLD`, `SELL`, `STRONG_BUY`, `STRONG_SELL` |
| `--upside-pct-min` / `-max` | (target − price) / price × 100 |
| `--sort FIELD [asc\|desc]` | `market_cap`, `upside_pct`, `pe`, `div_yield`, `price`, `total_ratings`, `name`, `symbol` |
| `--top` | Max rows (default 25) |
| `--cache PATH` | Cache the universe locally for faster repeated screens |

Full flag list: see [reference.md](reference.md).

## Workflow

1. Translate the user's natural-language criteria into flags.
2. Run `screen.py`. For repeated screens in one session, pass `--cache /tmp/cs_universe.json`.
3. Present the ranked markdown table.
4. To drill into a result, chain to `clearstreet-fundamentals-deep-dive <symbol>`.

## Safety

Read-only. The full fetch is ~58 sequential `GET` requests with built-in backoff for rate limits. Output is informational, not investment advice.
