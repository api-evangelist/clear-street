---
name: clearstreet-omni-research
description: >
  Send a prompt to Omni, Clear Street's financial AI copilot, and return the answer.
  Omni can draw on market data, fundamentals, analyst consensus, earnings, news, and —
  when entitled — the user's own positions and orders. Two modes: Instant for fast answers,
  Deep Insights for more reasoned analysis. Use for free-form questions with market context
  — "ask Omni…", "what does Omni think about…", "compare X vs Y", "summarize today's market".
  For structured single-ticker output use clearstreet-fundamentals-deep-dive; for filtered
  lists use clearstreet-screener.
---

# Omni Research

Ask Omni a question and get the answer back in the terminal. The script creates a thread, polls until the response completes, and prints Omni's markdown.

## Prerequisites

- Python 3.9+ (standard library only)
- `CLEARSTREET_API_KEY` set in the environment — see the [repo README](../../README.md#authentication)
- For portfolio-aware questions, the API key needs an `omni.account_data` entitlement on a trading account. Without it, Omni answers general market questions but can't see your positions or orders.

## Quick start

```bash
# Fast answer
python3 scripts/ask_omni.py "What is NVDA's forward P/E and analyst consensus?"

# Deeper single-ticker analysis (long-running, ~5 min) — only when explicitly requested
python3 scripts/ask_omni.py --deep --ticker NVDA "Give a thorough qualitative view on NVDA."

# Show Omni's reasoning trace
python3 scripts/ask_omni.py --thinking "Top 5 tech stocks reporting earnings this week"
```

## Usage

```text
ask_omni.py [--deep --ticker SYMBOL] [--thinking] [--thread THREAD_ID]
            [--account-id ID] [--capabilities CAP1,CAP2] [--timeout SECS] [--json]
            "your question"
```

| Flag | Default | Description |
|---|---|---|
| `prompt` | (required) | Free-form question |
| `--deep` | off | Deep Insights mode — deeper reasoning on **one ticker**. Long-running (~5 min) and requires `--ticker`. Use only when the user explicitly asks for a deep/thorough analysis. |
| `--ticker` | (none) | Ticker the deep analysis targets, e.g. `NVDA` (required with `--deep`) |
| `--thinking` | off | Print Omni's tool-use trace before the answer |
| `--thread` | (none) | Continue an existing thread |
| `--account-id` | env / auto | `CLEARSTREET_ACCOUNT_ID`, else the first account the key sees |
| `--capabilities` | (none) | `PREFILL_ORDER`, `OPEN_CHART`, `OPEN_SCREENER`, `OPEN_ENTITLEMENT_CONSENT` |
| `--timeout` | 120 / 600 | Seconds to wait (120 instant, 600 deep) |
| `--json` | off | Full response payload including structured actions |

## Examples

| Ask | Command |
|---|---|
| "Ask Omni about NVDA's outlook" | `ask_omni.py "What's the outlook for NVDA?"` |
| "Give me a thorough view on AAPL" | `ask_omni.py --deep --ticker AAPL "Thorough view on AAPL for a long-term hold"` |
| "Summarize today's market" | `ask_omni.py "Summarize today's US market — winners, losers, notable names"` |

## Workflow

1. Pass the user's question as the prompt.
2. Default to Instant. Only use `--deep` (with `--ticker SYMBOL`) when the user **explicitly** asks for a deep or thorough analysis — it's a single-ticker, long-running task (~5 min), so tell the user it will take a few minutes before you start it.
3. Print the markdown response and mention the `thread_id` for follow-ups.
4. Replace any `[SYMBOL](ticker:SYMBOL)` links with plain ticker text — those render only in Clear Street apps.

See [reference.md](reference.md) for the thread/response lifecycle and payload shape.

## Safety

This skill does not place orders. Even with `PREFILL_ORDER` in capabilities, Omni only suggests order parameters — execution requires a separate, explicit API call. Output is informational, not investment advice.
