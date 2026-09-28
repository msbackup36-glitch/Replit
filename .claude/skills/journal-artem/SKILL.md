---
name: journal-artem
description: Log and review intraday trend-day examples, false signals and trades for the "Artem Trade" setup (a stock that opens near one extreme and grinds to the other). Use when the user says "journal-artem", shares a chart screenshot of a trending (or failed) open, reports a trade on this setup, asks to review the trend library, or asks what the examples say about the 10-minute filter.
---

# journal-artem

The journal lives in `journal/artem/`:

| Path | What it is |
|---|---|
| `setup.md` | The living spec: trend-day definition, the 10-minute filter, the next filter, open questions. |
| `entries/` | One Markdown file per example, with front matter. The source of truth. |
| `charts/` | Chart screenshots, named `YYYY-MM-DD_TICKER_direction_tf.png`. |
| `reference/raschke.md` | Linda Raschke's related rules and the TS-scan analysis. |
| `tools/build_index.py` | Rebuilds `index.md` and its summary stats from the entries. |
| `index.md` | Generated. Never edit by hand. |

## Categories

- `trend` is a stock that trended: an example of what the filter should catch.
- `false_signal` is a stock that looked like the setup early and then failed. This is the false-signal library.
- `control` is a stock that was not flagged, kept for comparison.

## Opening types

- `clean_drive`: the day's extreme was set in the first bar or two, then higher lows (long) or lower highs (short) from there.
- `delayed_start`: the first 10 to 15 minutes did something else (a flush, a pop or a sideways box), and the trend began around 09:40 to 10:00.
- `n/a`: for false signals and controls where neither applies.

## Adding an entry

1. Work out the ticker, date, direction and chart timeframe from the chart header and axis. If the direction or date is ambiguous, ask. Don't guess.
2. Copy the screenshot into `charts/` using the naming convention. If the file name exists, append `_2`.
3. Copy `entries/_template.md` to `entries/YYYY-MM-DD_TICKER_direction.md` and fill in what the chart shows:
   - Times are US Eastern, `HH:MM`. Prices come from the chart labels where there is one.
   - Anything read off a screenshot rather than taken from data is approximate. Set `values: chart-read` and don't add more precision than the chart gives.
   - If the chart stops while the trend is still running, set `trend_end_capped: yes`. The time is then where the chart ends, and the index leaves it out of its timing stats.
   - Put the user's own annotations under **Notes (Artem)**, quoted as written. Your observations go under **Observations**.
   - Fill in `first10_*` only from bar data (the TradingView MCP `get-ohlcv` tool, 5m or 2m). Never estimate them from an image. Leave them blank if no data was pulled.
4. For a trade actually taken, also fill in the `trade_*` fields. `trade_r` is the result in R: exit minus entry, divided by entry minus stop, with the sign flipped for shorts.
5. Run `python3 journal/artem/tools/build_index.py` and check that it reports no errors.
6. Commit the entry, chart and regenerated index together, with a message like `journal-artem: add CVS 2026-09-25 long (delayed start)`, then push to the session's working branch.

## Reviewing

When asked to review, run the index builder and read `index.md`. Then answer from the entries:

- Clean-drive vs delayed-start mix, and what each implies for the filter window.
- Typical time of the trend's extreme (when the trend ran out), and how many kept going into the afternoon.
- Which `setup.md` filter conditions the examples pass or fail. Flag any condition that trend examples keep failing, or that false signals keep passing.
- False signals: which condition would have excluded each one.

Keep review findings separate from `setup.md`. Change `setup.md` only when the user agrees a change, and log it in its changelog with the date.

## Rules

- The journal records what happened. Don't back-fill outcomes that weren't observed.
- Never mix up the two meanings of "TS" (see `reference/raschke.md`).
- A sample of cherry-picked winners proves nothing about a filter's hit rate. Say so whenever drawing conclusions from the journal before it holds false signals and controls in proportion.
