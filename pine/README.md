# PM Gapper Scan

`pm_gapper_scan.pine` — pre-market mover qualifier for US stocks and ETFs.

All conditions are evaluated live during the pre-market session:

| Condition | Long | Short |
|---|---|---|
| Move from the 04:00 PM open | >= +3% | <= -3% |
| Price vs VWAP anchored at the same 04:00 open | above | below |
| Price vs prior day RTH high / low | above high | below low |
| Price | >= $1 | >= $1 |
| Market cap | >= $500M | >= $500M |
| Avg volume, 10d, regular session | >= 1M shares | >= 1M shares |
| Pre-market volume | >= 50K | >= 50K |

Both the move and the VWAP are anchored at the 04:00 pre-market open, so the
overnight and after-hours move is excluded by construction. This measures what
is happening in the pre-market session itself.

Set `Direction` to Short to mirror every condition at once.

## Requirements

- Intraday timeframe (1m-5m). The script raises a runtime error on daily or higher.
- Extended Hours enabled on the chart, or there is no pre-market to scan.
- A plan with real-time extended-hours data, or the 04:00 bars will be missing.

Check the `Anchor` row of the condition table reads `04:00`. If it does not,
extended hours is off and the VWAP is wrong.

## Reading it

The condition table lists every test with PASS/fail separately, so a symbol
that does not qualify tells you which condition it failed. The background
shades whenever all conditions hold at once, and the `PM` label marks the first
bar that qualified.

## Why this is a chart indicator and not a screener

The built-in TradingView screener compares a field to a constant, never to
another field. `price > VWAP` and `price > prior day high` are both field-to-
field comparisons, and VWAP is not a built-in screener column at all. So the
built-in screener does the coarse net it can express (price, average volume,
market cap, pre-market volume, pre-market change from open) and this script
applies the rest.
