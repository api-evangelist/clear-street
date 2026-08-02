---
name: clearstreet-fundamentals-deep-dive
description: >
  Generate a one-page research brief for a US stock or ETF using the Clear Street API —
  fundamentals (market cap, P/E, EPS, beta, sector, 52-week range), analyst consensus
  (rating + price-target distribution), earnings history (estimate vs actual + surprise %),
  and the latest quarterly income and cash-flow statements. Use when the user asks to
  "research", "deep-dive", "analyze", or "give me a one-pager on" a single ticker. For
  multi-name screens use clearstreet-screener; for an earnings calendar use
  clearstreet-earnings-radar; for free-form questions use clearstreet-omni-research.
---

# Fundamentals Deep-Dive

Build a single-ticker research brief by joining several Clear Street API endpoints into one markdown page: snapshot, analyst consensus, earnings history, and the latest quarterly statements.

## Prerequisites

- Python 3.9+ (standard library only)
- `CLEARSTREET_API_KEY` set in the environment — see the [repo README](../../README.md#authentication)

## Quick start

```bash
python3 scripts/deep_dive.py NVDA
```

## Usage

```text
deep_dive.py SYMBOL [--quarters N] [--json]
```

| Argument | Default | Description |
|---|---|---|
| `SYMBOL` | (required) | Ticker (e.g. `NVDA`) or instrument UUID |
| `--quarters` | `4` | Number of earnings quarters to display |
| `--json` | off | Emit the raw merged JSON instead of markdown |

## Examples

| Ask | Command |
|---|---|
| "Research NVDA for me" | `deep_dive.py NVDA` |
| "Deep-dive on Apple, last 8 quarters" | `deep_dive.py AAPL --quarters 8` |
| "Income statement and cash flow for TSLA" | `deep_dive.py TSLA` |

## Workflow

1. Identify the ticker the user is asking about.
2. Run `python3 scripts/deep_dive.py <SYMBOL>`.
3. Present the markdown brief directly — it's formatted for chat, Slack, or a doc. Show only the fields the brief includes; if you add any instrument-identity details, omit option-only fields (expiry / strike) for stocks and ETFs, and don't surface the internal instrument id (UUID).
4. For free-form follow-ups ("is it a buy?"), chain to `clearstreet-omni-research`.

See [reference.md](reference.md) for endpoint and field details.

## Safety

Read-only. The script issues only `GET` requests and never touches order or position endpoints. Output is informational, not investment advice.
