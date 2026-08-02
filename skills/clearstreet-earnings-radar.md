---
name: clearstreet-earnings-radar
description: >
  Pull a forward-looking earnings calendar — every US-listed company reporting between two
  dates, with EPS and revenue estimates, optionally annotated with each name's prior-quarter
  surprise. Use when the user asks "who reports this week?", "earnings calendar for [dates]",
  or "any big prints tomorrow?". For a single-ticker drill-down, chain to
  clearstreet-fundamentals-deep-dive; for analyst views, use clearstreet-omni-research.
---

# Earnings Radar

Show a forward-looking earnings calendar, ranked per day by estimated revenue, optionally annotated with each company's prior-quarter surprise.

## Prerequisites

- Python 3.9+ (standard library only)
- `CLEARSTREET_API_KEY` set in the environment — see the [repo README](../../README.md#authentication)

## Quick start

```bash
# Reporting today through Friday, US-listed, top 30 by estimated revenue
python3 scripts/radar.py --days 5 --currency USD --top 30
```

## Usage

```text
radar.py [--days N | --from YYYY-MM-DD --to YYYY-MM-DD]
         [--currency USD] [--top N] [--filter-symbols S1,S2]
         [--include-prior-surprise] [--json]
```

| Flag | Default | Description |
|---|---|---|
| `--days` | `5` | Forward window from today |
| `--from` / `--to` | — | Explicit date window (overrides `--days`) |
| `--currency` | (none) | Filter by reporting currency (e.g. `USD`) |
| `--top` | `40` | Max rows per date, ranked by revenue estimate |
| `--filter-symbols` | (none) | Comma-separated ticker whitelist |
| `--include-prior-surprise` | off | Add last quarter's EPS/revenue surprise (one extra call per row) |
| `--json` | off | Raw JSON output |

## Examples

| Ask | Command |
|---|---|
| "Who reports this week?" | `radar.py --days 5 --currency USD` |
| "Earnings calendar next 14 days" | `radar.py --days 14` |
| "Big prints tomorrow?" | `radar.py --from 2026-06-18 --to 2026-06-18 --top 20` |

## Workflow

1. Translate the user's date phrase into a `--days` value or a `--from`/`--to` window.
2. Run `radar.py`.
3. Present the returned markdown.
4. For a single-ticker drill-down, chain to `clearstreet-fundamentals-deep-dive`.

See [reference.md](reference.md) for endpoint and field details.

## Safety

Read-only. Estimates are consensus figures and subject to change. Output is informational, not investment advice.
