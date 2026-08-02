---
name: clearstreet-borrow-check
description: >
  Check the borrow / short-sale / margin status of a US stock or ETF — easy-to-borrow
  flag, threshold-security and short-prohibited flags, long and short margin rates, PTP
  (publicly-traded partnership) flag, liquidation-only status, and average daily volume —
  and explain each flag in plain English. Use when the user asks "is X hard to borrow",
  "can I short X", "what's the margin rate on X", "is X on the threshold list", or
  "is X a PTP".
---

# Borrow Check

Translate the borrow, margin, and short-sale flags on a US instrument into a plain-English verdict. Useful for short-sellers, options structurers, and margin traders.

## Prerequisites

- Python 3.9+ (standard library only)
- `CLEARSTREET_API_KEY` set in the environment — see the [repo README](../../README.md#authentication)

## Quick start

```bash
python3 scripts/borrow.py NVDA
python3 scripts/borrow.py GME AAPL TSLA   # multiple tickers
```

## Usage

```text
borrow.py SYMBOL [SYMBOL ...] [--json]
```

## Examples

| Ask | Command |
|---|---|
| "Is GME hard to borrow?" | `borrow.py GME` |
| "Can I short TSLA?" | `borrow.py TSLA` |
| "What's the margin rate on NVDA?" | `borrow.py NVDA` |
| "Is BX a PTP?" | `borrow.py BX` |
| "Borrow status for NVDA, AMD, AVGO" | `borrow.py NVDA AMD AVGO` |

## Output

Per symbol: a one-line verdict, a flag table, and plain-English notes on each non-default flag.

## Workflow

1. Run `python3 scripts/borrow.py <SYMBOL>...`.
2. Present the output as-is — it's already explanatory. Don't add an instrument-identity table with option-only fields (expiry / strike) or the internal instrument id.
3. For company context, chain to `clearstreet-fundamentals-deep-dive` or `clearstreet-omni-research`.

See [reference.md](reference.md) for the full field list and what each flag means.

## Safety

Read-only. Does not place trades or recommend positions — it reports the operational and regulatory state of an instrument. Flags can change intraday. Output is informational, not investment advice.
